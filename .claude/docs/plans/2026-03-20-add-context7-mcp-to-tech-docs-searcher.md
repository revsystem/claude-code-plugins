# Add Context7 MCP to tech-docs-searcher Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** tech-docs-searcher プラグインに Context7 MCP サーバーを追加し、フレームワーク・ライブラリのドキュメント検索性能を向上させる。同時に `docs/plans/` ディレクトリを作成してプロジェクトのドキュメント整理基盤を整える。

**Architecture:** Context7 MCP (`@upstash/context7-mcp`) は `resolve-library-id` + `query-docs` の 2 ステップでライブラリの公式ドキュメントを取得する。現在 WebSearch/WebFetch で対応していたフレームワーク・ライブラリ調査を Context7 で補完し、より精度の高い情報を提供する。他の MCP と同様に `tech-docs-searcher/mcps/` に JSON 設定を配置し、エージェント定義と marketplace.json を更新する。

**Tech Stack:** npx (Node.js), @upstash/context7-mcp@latest, Claude Code Plugin format

---

### Task 1: docs ディレクトリ構造の作成

**Files:**
- Create: `docs/plans/.gitkeep`
- Create: `docs/specs/.gitkeep`

- [ ] **Step 1: docs/plans と docs/specs ディレクトリを作成する**

```bash
mkdir -p docs/plans docs/specs
touch docs/plans/.gitkeep docs/specs/.gitkeep
```

- [ ] **Step 2: Lint を実行してエラーがないことを確認する**

```bash
npx markdownlint-cli "**/*.md" --ignore node_modules
```

Expected: No errors

- [ ] **Step 3: コミット**

```bash
git add docs/plans/.gitkeep docs/specs/.gitkeep
git commit -m "chore: add docs/plans and docs/specs directory structure"
```

---

### Task 2: Context7 MCP サーバー設定ファイルの作成

**Files:**
- Create: `tech-docs-searcher/mcps/context7-mcp-server.json`

Context7 は npm パッケージのため、他の MCP が使用する `uvx` ではなく `npx` で起動する。

- [ ] **Step 1: MCP 設定ファイルを作成する**

```json
{
    "mcpServers": {
        "context7": {
            "command": "npx",
            "args": ["-y", "@upstash/context7-mcp@latest"],
            "env": {
                "DEFAULT_MINIMUM_TOKENS": "10000"
            },
            "disabled": false,
            "autoApprove": true
        }
    }
}
```

ファイルパス: `tech-docs-searcher/mcps/context7-mcp-server.json`

- [ ] **Step 2: Lint を実行してエラーがないことを確認する**

```bash
npx markdownlint-cli "**/*.md" --ignore node_modules
```

Expected: No errors

---

### Task 3: エージェント定義の更新

**Files:**
- Modify: `tech-docs-searcher/agents/tech-docs-searcher.md`

変更内容:
1. `tools:` に `mcp_context7` を追加
2. `## MCP サーバーの活用` に Context7 セクションを追加
3. `### 使い分けの指針` テーブルを更新（フレームワーク・ライブラリを Context7 優先に）

- [ ] **Step 1: frontmatter の tools に context7 を追加する**

変更前:
```
tools: Read, Glob, Grep, WebFetch, WebSearch, mcp_aws-documentation-mcp-server, mcp_aws-knowledge-mcp-server
```

変更後:
```
tools: Read, Glob, Grep, WebFetch, WebSearch, mcp_aws-documentation-mcp-server, mcp_aws-knowledge-mcp-server, mcp_context7
```

- [ ] **Step 2: MCP サーバーの活用セクションに Context7 の説明を追加する**

`### AWS Knowledge MCP Server` セクションの後に以下を追加:

```markdown
### Context7 MCP Server

フレームワーク・ライブラリの公式ドキュメントを取得する際に使用する。2 ステップで動作する:

1. **resolve-library-id**: ライブラリ名から Context7 の library ID を解決する
2. **query-docs**: library ID を指定して公式ドキュメントを取得する

WebSearch/WebFetch より精度の高い公式ドキュメントを取得できる。バージョン指定も可能。
```

- [ ] **Step 3: 使い分けの指針テーブルを更新する**

変更前:
```markdown
| フレームワーク・ライブラリ | WebSearch → WebFetch |
```

変更後:
```markdown
| フレームワーク・ライブラリ | Context7 MCP (`resolve-library-id` → `query-docs`) → WebSearch |
```

- [ ] **Step 4: Lint を実行してエラーがないことを確認する**

```bash
npx markdownlint-cli "**/*.md" --ignore node_modules
```

Expected: No errors

- [ ] **Step 5: コミット**

```bash
git add tech-docs-searcher/mcps/context7-mcp-server.json tech-docs-searcher/agents/tech-docs-searcher.md
git commit -m "feat: add Context7 MCP server to tech-docs-searcher for library docs search"
```

---

### Task 4: marketplace.json の更新

**Files:**
- Modify: `.claude-plugin/marketplace.json`

変更内容:
1. `tech-docs-searcher` の `mcpServers` に `./mcps/context7-mcp-server.json` を追加
2. `tech-docs-searcher` の `version` を `0.0.5` → `0.0.6` に更新
3. `keywords` に `"context7"` を追加

- [ ] **Step 1: tech-docs-searcher エントリを更新する**

変更前:
```json
{
  "name": "tech-docs-searcher",
  ...
  "version": "0.0.5",
  "keywords": ["documentation", "research", "aws"],
  ...
  "mcpServers": [
    "./mcps/aws-knowledge-mcp-server.json",
    "./mcps/aws-documentation-mcp-server.json"
  ]
}
```

変更後:
```json
{
  "name": "tech-docs-searcher",
  ...
  "version": "0.0.6",
  "keywords": ["documentation", "research", "aws", "context7"],
  ...
  "mcpServers": [
    "./mcps/aws-knowledge-mcp-server.json",
    "./mcps/aws-documentation-mcp-server.json",
    "./mcps/context7-mcp-server.json"
  ]
}
```

- [ ] **Step 2: コミット**

```bash
git add .claude-plugin/marketplace.json
git commit -m "chore: bump tech-docs-searcher to v0.0.6 with context7 MCP"
```

---

### Task 5: 今回の計画ファイルを docs/plans に保存する

**Files:**
- Create: `docs/plans/2026-03-20-add-context7-mcp-to-tech-docs-searcher.md`

- [ ] **Step 1: 計画ファイルを docs/plans にコピーする**

`docs/plans/2026-03-20-add-context7-mcp-to-tech-docs-searcher.md` として本計画の内容を保存する。

- [ ] **Step 2: .gitkeep を削除する（ファイルが追加されたため不要）**

```bash
git rm docs/plans/.gitkeep
```

- [ ] **Step 3: コミット**

```bash
git add docs/plans/2026-03-20-add-context7-mcp-to-tech-docs-searcher.md
git commit -m "docs: add implementation plan for context7 MCP integration"
```

---

## Verification

実装完了後、以下で動作を確認する:

1. Lint パス確認:
   ```bash
   npx markdownlint-cli "**/*.md" --ignore node_modules
   ```

2. marketplace.json の JSON 構文確認:
   ```bash
   cat .claude-plugin/marketplace.json | python3 -m json.tool > /dev/null && echo "JSON valid"
   ```

3. Context7 MCP 設定の JSON 構文確認:
   ```bash
   cat tech-docs-searcher/mcps/context7-mcp-server.json | python3 -m json.tool > /dev/null && echo "JSON valid"
   ```

4. エージェントファイルに `mcp_context7` が含まれることを確認:
   ```bash
   grep "mcp_context7" tech-docs-searcher/agents/tech-docs-searcher.md
   ```

5. marketplace.json に context7 MCP が登録されていることを確認:
   ```bash
   grep "context7" .claude-plugin/marketplace.json
   ```
