# terraform-code-reviewer MCP 移行計画

日時: 2026-05-14
関連調査: `.claude/docs/research/2026-05-13-terraform-mcp-migration-research.md`

## 概要

`awslabs.terraform-mcp-server` が全バージョン yanked となり起動不能なため、MCP 依存を刷新する。
Docker/Go バイナリ不要の `uvx` ベース MCP に置き換え、「AWS 公式ドキュメントを参照しながらコードレビューを行うエージェント」として再定義する。

## アプローチ

Terraform 専用 MCP を廃止し、`document-reviewer` と同じ `aws-documentation-mcp-server` + `aws-knowledge-mcp-server` を採用する。

- `uvx awslabs.aws-documentation-mcp-server@latest` は既存プラグインで実績あり
- AWS プロバイダーの公式ドキュメントは AWS Documentation MCP 経由で参照可能
- Terraform 固有のレジストリ検索機能は失われるが、コードレビュー用途では影響軽微

代替案として「MCP なし（モデルの知識のみ）」も検討したが、AWS 公式ドキュメント参照による根拠提示はレビュー品質上重要なため、documentation MCP は維持する。

## 変更内容

### 変更1: MCP 設定ファイルの置き換え

削除: `terraform-code-reviewer/mcps/aws-terraform-mcp-server.json`

追加: `terraform-code-reviewer/mcps/aws-documentation-mcp-server.json`

```json
{
    "mcpServers": {
        "aws-documentation-mcp-server": {
            "command": "uvx",
            "args": ["awslabs.aws-documentation-mcp-server@latest"],
            "env": {
                "FASTMCP_LOG_LEVEL": "ERROR",
                "AWS_DOCUMENTATION_PARTITION": "aws",
                "MCP_USER_AGENT": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36"
            },
            "disabled": false,
            "autoApprove": true
        }
    }
}
```

追加: `terraform-code-reviewer/mcps/aws-knowledge-mcp-server.json`

`document-reviewer/mcps/aws-knowledge-mcp-server.json` と同内容をコピー。

### 変更2: エージェントの tools 更新

対象: `terraform-code-reviewer/agents/terraform-code-reviewer.md`

```yaml
# 変更前
tools: Read, Write, Edit, mcp_aws-terraform-mcp-server

# 変更後
tools: Read, Write, Edit, mcp_aws-documentation-mcp-server, mcp_aws-knowledge-mcp-server
```

エージェント本文の「連携機能 > MCPサーバー連携」セクションも更新:

```markdown
# 変更前
### MCPサーバー連携
- AWS Knowledge MCP: AWS公式ドキュメントとの照合
- AWS Documentation MCP: 最新のAWSサービス情報の取得
- AWS Terraform MCP: Terraformプロバイダー固有の情報取得

# 変更後
### MCPサーバー連携
- AWS Documentation MCP: AWS公式ドキュメントの参照（リソース仕様、プロバイダードキュメント）
- AWS Knowledge MCP: AWS サービスの詳細情報取得
```

### 変更3: marketplace.json 更新

対象: `.claude-plugin/marketplace.json` の terraform-code-reviewer エントリ

```json
// 変更前
"mcpServers": [
    "./mcps/aws-terraform-mcp-server.json"
]

// 変更後
"mcpServers": [
    "./mcps/aws-documentation-mcp-server.json",
    "./mcps/aws-knowledge-mcp-server.json"
]
```

バージョンを `0.0.4` から `0.1.0` に更新。

### 変更4: README.md 更新

対象: `README.md` の terraform-code-reviewer の説明箇所

MCP の記述を `aws-documentation-mcp-server` / `aws-knowledge-mcp-server` に更新し、
「固有 (aws-terraform-mcp-server)」となっているアーキテクチャ表を修正。

## 影響範囲

- Terraform レジストリ検索（プロバイダー・モジュールの最新バージョン検索）は使えなくなる
- コードレビューの主要機能（セキュリティ、ベストプラクティス、コスト最適化の分析）には影響なし
- 既存ユーザーは `aws-terraform-mcp-server` の MCP 接続が切れるため、プラグイン再インストールが必要

## 考慮事項

- `aws-knowledge-mcp-server.json` は `document-reviewer/` の同名ファイルと内容が同一になる。将来的に `aws-shared-mcps` に集約する余地があるが、今回のスコープ外。
- バージョンは `0.1.0` に上げ、変更内容をコミットメッセージで明示する。

## タスクリスト

### Phase 1: ファイル操作
- [x] 1-1: `mcps/aws-terraform-mcp-server.json` を削除する
- [x] 1-2: `document-reviewer/mcps/aws-documentation-mcp-server.json` を `terraform-code-reviewer/mcps/` にコピーして作成する
- [x] 1-3: `document-reviewer/mcps/aws-knowledge-mcp-server.json` を `terraform-code-reviewer/mcps/` にコピーして作成する

### Phase 2: エージェント・マーケットプレイス更新
- [x] 2-1: `agents/terraform-code-reviewer.md` の `tools:` を更新する
- [x] 2-2: `agents/terraform-code-reviewer.md` の MCPサーバー連携セクションを更新する
- [x] 2-3: `marketplace.json` の mcpServers と version を更新する

### Phase 3: ドキュメント更新
- [x] 3-1: `README.md` のアーキテクチャ表と terraform-code-reviewer の MCP 記述を更新する

### Phase 4: Lint 確認
- [x] 4-1: `npx markdownlint-cli "**/*.md" --ignore node_modules` を実行してエラーがないことを確認する
