---
date: 2026-06-12
task: AI工場OS運用設計レビュー（Fable5）+ 不足TOP10のCodex実装完了・本番反映
runId: 20260612-212444
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy, monetization]
relatedRunIds: [20260612-212444]
commitHashes: [9b85d79]
reviewFileCommit:
---

# AI工場OS運用設計レビュー + Codex実装TOP10完了

## 1. 作業目的

Progressアプリ（AI工場OSの司令塔）が「毎日5〜15分の判断でAI工場が止まらず進む」状態として成立しているかを10観点（AIの現在状況/次回実行予定/Project進捗/Goal進捗/収益化距離/判断の分離/Epic→Factory流入/修正依頼閉ループ/1週間放置耐性/半年運用耐性）で設計レビューし、今回はレビューで止めず、不足部分をCodexに実装させ本番反映・検証まで完了させる。Fable=設計レビュー担当、Codex=実装担当の役割分担。

## 2. 実施内容

- コード・実データ実態調査（execution-runs 99件 / epics 11件 / recommended 59件 / approvals 8件 / systemd timer 23:00稼働）に基づく全体レビュー。総合 **72/100**
- 最重大欠陥の特定: **修正依頼（needs_followup）が表示のみで閉ループしていない**（dispatch/runner/knowledge-loop のどれも消費せず6件永久滞留。ホームの「次作業候補に回しています」は虚偽表示）
- その他: 実行中も「待機中」表示 / Epic候補41件無期限滞留 / 収益ロードマップBirdLogハードコード / 承認→Goal紐付けの二度手間 / 停止の放置検知なし / execution-runs肥大リスク
- Codex実装対象TOP10を策定し、Codex（thread 019ebbb0-325a）に2ラウンドで投入 → **全10項目実装完了**
- Fable側で独立検証 → pm2再起動で本番反映 → commit/push

## 3. 変更ファイル

- progress/lib/knowledge-loop.ts — 修正依頼閉ループ（followupOfRunId付き候補生成+backfill）
- progress/lib/factory-runner.ts — running Run記録と完了時更新（30分stale回復）
- progress/lib/recommended-epics-store.ts — 30日expired / Goal同時指定承認 / followup継承
- progress/lib/command-center.ts — 収益config駆動・停止経過バンド・Goal根拠・TERMS更新
- progress/lib/execution-run-archive.ts（新規） — アーカイブローテーション（閾値300・バックアップ必須）
- progress/lib/data-health.ts（新規） — 孤児参照・滞留・running残留の整合チェック
- progress/lib/page-data-cache.ts（新規） — ページ表示専用request cache
- progress/data/real/revenue-config.json（新規） — 収益ロードマップ設定の正本
- progress/app/page.tsx ほかUI・API・guide・current-operating-model.md セット更新（計26ファイル）

## 4. 検証結果

- npx tsc --noEmit: 成功（Fable側で独立再実行）
- npm run lint: 警告ゼロ / npm run build: 成功
- データ保全: runs 99 / epics 11 / goals 4 / approvals 8 で増減なし。recommended 59→65 は既存needs_followup 6件のbackfill候補生成（仕様通り・削除ゼロ）
- pm2 restart 後、/ /decide /revenue /guide /portfolio /logs すべて200。収益¥0表示・Goal根拠注記・修正依頼セクションのレンダリング確認
- 機密スキャン: ヒットなし / git push: 9b85d79 を origin/main へ push 済み

## 5. 未対応

- dev環境でのHTTP確認はCodexサンドボックス制約で不可だったため、Fable側の本番反映後確認で代替
- P2-7アーカイブは閾値300未満のため実データ移動は未発生（合成320件でのユニット検証のみ実施）
- 危険判断pending時の「全体停止→個別Epic停止」への緩和、AI保留からの人間作業復帰トリガーはTOP10外として未実装

## 6. 危険ポイント

- 修正候補の自動生成は重複ガード（followupOfRunId一致で再生成しない）前提。万一二重生成されたらrejected化で対処（削除不要）
- アーカイブ初発動（300件超過時）はactive+archive合計件数の照合を必ず人間が1回確認すること
- factory-runnerのrunning記録は異常終了時に30分staleで自動回復するが、初回の23:00実行で実地確認が必要

## 7. 次にやるべきこと

- iPhoneで主要5ページの375px表示確認（下記観点）
- 次回23:00自動実行で「今」のrunning表示・followup backfill・expired判定の実地動作を観察
- 30日後にexpired初発動の妥当性確認 / 300件到達時にアーカイブ初発動の件数照合

## 8. ChatGPT レビュー依頼文

```
Progressアプリ（AI工場OS司令塔）の運用設計レビューと実装をレビューしてください。
対象: kaeru07/ny01 の commit 9b85d79（progress/配下26ファイル）/ runId 20260612-212444

背景: Fable5が10観点で設計レビュー（総合72/100）し、不足TOP10をCodexが実装。
主要変更: ①修正依頼の閉ループ（needs_followup→followupOfRunId付き候補自動生成）
②factory-runner実行中のrunning Run記録 ③Epic候補30日expired ④収益ロードマップのrevenue-config.json駆動化
⑤候補承認時のGoal同時指定 ⑥工場停止の経過日数バンド ⑦ExecutionRunアーカイブ（閾値300）
⑧ページ専用request cache ⑨Goal進捗の算出根拠表示 ⑩data-healthチェック

確認したい観点:
1. 修正依頼閉ループの設計（followupOfRunIdリンク方式）に抜けはないか。修正run側へのリンク伝搬がEpic notes経由なのは脆くないか
2. running Run記録方式（異常終了30分stale回復）でゴーストrunが残るエッジケースはないか
3. expired 30日・アーカイブ閾値300件のパラメータは半年運用で妥当か
4. 総合72点の採点とTOP10の優先順位付けは妥当か。漏れている破綻リスクはないか
```
