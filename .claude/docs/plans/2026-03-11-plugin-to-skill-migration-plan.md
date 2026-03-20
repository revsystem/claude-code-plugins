# プラグインからスキルへの移行検討プラン

作成日: 2026-03-11

## 背景

claude-code-plugins リポジトリには4つのプラグインが存在する。一方、claude-code-skills リポジトリではスキルとフックを symlink 方式で管理している。プラグインとスキルはそれぞれ異なる特性を持つため、各プラグインの構成要素を分析し、スキルとして提供する方が適切なものを特定する。

## プラグインとスキルの特性比較

| 特性 | プラグイン | スキル |
|------|-----------|--------|
| 実行コンテキスト | サブエージェント（独立プロセス） | メイン会話内 |
| MCP サーバー定義 | 可能 | 不可（外部定義の MCP は利用可） |
| 会話履歴の参照 | 不可（独立コンテキスト） | 可（全会話履歴を参照） |
| 対話性 | 低い（自律実行して結果を返す） | 高い（ユーザーと対話しながら実行） |
| 並列実行 | 可能（バックグラウンド実行） | 不可（メインスレッドで逐次実行） |
| 配布方法 | マーケットプレイス | git clone + symlink |
| 起動方法 | Agent tool で自動起動 | `/command` または自然言語で起動 |

スキルに適するケースは、MCP サーバーを必要とせず、構造化されたプロンプトワークフローが中心で、ユーザーとの対話が有益な場合である。逆に、MCP サーバーへの依存があり、長時間の自律処理やバックグラウンド実行が求められる場合はプラグインが適している。

## 全プラグインの分析結果

### 1. document-reviewer

4つの構成要素からなる複合プラグイン。

| 構成要素 | 種別 | 使用ツール | MCP 依存 | 移行判定 |
|----------|------|-----------|---------|---------|
| article-reviewer | agent | Read, Write, Edit | なし | 移行候補 |
| official-document-checker | agent | Read, Bash, WebSearch, WebFetch | aws-knowledge, aws-documentation | プラグイン維持 |
| document-reviewer | command | Read, Write, Edit | なし（サブエージェント並列起動） | プラグイン維持 |
| terminology-standards.md | resource | - | - | article-reviewer と共に移行 |

article-reviewer は MCP サーバーを一切使用せず、Read/Write/Edit のみで動作する。校正プロセス（用語チェック→構成分析→文章校正→Markdown最適化→フィードバック生成）は構造化されたプロンプトワークフローそのものであり、スキル定義との親和性が高い。

official-document-checker は AWS MCP サーバー2つに依存するため、プラグインのサブエージェントとして維持が必要。document-reviewer コマンドは2つのエージェントを並列起動して結果を統合するオーケストレーターのため、プラグインの枠組みが必須となる。

### 2. terraform-code-reviewer

| 構成要素 | 種別 | 使用ツール | MCP 依存 | 移行判定 |
|----------|------|-----------|---------|---------|
| terraform-code-reviewer-agent | agent | Read, Write, Edit | aws-terraform-mcp-server | プラグイン維持 |
| terraform-code-reviewer | command | Read, Write, Edit | なし（サブエージェント起動） | プラグイン維持 |

aws-terraform-mcp-server への依存があり、セキュリティ/ベストプラクティス/パフォーマンス/コストの4観点から詳細分析を行う処理はサブエージェントの自律実行に適している。移行対象外。

### 3. aws-cost-analyst

| 構成要素 | 種別 | 使用ツール | MCP 依存 | 移行判定 |
|----------|------|-----------|---------|---------|
| aws-cost-analyst | agent | Glob, Grep, Read, WebFetch, WebSearch | cost-explorer-mcp, pricing-mcp | プラグイン維持 |

2つの専用 MCP サーバー（Cost Explorer, Pricing）への強い依存がある。コスト分析は API 呼び出しを伴う長時間処理になりやすく、バックグラウンド実行の恩恵が大きい。移行対象外。

### 4. tech-docs-searcher

| 構成要素 | 種別 | 使用ツール | MCP 依存 | 移行判定 |
|----------|------|-----------|---------|---------|
| tech-docs-searcher | agent | Read, Glob, Grep, WebFetch, WebSearch | aws-documentation-mcp, aws-knowledge-mcp | プラグイン維持 |

AWS Documentation MCP と AWS Knowledge MCP に依存する。ドキュメント検索はバックグラウンド実行で他の作業と並行できる利点がある。移行対象外。

## 移行判定のまとめ

| プラグイン/構成要素 | 判定 | 理由 |
|-------------------|------|------|
| article-reviewer | スキルへ移行 | MCP 不使用、プロンプトワークフロー中心、対話性向上の恩恵あり |
| terminology-standards.md | スキルへ移行 | article-reviewer の参照リソース |
| official-document-checker | プラグイン維持 | AWS MCP 依存 |
| document-reviewer コマンド | プラグイン維持 | サブエージェント並列起動が必須 |
| terraform-code-reviewer | プラグイン維持 | MCP 依存、自律分析に適合 |
| aws-cost-analyst | プラグイン維持 | MCP 依存、長時間処理 |
| tech-docs-searcher | プラグイン維持 | MCP 依存、バックグラウンド実行の恩恵 |

## 移行候補の詳細設計: article-reviewer

### スキル移行の利点

1. メイン会話内で実行されるため、ユーザーとの対話的なレビューが可能になる。現状のサブエージェント方式では、校正結果を一括で受け取るだけで途中経過への介入ができない
2. 会話履歴を参照できるため、前の会話で議論した記事の修正方針を踏まえた校正が可能になる
3. 構造化されたプロンプトワークフロー（準備→構造分析→文章校正→Markdown最適化→フィードバック生成）は、スキルの SKILL.md フォーマットと自然に対応する
4. terminology-standards.md をスキルのリソースファイルとして同梱でき、自己完結する

### スキル定義の設計案

claude-code-skills リポジトリに以下の構造で配置する。

```text
skills/
  article-reviewer/
    SKILL.md                       # スキル定義本体
    resources/
      terminology-standards.md     # 用語規約（現行と同一内容）
```

SKILL.md の frontmatter:

```yaml
---
name: article-reviewer
description: Use when the user asks to review, proofread, or improve a Japanese technical article for Qiita. Also use when the user says 記事レビュー, 文章校正, or 記事チェック.
---
```

SKILL.md の本文構成:

- 現行の article-reviewer.md から校正プロセス、チェックリスト、校正ルール、Markdown 記法ルール、フィードバック形式を移植する
- `official-document-checker-agent` との連携に関する記述は削除し、スキル単体で完結する内容にする
- 用語規約の参照パスを `resources/terminology-standards.md`（スキルディレクトリからの相対パス）に変更する
- handover スキルのフォーマット（Process / Template / Quality Rules）に準拠する

### install.sh への追加

既存の install.sh に `skills/article-reviewer` ディレクトリの symlink 作成を追加する。現行の install.sh は `skills/` ディレクトリ配下のサブディレクトリを一括で symlink するため、新しいスキルを配置するだけで自動的に対応される。

### プラグイン側への影響と対策

article-reviewer をスキルに移行した後、プラグイン側には2つの選択肢がある。

選択肢 A: article-reviewer エージェントをプラグインから削除する

- document-reviewer コマンドは official-document-checker のみを起動する形に改修する
- 文章校正は `/article-reviewer` スキルで別途実行する運用に変更する
- 利点: 重複がなくなる。スキルによる対話的レビューが活かせる
- 欠点: `/document-reviewer` 一発で文章校正+技術検証を完了する現行ワークフローが崩れる

選択肢 B: article-reviewer エージェントをプラグインにも残す（二重管理）

- スキル版とプラグイン版の両方を維持する
- `/document-reviewer` コマンドは従来通り両エージェントを並列起動する
- スキル版は単独で対話的に使いたい場合に利用する
- 利点: 現行ワークフローを維持しつつ、対話的レビューも選択可能
- 欠点: 同一ロジックが2箇所に存在し、用語規約の更新が二重管理になる

推奨: 選択肢 A を採用する。理由は以下の通り。

- 技術記事のレビューにおいて、文章校正と技術検証は同時に実行する必要性が低い。文章校正はスキルで対話的に実施し、技術検証は後から `/document-reviewer`（official-document-checker のみ）で実行する運用が実用的
- 二重管理は用語規約の更新漏れリスクを生む
- スキルの対話性という利点を最大限に活かすには、サブエージェント版を廃止して移行先に一本化するのが明確

### 移行手順の概要

1. claude-code-skills リポジトリに `skills/article-reviewer/SKILL.md` と `skills/article-reviewer/resources/terminology-standards.md` を作成する
2. SKILL.md の内容は現行の article-reviewer.md から移植し、スキルフォーマット（Process / Quality Rules）に再構成する
3. terminology-standards.md は現行ファイルをそのままコピーする
4. `install.sh` を実行して symlink を作成する
5. `/article-reviewer` コマンドで動作確認を行う
6. 選択肢 A を採用する場合:
   - claude-code-plugins の `document-reviewer/agents/article-reviewer.md` を削除する
   - `document-reviewer/commands/document-reviewer.md` から article-reviewer エージェントへの参照を削除し、official-document-checker のみを起動する形に改修する
   - `document-reviewer/agents/resources/terminology-standards.md` を削除する（スキル側で管理するため）
   - marketplace.json のバージョンを更新する
