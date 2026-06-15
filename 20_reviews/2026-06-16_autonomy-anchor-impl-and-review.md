---
date: 2026-06-16
task: 自走化Goal最優先アンカー化＋selection記録＋dry-run＋達成通知土台（Codex実装→Fableレビュー）
runId: 20260616-002003
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260615-212004]
commitHashes: [bc31630]
---

# 2026-06-16 自走化Goal 最優先アンカー化（Phase1 Codex / Phase2 Fableレビュー）

> 設計レビュー(runId 20260615-212004)に基づくEpic。Phase1=Codex実装、Phase2=Fable実装後レビュー。**結論 close_ok**（1点修正適用）。

## 実装概要（Phase1）
- **ズレ原因**: epic-91「AI工場オペレーションセンター」はGoal紐付け＋pinはあるが、契約（priority/doneCriteria/riskFlags/preferredExecutor）不足で実行Epicとして不完全＝`manual`(候補外)。
- **統合**: 既存 `goal-ai-factory-os`（AI工場OS自走化/North Star）を**正本**に（新規 factory-autonomy は作らない）。`epic-91` に P0/factoryEligible=true/riskFlags=[]/有限doneCriteria7件 を付与し実行アンカーEpic化。
- **表示=実行の一致**: `buildAutoQueue()`(表示) と `scanFactoryDispatch()/runFactory()`(実行) を共通の `lib/autonomy-anchor.ts` 判定で最優先化。順序 **pin > review-fix > autonomy anchor > 通常P0**。
- **selectionログ**: ExecutionRun.selection（selectedGoalKey/Title/WorkItemId/Reason/priority/decisionPolicy/riskFlags/hasFixPrompt/selectedAt）を記録。Goalは lastSelectedRunId/lastSelectedAt の最小ポインタのみ。
- **dry-run**: `GET /api/operations/goal-ai-factory-os-next-run-selection-test`＝実選定関数をread-onlyで呼ぶ。Blocked/OFF時 wouldRunNext=false。
- **メール通知土台**: completed時 `NOTIFY_EMAIL_TO` 宛。実送信サービス未導入＝no-op/log。

## Phase2 Fableレビュー（独立検証）
- tsc 0 / lint 0 / build 成功。
- **表示=実行一致を確認**: dry-runで `queue.sourceId=epic-91` と `dispatch.epicId=epic-91`（共に goal-ai-factory-os）。/api/auto-queue `next=epic-91`・`candidateEligible=true`・`pinnedExcluded=[]`（候補外解消）。factory Running時 wouldRunNext=true、Blocked時 false。
- ExecutionRun.selection 記録・Goal最小ポインタを確認。
- 安全: 危険(blocked)/判断要(waiting_user)ゲート・review-fix優先・caps維持。
- **修正した懸念（footgun）**: Codex初版は no-op通知が実送信フラグ `autonomyNotifiedAt` を立て `sent:true` を返していた。→ 将来実メールプロバイダを入れても「送信済み」扱いで**実メールが永久に飛ばない**。修正: no-opは `autonomyNotifyNoopAt`（ログ1回）に分離し `autonomyNotifiedAt` を消費しない。実送信は `AUTONOMY_EMAIL_PROVIDER` 構成時に**成功後のみ** `autonomyNotifiedAt` を立てる設計に。送信モジュール未実装のうちは provider 設定時も「未送信」として attempts のみ加算（誤った送信済み化を防ぐ）。types/goal・goal-reader・.env.local.example も対応。

## 変更ファイル
lib/autonomy-anchor.ts(新) / lib/autonomy-notification.ts(新) / lib/auto-queue-score.ts / lib/auto-queue.ts / lib/factory-dispatch.ts / lib/factory-runner.ts / lib/factory-schedule.ts / lib/goal-reader.ts / types/auto-queue.ts / types/execution-run.ts / types/goal.ts / lib/types/operations.ts / app/api/operations/goal-ai-factory-os-next-run-selection-test/route.ts(新) / app/page.tsx / app/queue/page.tsx / components/goals/GoalListItem.tsx / app/guide/page.tsx / docs/operations/current-operating-model.md / .env.local.example
※ epic-91 の契約は `data/real/epics.json`（runtime data）のため commit 対象外。

## Phase2 最終判断
- **close_ok**（email footgun 修正を適用済み）。
- needs_followup（将来）: 実メール送信モジュール（Resend/SMTP）の実装は別Phase。autonomy-anchor は epic-91 をID固定で認識（契約チェック付き）＝epic-91変更時は要追従。epic-91 doneCriteria が自己参照的＝次の自走化マイルストーンEpicの定義が必要。

## 残課題 / 監視
- 次回定時で epic-91 が実際に先頭選定され ExecutionRun.selection が残るか監視。
- epic-91 契約は runtime data。新規環境では再付与が必要。
- 実メール送信は未実装（no-opログのみ）。

## ChatGPT レビュー依頼文
```
対象: progress 自走化Goal最優先アンカー化＋通知土台（runId 20260616-002003 / commit bc31630・Phase2 close_ok）
変更: lib/autonomy-anchor.ts, lib/autonomy-notification.ts, lib/auto-queue*.ts, lib/factory-dispatch.ts, lib/factory-runner.ts, lib/factory-schedule.ts, types/*, app/api/operations/goal-ai-factory-os-next-run-selection-test, app/page.tsx, app/queue/page.tsx, components/goals/GoalListItem.tsx, docs, .env.local.example
背景: 既存goal-ai-factory-osを正本に自走化を最優先アンカー化。表示(buildAutoQueue)と実行(scanFactoryDispatch)を一致。selection記録/dry-run/通知土台。Fableレビューでno-op通知のautonomyNotifiedAt消費footgunを修正。
観点:
1. epic-91をID固定でアンカー認識する設計の堅牢性(契約消失時はisAutonomyAnchorEpicがfalseになる)。
2. 永久ピン飢餓の懸念(epic-91が常に先頭→他作業が進まない)への対策十分性。
3. 表示=実行の一致が本当に担保されるか(buildAutoQueueとscanFactoryDispatchのboost規則が同一か)。
4. メール通知のidempotency再設計(noopAt分離・実送信は成功後notifiedAt)に穴がないか。
5. epic-91 doneCriteriaが自己参照的＝completion/メール発火が成立するか。
```
