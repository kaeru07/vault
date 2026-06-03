---
date: 2026-06-04
task: Factory定期実行履歴は新規作成せず既存 /logs を拡張（定期実行フィルタ + Factoryメタ）
runId: 20260604-001051
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260602-121838]
commitHashes: []
---

# Factory定期実行履歴：既存機能調査 → 拡張（2026-06-04）

## 1. 作業目的
「Factory定期実行履歴ページ」を作る前に、既存機能・ページ・API・データに同等機能がないか調査し、二重実装を避ける。既存があれば新規作成せず拡張する。

## 2. 事前調査の結果（既存に同等機能あり）
- **既存ページ**: `/logs` に `mode=event/history/review/daily`。`history` が全 ExecutionRun を `ExecutionRunList` で一覧表示。`/automation`・`/epic` は現在状態カードのみで履歴一覧は持たない。
- **ExecutionRun一覧で代用可**: 可。`source`(schedule/boot/factory_runner) / `trigger`(systemd/cron/startup) / `factoryRun` / `runnerMode` / `runIndex` / `stopReason` を保持し、Factory定期実行を識別できる。
- **data/real**: `factory-runs` / `schedule-runs` / `logs` 系の別JSONは**なし**。`execution-runs.json`（単一正本）+ `automation-log.ndjson`（fallback）+ `work-log.ndjson`（イベント）のみ。`factory-schedule.log` は shell 用で data 外。
- **app/api**: `/api/execution-runs` が既存。Factory専用の履歴APIはなし。
- **lib**: `execution-run-reader` が集約。`factory-overview`/`factory-schedule-status` は現状表示用。
- **Nav**: TopNav/BottomNav に「ログ」導線あり。

→ 判断ツリーの「**既存ログ画面で十分なら定期実行タブ（フィルタ）を追加**」に該当。

## 3. 拡張内容（新規ページ/JSON/API/ナビは作らない）
- `app/logs/page.tsx`: 実行履歴モードに「🏭 定期実行のみ」フィルタを追加（`isFactoryRun` 判定 + `factory=1` クエリ + トグルUI）。
- `components/logs/ExecutionRunCard.tsx`: 該当 Run のみ Factory メタバッジ（🏭 + source / trigger / Run# / stopReason）を表示。

## 4. なぜ新規作成でなく拡張したか
`/factory-runs` を新設すると、`execution-runs.json` と同内容の別JSON・別API・別ナビを二重に作ることになり、禁止事項（別JSON複製 / 二重API / ナビ増やしすぎ）に抵触する。既存 `/logs` に `source/trigger/factoryRun` の絞り込みを足すだけで「定期実行履歴の閲覧」要件を満たせる。

## 5. 検証結果
- typecheck OK / lint OK / build OK / pm2 deploy。
- `/logs?mode=history`=215件 → `&factory=1`=**29件**に絞り込み確認。Factoryページの全カードに🏭メタバッジ表示。

## 6. 未対応 / 次にやるべきこと
- 期間別・source別の集計サマリは未追加（必要なら同フィルタ上に検討）。
- systemd `factory-schedule.service` の exit-code 1 問題（runId 20260602-121838）は、このフィルタで定期実行の成否を追跡しやすくなった。

## 7. ChatGPTレビュー依頼文
```
対象: progress / Factory定期実行履歴 = 既存/logs拡張 / runId 20260604-001051
新規ページを作らず、既存 /logs?mode=history に「🏭定期実行のみ」フィルタ（source/trigger/factoryRunで絞る）と ExecutionRunCard の Factoryメタ表示を追加しました。execution-runs.json が単一正本で別JSON/別APIは作っていません。
確認したい観点:
1. 二重実装回避の判断（/factory-runs 新設せず /logs 拡張）は妥当か。
2. isFactoryRun の判定条件（source/trigger/factoryRun）に過不足はないか。
3. 215→29 の絞り込みが「定期実行履歴」として十分か、集計サマリ等の追加が要るか。
```
