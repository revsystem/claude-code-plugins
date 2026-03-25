# Claude Code Plugins - ドキュメント・コードレビュープラグイン

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugins-blue)](https://docs.claude.com/en/docs/claude-code/plugins)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-0.0.5-orange.svg)](.claude-plugin/marketplace.json)

技術記事レビュー、Terraformコードレビュー、AWSコスト分析、技術ドキュメント検索を提供するClaude Code Pluginsです。文章校正、AWS公式ドキュメント検証、Terraformコードレビュー、AWSコスト最適化、最新技術情報の調査を組み合わせ、高品質な技術文書とインフラの作成をサポートします。

## プラグイン一覧

| プラグイン | 概要 | 使い方 | モデル |
|-----------|------|--------|-------|
| `document-reviewer` | 技術記事の文章校正とAWS公式ドキュメント検証 | `/document-reviewer @article.md` | inherit |
| `terraform-code-reviewer` | Terraformコードのセキュリティ・コスト・ベストプラクティスレビュー | `/terraform-code-reviewer` | inherit |
| `aws-cost-analyst` | AWSコスト分析・異常調査・最適化提案 | エージェント（自然言語） | sonnet |
| `tech-docs-searcher` | フレームワーク・ライブラリ・AWSサービスの最新ドキュメント検索 | エージェント（自然言語） | sonnet |

`inherit` は Claude Code の実行モデルを継承します。`sonnet` はコスト効率を考慮した明示指定です。

## インストール

### 前提条件
- [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) がインストールされていること
- uvx がインストールされていること（AWS 系 MCP サーバーの起動に使用）
- Node.js / npx がインストールされていること（tech-docs-searcher の Context7 MCP サーバーの起動に使用）
- **aws-cost-analyst を使用する場合**: AWS CLIが設定済みで、Cost Explorer / Pricing API にアクセスできるプロファイルが必要（シェルの `AWS_PROFILE` 環境変数を自動参照）

### インストール手順

#### Githubマーケットプレイスを追加する場合

1. **Githubリポジトリにあるマーケットプレイスを追加する**

```bash
/plugin marketplace add revsystem/claude-code-plugins
```

2. **プラグインをインストールする**

**技術記事レビュープラグイン**

```bash
/plugin install document-reviewer@document-and-code-reviewer
```

**Terraformコードレビュープラグイン**

```bash
/plugin install terraform-code-reviewer@document-and-code-reviewer
```

**AWSコスト分析プラグイン**

```bash
/plugin install aws-cost-analyst@document-and-code-reviewer
```

**技術ドキュメント検索プラグイン**

```bash
/plugin install tech-docs-searcher@document-and-code-reviewer
```

3. **プラグインを有効化する**

```bash
/plugin enable
```

`document-and-code-reviewer` を選択してEnterキーを押します。

```bash
╭─────────────────────────────╮
│ Manage plugins                                           │
│ Select a marketplace to manage plugins:                  │
│                                                          │
│ ❯ document-and-code-reviewer                             │
│   4 plugins installed · 4 disabled                       │
╰─────────────────────────────╯
```

有効にしたいプラグインを選択して `Enable plugin` を選択してEnterキーを押します。

```bash
╭─────────────────────────────╮
│ document-and-code-reviewer › Manage plugins              │
│                                                          │
│ ❯ ◯ document-reviewer                                   │
│     Review blog articles. · v0.0.4                       │
│                                                          │
│   ◯ terraform-code-reviewer                             │
│     Review Terraform code. · v0.0.4                      │
│                                                          │
│   ◯ aws-cost-analyst                                    │
│     AWS cost analysis. · v0.0.4                          │
│                                                          │
│   ◯ tech-docs-searcher                                  │
│     Technical docs search. · v0.0.4                      │
╰─────────────────────────────╯
```

```bash
╭─────────────────────────────╮
│ ❯ Enable plugin                                          │
│   Mark for update                                        │
│   Mark for uninstallation                                │
│   Update now                                             │
│   Uninstall now                                          │
│   Back to plugin list                                    │
╰─────────────────────────────╯
```

#### ローカルマーケットプレイスを追加する場合

1. **リポジトリをクローンする**

```bash
git clone https://github.com/revsystem/claude-code-plugins.git
cd claude-code-plugins
```

2. **ローカルマーケットプレイスとして追加**

```bash
/plugin marketplace add ./claude-code-plugins
```

3. **プラグインをインストール**

**技術記事レビュープラグイン**

```bash
/plugin install document-reviewer@document-and-code-reviewer
```

**Terraformコードレビュープラグイン**

```bash
/plugin install terraform-code-reviewer@document-and-code-reviewer
```

**AWSコスト分析プラグイン**

```bash
/plugin install aws-cost-analyst@document-and-code-reviewer
```

**技術ドキュメント検索プラグイン**

```bash
/plugin install tech-docs-searcher@document-and-code-reviewer
```

4. **プラグインを有効化する**

前述の、Githubマーケットプレイスを追加する場合の手順を参照してください。

### AWS_PROFILE の設定（aws-cost-explorer / aws-pricing MCPサーバー）

AWS Cost Explorer MCPサーバーと AWS Pricing MCPサーバーは AWS API にアクセスするため、`AWS_PROFILE` 環境変数の設定が必要です。

MCPサーバー設定ファイルは `${AWS_PROFILE:-default}` 構文を使用しており、シェル環境の `AWS_PROFILE` を自動的に参照します。未設定の場合は AWS CLI の `default` プロファイルが使用されます。

**シェルで環境変数を設定する**

```bash
# ~/.bashrc や ~/.zshrc に追加
export AWS_PROFILE=your-aws-profile
```

設定するプロファイルには、Cost Explorer API と Pricing API へのアクセス権限が必要です。

**MCPサーバー設定ファイルの場所**（直接編集する場合）

```text
~/.claude/plugins/marketplaces/document-and-code-reviewer/mcps/
├── aws-cost-explorer-mcp-server.json
└── aws-pricing-mcp-server.json
```

## 使用方法

### 1. document-reviewer - 技術記事レビュー

#### 基本的な使用方法

```bash
# 記事全体をレビュー
/document-reviewer @your_article.md
```

```bash
# 特定のセクションをレビュー
/document-reviewer --section "設定手順" @your_article.md
```

```bash
# レビューレベルの指定 (strict/normal/quick)
/document-reviewer --level strict @your_article.md
```

### 2. terraform-code-reviewer - Terraformコードレビュー

#### 基本的な使用方法

```bash
# Terraformコード全体をレビュー
/terraform-code-reviewer
```

```bash
# 特定のファイルをレビュー
/terraform-code-reviewer --file main.tf
```

```bash
# 特定の観点でレビュー (security/cost/performance/best-practices)
/terraform-code-reviewer --focus security
```

```bash
# レビューレベルの指定 (strict/normal/quick)
/terraform-code-reviewer --level strict
```

レビューの目的や観点を指定することで、より詳細なレビューが得られます。

```bash
# レビュー対象のファイルを指定
/terraform-code-reviewer --level strict Terraformのコードをレビューして。ALBのセキュリティレベルを向上させたい。
```

### 3. aws-cost-analyst - AWSコスト分析

aws-cost-analyst はコマンドではなくエージェントとして動作します。自然言語でコスト分析を依頼してください。

```bash
# 既存リソースのコスト分析
先月のAWSコストを分析して、サービス別の内訳を教えて。

# 異常コストの調査
今月のAWSコストが急増している原因を調べて。

# コスト最適化の提案
AWSコストの最適化ポイントを特定して、削減額を試算して。
```

### 4. tech-docs-searcher - 技術ドキュメント検索

tech-docs-searcher はコマンドではなくエージェントとして動作します。自然言語でドキュメント検索を依頼してください。

```bash
# フレームワークの最新情報調査
React 19 の最新機能とベストプラクティスを調べて。

# AWS サービスのドキュメント検索
AWS Lambda の cold start を最小化する方法を調べて。

# API リファレンス調査
Next.js の App Router の最新APIドキュメントを確認して。

# 実装パターンの調査
TypeScript で DDD を実装する際のベストプラクティスを調べて。
```

### 出力例

#### document-reviewer の出力例

```text
📊 レビュー結果サマリー
- 重大な問題: 3件
- 推奨修正: 7件
- 追加提案: 4件
- 総合評価: B+ (良好、一部改善推奨)

🔴 重大な問題
❌ **技術的誤り**: API エンドポイントの記述が不正確
- 現在の記載: "https://api.example.com/v1"
- 正しい情報: "https://api.example.com/v2"
- 参照先: https://docs.aws.amazon.com/...
- 修正理由: 古いバージョンのAPIを使用しているため

🟡 推奨修正
⚠️ **表現の改善**: より読みやすい文章への修正
- 現在の表現: "この機能を使用することにより..."
- 推奨表現: "この機能を使うと..."
- 改善理由: より簡潔で理解しやすい表現
```

#### terraform-code-reviewer の出力例

```text
📊 Terraformコードレビュー結果サマリー
- 重大な問題: 2件
- 推奨修正: 8件
- 追加提案: 5件
- 総合評価: B+ (良好、セキュリティとコスト最適化推奨)

🔴 重大な問題
❌ **セキュリティリスク**: セキュリティグループが0.0.0.0/0に開放
- 現在の設定: ingress { from_port = 22; to_port = 22; cidr_blocks = ["0.0.0.0/0"] }
- 推奨設定: 特定のIPアドレス範囲のみ許可
- 修正理由: セキュリティリスクが高い設定

🟡 推奨修正
⚠️ **コスト最適化**: インスタンスタイプの見直し
- 現在の設定: t3.large (2 vCPU, 8GB RAM)
- 推奨設定: t3.medium (2 vCPU, 4GB RAM)
- 期待効果: 月額コスト約30%削減
```

#### aws-cost-analyst の出力例

```text
📊 コスト分析サマリー
- 分析期間: 2026-01-01 〜 2026-01-31
- 総コスト: $3,245.67
- 前月比: +12.3%

## サービス別コスト内訳
| サービス       | コスト     | 割合  | 前月比  |
|---------------|-----------|-------|--------|
| Amazon EC2    | $1,523.45 | 46.9% | +8.2%  |
| Amazon RDS    | $867.23   | 26.7% | +15.1% |
| Amazon S3     | $234.56   | 7.2%  | +3.4%  |
| その他         | $620.43   | 19.1% | +18.7% |

## コスト最適化の推奨事項
| 推奨事項                          | 推定削減額  | 難易度 | 優先度 |
|----------------------------------|-----------|--------|--------|
| EC2 Savings Plans 適用            | $380/月   | 低     | 高     |
| 未使用EBSボリューム削除             | $45/月    | 低     | 高     |
| RDS インスタンスサイズの見直し       | $120/月   | 中     | 中     |
```

#### tech-docs-searcher の出力例

```text
## 調査対象: AWS Lambda cold start 最適化
### バージョン/更新日
AWS Lambda ドキュメント 2026年1月更新

### 要点
- SnapStart を有効化すると Java ランタイムの cold start を最大90%短縮可能
- Provisioned Concurrency で事前にインスタンスをウォームアップ
- 軽量ランタイム（Python、Node.js）は cold start が短い傾向
- パッケージサイズの最小化が cold start 短縮に直結

### 参照リンク
- https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html
- https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html
```

## アーキテクチャ

### プラグイン構成

```console
claude-code-plugins/
├── .claude-plugin/
│   └── marketplace.json                   # マーケットプレイス設定
├── document-reviewer/                     # 技術記事レビュープラグイン
│   ├── agents/
│   │   ├── article-reviewer.md                # 文章校正エージェント
│   │   ├── official-document-checker.md       # AWS公式ドキュメント検証エージェント
│   │   └── resources/
│   │       └── terminology-standards.md       # 用語規約リソース
│   ├── commands/
│   │   └── document-reviewer.md               # 技術記事統合レビューコマンド
│   └── mcps/
│       ├── aws-documentation-mcp-server.json  # AWS公式ドキュメント検索
│       └── aws-knowledge-mcp-server.json      # AWSナレッジベース検索
├── terraform-code-reviewer/               # Terraformコードレビュープラグイン
│   ├── agents/
│   │   └── terraform-code-reviewer.md         # Terraformコードレビューエージェント
│   ├── commands/
│   │   └── terraform-code-reviewer.md         # Terraformコードレビューコマンド
│   └── mcps/
│       └── aws-terraform-mcp-server.json      # Terraform MCP Server
├── aws-cost-analyst/                      # AWSコスト分析プラグイン
│   ├── agents/
│   │   └── aws-cost-analyst.md                # AWSコスト分析エージェント
│   └── mcps/
│       ├── aws-cost-explorer-mcp-server.json  # AWS Cost Explorer MCP
│       └── aws-pricing-mcp-server.json        # AWS Pricing MCP
└── tech-docs-searcher/                    # 技術ドキュメント検索プラグイン
    ├── agents/
    │   └── tech-docs-searcher.md              # 技術ドキュメント検索エージェント
    └── mcps/
        ├── aws-documentation-mcp-server.json  # AWS公式ドキュメント検索
        ├── aws-knowledge-mcp-server.json      # AWSナレッジベース検索
        └── context7-mcp-server.json           # Context7 ライブラリドキュメント検索
```

> MCP サーバーの重複（`aws-documentation-mcp-server`、`aws-knowledge-mcp-server`）は Claude Code の自動重複排除機能により、ランタイムで1インスタンスのみ起動されます。各プラグインの自己完結性を優先し、意図的に重複を許容しています。

## 設定

### レビューレベル

- **strict**: 最も厳格なレビュー（本格的な公開前）
- **normal**: 標準的なレビュー（日常的な使用）
- **quick**: 簡易レビュー（素早い確認）

### カスタマイズ

各エージェントの設定は、対応するMarkdownファイルを編集することで変更できます：

- `agents/article-reviewer.md`: 文章校正ルールの調整
- `agents/official-document-checker.md`: 技術検証ルールの調整
- `agents/terraform-code-reviewer.md`: Terraformコードレビュールールの調整
- `agents/aws-cost-analyst.md`: AWSコスト分析ルールの調整
- `agents/tech-docs-searcher.md`: 技術ドキュメント検索ルールの調整
- `agents/resources/terminology-standards.md`: 用語規約の調整

## ライセンス

このプロジェクトは [MIT License](LICENSE) の下で公開されています。
