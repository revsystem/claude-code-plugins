# Claude Code Plugins - 技術記事レビューシステム

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Plugins-blue)](https://docs.claude.com/en/docs/claude-code/plugins)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-0.0.1-orange.svg)](.claude-plugin/marketplace.json)

Qiita向け技術記事の包括的レビューを提供するClaude Code Pluginsです。文章校正とAWS公式ドキュメント検証を組み合わせ、高品質な技術記事の作成をサポートします。

## 🚀 主な機能

### 📝 文章校正エージェント
- **日本語の自然さ**: 読みやすく自然な日本語表現への修正
- **技術用語の統一**: 適切な表記と用語の一貫性確保
- **構成の最適化**: 論理的な文章構造と段落構成の改善
- **Qiita Markdown記法**: Qiitaの記法に準拠した記述

### 🔍 AWS公式ドキュメント検証エージェント
- **技術的正確性**: AWS公式ドキュメントとの照合
- **API仕様の検証**: エンドポイント、パラメータ、レスポンス形式の確認
- **サービス名の正確性**: 正式なサービス名と表記の検証
- **料金・制限事項**: 最新の料金体系と制限事項の確認

### 🎯 統合レビューコマンド
- **包括的レビュー**: 2つのエージェントを連携した総合的なレビュー
- **優先度別フィードバック**: 重大・推奨・提案の3段階で整理
- **具体的な修正提案**: 実際の運用で役立つ改善案を提供

## 📦 インストール

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
```bash
/plugin install document-reviewer@document-and-code-reviewer
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
```bash
/plugin install  document-reviewer@document-and-code-reviewer
```

## 🎮 使用方法

### 基本的な使用方法

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

### 出力例

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

## 🏗️ アーキテクチャ

### プラグイン構成

```
claude-code-plugins/
├── .claude-plugin/
│   └── marketplace.json          # マーケットプレイス設定
├── agents/
│   ├── article-reviewer.md       # 文章校正エージェント
│   └── official-document-checker.md # AWS公式ドキュメント検証エージェント
├── commands/
│   └── document-reviewer.md      # 統合レビューコマンド
└── mcps/
    ├── aws-knowledge-mcp-server.json
    └── aws-documentation-mcp-server.json
```

## 🔧 設定

### レビューレベル

- **strict**: 最も厳格なレビュー（本格的な公開前）
- **normal**: 標準的なレビュー（日常的な使用）
- **quick**: 簡易レビュー（素早い確認）

### カスタマイズ

各エージェントの設定は、対応するMarkdownファイルを編集することで変更できます：

- `agents/article-reviewer.md`: 文章校正ルールの調整
- `agents/official-document-checker.md`: 技術検証ルールの調整

## 📋 対応技術

### 文章校正
- 日本語の自然さと読みやすさ
- 技術用語の適切な表記
- Qiita Markdown記法
- 文章構造と論理性

### AWS公式ドキュメント検証
- AWS サービス名とAPI仕様
- 料金情報と制限事項
- セキュリティ設定
- 最新の推奨事項

## 📄 ライセンス

このプロジェクトは [MIT License](LICENSE) の下で公開されています。

---

**技術記事の品質向上を支援するClaude Code Pluginsです。** 🚀
