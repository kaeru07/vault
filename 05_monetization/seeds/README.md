---
title: Progress app データseed（AI工場オペレーションセンター）
type: seed-data
issue: kaeru07/vault#91
created: 2026-05-30
---

# Progress app データ seed

> Epic #91 の `/operations` ページ実装時に Progress app の `data/real/` へコピーするseedデータ。
> **Vault側はこのフォルダが正本。Progress側のデータが壊れたときはここから復元。**

## デプロイ手順

Progress app の `data/real/` ディレクトリへコピーする:

```bash
cp epics-seed.json         /root/company/apps/ny01/progress/data/real/epics.json
cp approvals-seed.json     /root/company/apps/ny01/progress/data/real/approvals.json
cp operational-decisions-seed.ndjson /root/company/apps/ny01/progress/data/real/operational-decisions.ndjson
```

## ファイル説明

| ファイル | Progress側パス | 説明 |
|---|---|---|
| `epics-seed.json` | `data/real/epics.json` | Epic一覧（goal/progress/remainingWork/latestRunId/nextAction/decisionPolicy） |
| `approvals-seed.json` | `data/real/approvals.json` | 承認待ち一覧（初期空配列） |
| `operational-decisions-seed.ndjson` | `data/real/operational-decisions.ndjson` | 運用判断ログ（初期空・Claude Code が読み書き） |

## スキーマ（設計レビュー参照）

詳細スキーマ: [[../AI工場オペレーションセンター設計レビュー]] §3

## 関連
- 実装 Runbook: [[../AI工場_operations実装Runbook]]
- Issue: kaeru07/vault#91
</content>
