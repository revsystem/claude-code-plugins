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

指摘は優先度（高/中/低）でグルーピングされ、各指摘にファイル・行、問題の概要、現在のコードと修正後のコード例、修正理由、参照した公式ドキュメントのURLが含まれます。

```text
## 高優先度

### main.tf:12 セキュリティグループがSSHを0.0.0.0/0に開放

現在のコード:
  cidr_blocks = ["0.0.0.0/0"]
修正後:
  cidr_blocks = [var.admin_cidr]
理由: SSHポートの全開放は侵入リスクが高い。接続元を管理用CIDRに限定する。
参照: https://docs.aws.amazon.com/vpc/latest/userguide/security-group-rules.html
```

## エージェント連携

### terraform-code-reviewer-agent

- 担当: Terraformコードの詳細分析と改善提案
- 対象: セキュリティ、ベストプラクティス、パフォーマンス、コスト最適化
- 出力: 優先度別（高/中/低）のフィードバックと具体的なコード修正提案

チェック観点、指摘しない項目、レポート形式の詳細は agent 側で定義されています。
