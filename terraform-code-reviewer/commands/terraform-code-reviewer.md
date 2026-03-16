---
name: terraform-code-reviewer
description: Terraformコードの包括的レビューを実行するコマンド。セキュリティ、ベストプラクティス、パフォーマンス、コスト最適化を総合的に評価し、実用的な改善提案を提供。
tools: Read, Write, Edit
model: inherit
---

# Terraform コード包括レビューコマンド

このコマンドは、`terraform-code-reviewer-agent` にレビューを委任し、Terraformコードの包括的なレビュー結果を提供します。

## 使用方法

### 基本的な使用方法

```bash
/terraform-code-reviewer
```
Terraformコード全体を包括的にレビューします。

### 特定のファイルをレビュー

```bash
/terraform-code-reviewer --file main.tf
```
指定したTerraformファイルのみを重点的にレビューします。

### 特定の観点でレビュー

```bash
/terraform-code-reviewer --focus security
```
セキュリティに特化したレビューを実行します（security/cost/performance/best-practices）。

### レビューレベルの指定

```bash
/terraform-code-reviewer --level strict
```
より厳格なレビューを実行します（strict/normal/quick）。

## 出力例

### レビューサマリー

```text
📊 Terraformコードレビュー結果サマリー
- 重大な問題: 2件
- 推奨修正: 8件
- 追加提案: 5件
- 総合評価: B+ (良好、セキュリティとコスト最適化推奨)
```

### 詳細フィードバック

各問題について、以下の情報を提供：
- 問題の概要と影響度
- 現在のコード内容
- 具体的な修正提案（コード例付き）
- 修正理由と期待される効果
- 関連するTerraform公式ドキュメント

## エージェント連携

### terraform-code-reviewer-agent

- 担当: Terraformコードの詳細分析と改善提案
- 対象: セキュリティ、ベストプラクティス、パフォーマンス、コスト最適化
- 出力: 優先度別（🔴重大/🟡推奨/🟢追加）のフィードバックと具体的なコード修正提案

レビュープロセス、チェックリスト、フィードバック形式の詳細は agent 側で定義されています。
