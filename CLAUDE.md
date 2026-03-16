# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Claude Code Plugins のマーケットプレイスリポジトリ。技術記事レビュー、Terraformコードレビュー、AWSコスト分析、技術ドキュメント検索の4つのプラグインと、共有MCPサーバーを提供する。

## Lint

```bash
npx markdownlint-cli "**/*.md" --ignore node_modules
```

ルール設定は `.markdownlint.json` にある。テスト・ビルドの仕組みは存在しない。

## Architecture

### マーケットプレイス構成

`.claude-plugin/marketplace.json` が全プラグインの定義を管理する中央レジストリ。各プラグインは独立したディレクトリに分離されている。

### プラグイン一覧

| プラグイン | 種別 | モデル | MCP |
|-----------|------|--------|-----|
| `document-reviewer` | agents + command | inherit | aws-shared-mcps |
| `terraform-code-reviewer` | agent + command | inherit | 固有 (aws-terraform-mcp-server) |
| `aws-cost-analyst` | agent のみ | sonnet | 固有 (cost-explorer, pricing) |
| `tech-docs-searcher` | agent のみ | sonnet | aws-shared-mcps |
| `aws-shared-mcps` | MCP のみ | - | aws-documentation, aws-knowledge |

### ディレクトリ構造の規約

各プラグインは `source` で指定されたディレクトリに以下のサブディレクトリを持つ:

- `agents/` - エージェント定義 (Markdown + YAML frontmatter)
- `commands/` - スラッシュコマンド定義 (Markdown)
- `mcps/` - MCP サーバー設定 (JSON)
- `agents/resources/` - エージェントが参照するリソースファイル

### エージェント定義のフォーマット

```yaml
---
name: agent-name
description: 説明文
tools: Read, Write, Edit, mcp_server-name
model: inherit|sonnet
color: blue|red|green|cyan
---
```

`inherit` は Claude Code の実行モデルを継承し、`sonnet` はコスト効率のため明示指定。

### MCP サーバー設定のフォーマット

JSON ファイルで `command`, `args`, `env` を定義。すべて `uvx` コマンドで起動する。AWS 系は `${AWS_PROFILE:-default}` と `${AWS_REGION:-us-east-1}` を環境変数から参照する。

### 共有 MCP の仕組み

`aws-shared-mcps` は `document-reviewer` と `tech-docs-searcher` が共通で利用する AWS Documentation / Knowledge MCP サーバーを集約したプラグイン。個別プラグインから MCP 定義を削除し、依存先として参照する設計。

## Conventions

- プラグインのバージョンは `marketplace.json` と `README.md` の両方で管理する
- コミットメッセージは Conventional Commits (feat:, fix:, docs:, refactor:, chore:) を英語で記述
- 用語の統一基準は `document-reviewer/agents/resources/terminology-standards.md` に定義
- マーケットプレイス名は `document-and-code-reviewer` (レジストリ上の名前)

### command と agent の責務分離

- command はユーザー向けインターフェース（使い方、引数、出力例、agent への委任関係）に限定する
- レビューロジック、チェックリスト、フィードバック形式は agent に集約する
- command に agent の内容を要約して埋め込まない
- 複数 agent を持つ場合のみ、command にオーケストレーション（並行実行、結果統合）を含める
- 詳細は `.claude/docs/plans/2026-03-16-deduplicate-plugin-command-agent-plan.md` を参照
