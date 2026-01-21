# Claude Code Plugins - ドキュメント・コードレビュープラグイン

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugins-blue)](https://docs.claude.com/en/docs/claude-code/plugins)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-0.0.1-orange.svg)](.claude-plugin/marketplace.json)

技術記事とTerraformコードの包括的レビューを提供するClaude Code Pluginsです。文章校正、AWS公式ドキュメント検証、Terraformコードレビューを組み合わせ、高品質な技術文書とコードの作成をサポートします。

## プラグイン一覧

### 1. document-reviewer - 技術記事レビュープラグイン

#### 文章校正エージェント
- **日本語の自然さ**: 読みやすく自然な日本語表現への修正
- **技術用語の統一**: 適切な表記と用語の一貫性確保
- **構成の最適化**: 論理的な文章構造と段落構成の改善
- **Qiita Markdown記法**: Qiitaの記法に準拠した記述

#### AWS公式ドキュメント検証エージェント
- **技術的正確性**: AWS公式ドキュメントとの照合
- **仕様の検証**: エンドポイント、パラメータ、レスポンス形式など仕様の確認
- **サービス名の正確性**: 正式なサービス名と表記の検証
- **料金・制限事項**: 最新の料金体系と制限事項の確認

#### 統合レビューコマンド
- **包括的レビュー**: 2つのエージェントを連携した総合的なレビュー
- **優先度別フィードバック**: 重大・推奨・提案の3段階で整理
- **具体的な修正提案**: 実際の運用で役立つ改善案を提供

### 2. terraform-code-reviewer - Terraformコードレビュープラグイン

#### Terraformコードレビューエージェント
- **セキュリティレビュー**: セキュリティグループ、IAM、暗号化設定の検証
- **ベストプラクティス**: Terraformの公式ガイドラインに基づく検証
- **パフォーマンス最適化**: リソース設定、データソース使用の最適化
- **コスト最適化**: リソースサイズ、ライフサイクル設定の検証

#### 包括的レビューコマンド
- **多角的レビュー**: セキュリティ、パフォーマンス、コスト、ベストプラクティスの4観点
- **段階的改善提案**: 優先度に基づいた実装可能な改善ロードマップ
- **具体的なコード修正**: 実際のコード例を含む修正提案

## プラグインが使用するモデル
Claude Codeと同じモデルを使用します。Sonnet 4.5を推奨します。

## インストール

### 前提条件
- [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) がインストールされていること
- uvxがインストールされていること

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

3. **プラグインを有効化する**
```bash
/plugin enable
```

`document-and-code-reviewer`を選択してEnterキーを押します。
```bash
╭─────────────────────────────╮
│ Manage plugins                                           │
│ Select a marketplace to manage plugins:                  │
│                                                          │
│ ❯ document-and-code-reviewer                             │
│   2 plugins installed · 2 disabled                       │
╰─────────────────────────────╯
```

`document-reviewer`を選択してEnterキーを押します。

```bash
╭─────────────────────────────╮
│ document-and-code-reviewer › Manage plugins              │
│                                                          │
│ ❯ ◯ document-reviewer                                   │
│     Review blog articles. · v0.0.1                       │
│                                                          │
│   ◯ terraform-code-reviewer                             │
│     Review Terraform code. · v0.0.1                      │
╰─────────────────────────────╯
```

`Enable plugin`を選択してEnterキーを押します。

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

同様に`terraform-code-reviewer`も有効化します。

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

4. **プラグインを有効化する**
前述の、Githubマーケットプレイスを追加する場合の手順を参照してください。

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

```

### 出力例

#### document-reviewer の出力例

```
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

```
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

## アーキテクチャ

### プラグイン構成

```console
claude-code-plugins/
├── .claude-plugin/
│   └── marketplace.json          # マーケットプレイス設定
├── agents/
│   ├── article-reviewer.md       # 文章校正エージェント
│   ├── official-document-checker.md # AWS公式ドキュメント検証エージェント
│   └── terraform-code-reviewer.md     # Terraformコードレビューエージェント
├── commands/
│   ├── document-reviewer.md      # 技術記事統合レビューコマンド
│   └── terraform-code-reviewer.md     # Terraformコードレビューコマンド
└── mcps/
    ├── aws-knowledge-mcp-server.json
    ├── aws-documentation-mcp-server.json
    └── aws-terraform-mcp-server.json
```

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

## 対応技術

### document-reviewer
#### 文章校正
- 日本語の自然さと読みやすさ
- 技術用語の適切な表記
- Qiita Markdown記法
- 文章構造と論理性

#### AWS公式ドキュメント検証
- AWS サービス名とAPI仕様
- 料金情報と制限事項
- セキュリティ設定
- 最新の推奨事項

### terraform-code-reviewer
#### Terraformコードレビュー
- セキュリティ設定（セキュリティグループ、IAM、暗号化）
- ベストプラクティス（命名規則、モジュール化、状態管理）
- パフォーマンス最適化（リソースサイズ、データソース使用）
- コスト最適化（ライフサイクル設定、リザーブドインスタンス）

## ライセンス

このプロジェクトは [MIT License](LICENSE) の下で公開されています。

