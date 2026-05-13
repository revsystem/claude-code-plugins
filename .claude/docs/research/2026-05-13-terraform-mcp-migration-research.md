# terraform-mcp-server 移行調査レポート

日時: 2026-05-13

## 対象範囲

- `terraform-code-reviewer/mcps/aws-terraform-mcp-server.json`
- `terraform-code-reviewer/agents/terraform-code-reviewer.md`
- `terraform-code-reviewer/commands/terraform-code-reviewer.md`
- `.claude-plugin/marketplace.json`

## 問題の背景

`uvx awslabs.terraform-mcp-server@latest` が全バージョン yanked されており、起動不能。
PyPI の公式理由: "Superseded by https://github.com/hashicorp/terraform-mcp-server"

## awslabs 版が提供していたツール

| ツール | 機能 |
|---|---|
| ExecuteTerraformCommand | ローカル CLI (plan/validate/apply/destroy) |
| RunCheckovScan | セキュリティスキャン |
| SearchAwsProviderDocs | AWS プロバイダードキュメント検索 |
| SearchSpecificAwsIaModules | AWS-IA モジュール検索 |
| SearchUserProvidedModule | GitHub からのモジュール解析 |
| ExecuteTerragruntCommand | Terragrunt 実行 |

## HashiCorp 版の特性

### インストール方法

uvx は使えない（Go 製バイナリのため）。2 つの選択肢がある。

Docker（公式推奨）:
```
docker run -i --rm hashicorp/terraform-mcp-server
```

Go バイナリ（Docker 不要、Go が必要）:
```
go install github.com/hashicorp/terraform-mcp-server/cmd/terraform-mcp-server@latest
terraform-mcp-server stdio
```

### ツールセット

- `registry`: プロバイダー・モジュールの公開レジストリ検索（TFE_TOKEN 不要）
- `registry-private`: プライベートレジストリ（TFE_TOKEN 必要）
- `terraform`: HCP Terraform/Enterprise のワークスペース・実行管理（TFE_TOKEN 必要）

### 提供ツール（registry のみ抜粋）

- `search_providers`, `get_provider_details`, `get_provider_capabilities`
- `get_latest_provider_version`
- `search_modules`, `get_module_details`, `get_latest_module_version`

### awslabs 版からのギャップ

| 機能 | 状況 |
|---|---|
| ローカル terraform CLI 実行 | なし（HCP リモート実行に置き換え） |
| Checkov セキュリティスキャン | なし |
| Terragrunt サポート | なし |
| AWS best practices ガイダンス | なし |
| プロバイダードキュメント検索 | あり（構造化メタデータ） |
| モジュール検索 | あり（レジストリメタデータ） |

## レビュープラグインへの影響評価

terraform-code-reviewer は「コードレビュー」用途なので、ローカル CLI 実行や Checkov は直接使っていない。
エージェントが実際に活用できる HashiCorp ツールは `registry` ツールセット（プロバイダー仕様・モジュール仕様の参照）で、コードレビューの根拠として十分機能する。

Checkov スキャンや `terraform validate` はエージェントの外部連携として記述されているが（agents/terraform-code-reviewer.md の「外部ツール連携」セクション）、MCP 経由では実行していなかったため、実質的な機能劣化はない。

## 必要な変更箇所

1. `mcps/aws-terraform-mcp-server.json` — コマンドを HashiCorp 版に変更（ファイル名も改名）
2. `agents/terraform-code-reviewer.md` の `tools:` — MCP サーバー名を更新
3. `.claude-plugin/marketplace.json` — mcpServers パスを更新（ファイル名変更時）
4. `README.md` — 前提条件（Docker または Go）を追記

## 注意点・リスク

- Docker が必要（または Go によるバイナリインストール）。環境依存が増える。
- MCP サーバー名が変わるため、`mcp_aws-terraform-mcp-server` ツール参照をすべて更新する必要がある。
- TFE_TOKEN は registry 利用のみであれば不要。認証なしで公開レジストリにアクセスできる。
- ファイル名 `aws-terraform-mcp-server.json` は HashiCorp 版に合わせて `terraform-mcp-server.json` に改名するのが自然。
