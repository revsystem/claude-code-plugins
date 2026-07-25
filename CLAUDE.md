# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Claude Code Plugins のマーケットプレイスリポジトリ。技術記事レビューとTerraformコードレビューの2つのプラグインを提供する。

## Lint

```bash
npx markdownlint-cli "**/*.md" --ignore node_modules
```

ルール設定は `.markdownlint.json` にある。テスト・ビルドの仕組みは存在しない。

## Architecture

### マーケットプレイス構成

`.claude-plugin/marketplace.json` が全プラグインの定義を管理する中央レジストリ。各プラグインは独立したディレクトリに分離されている。

### エージェント定義のフォーマット

`inherit` は Claude Code の実行モデルを継承し、`sonnet` はコスト効率のため明示指定。

### MCP サーバー設定のフォーマット

JSON ファイルで MCP サーバーを定義する。現在使用している `aws-knowledge-mcp-server` はリモート HTTP サーバーで、`url` と `type` のみを指定し認証情報を必要としない。

## Conventions

- プラグインのバージョンは `marketplace.json` と `README.md` の両方で管理する
- コミットメッセージは Conventional Commits (feat:, fix:, docs:, refactor:, chore:) を英語で記述
- 用語の統一基準は `document-reviewer/agents/resources/terminology-standards.md` に定義

### command と agent の責務分離

- command はユーザー向けインターフェース（使い方、引数、出力例、agent への委任関係）に限定する
- レビューロジック、チェックリスト、フィードバック形式は agent に集約する
- command に agent の内容を要約して埋め込まない
- 複数 agent を持つ場合のみ、command にオーケストレーション（並行実行、結果統合）を含める
- 詳細は `.claude/docs/plans/2026-03-16-deduplicate-plugin-command-agent-plan.md` を参照
