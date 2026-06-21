---
date: 2026-06-21
task: キュー優先順位操作を全アイテム種別で機能・即時反映(#13エラー/上下無効を根本修正)
runId: 20260621-173721
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260621-173720]
commitHashes: [b2b6668]
---

# 2026-06-21 キュー優先順位操作の根本修正（Codex委譲・Claude実APIテスト検証）

## 1. 作業目的
- ユーザー報告: キュー #13(goal達成系)で「最優先」押下→`workItemId(epic:* or todo:*) and valid action are required`。上下矢印も効かない。優先順位変更が確実にでき即時反映するよう根本から見直す。

## 2. 原因
- キューは epic(`epic:`)/goal_todo(`todo:`)/goal(`goal:`)の3種。control API は epic/todo のみ処理し **goal:* を弾く**(#13)。Goal は queueControl を持たず pinnedTop 合成のみで **manualOrder 不可**。`canMove` が epic 限定で上下が goal/todo で無効。

## 3. 修正内容（Codex委譲・精密仕様）
- `types/goal.ts`: Goal に `queueControl?` 追加。
- `lib/goal-reader.ts`: normalizeGoal で queueControl 保持(read→write脱落防止)。
- `lib/goal-writer.ts`: `updateGoalControl(goalId, {pinnedTop?, priorityBoost?, queueControl?})` 追加。
- `lib/auto-queue.ts`: goal アイテムが `goal.queueControl` を使用(manualOrder/hold 反映)。
- `app/api/auto-queue/control/route.ts`: `parseGoalId` + goal ブロック(pin/unpin/prioritize/hold/unhold/exclude/include/moveUp/Down/complete422)。`writeManualOrder` を epic/todo/goal 汎用化(各 queueControl.manualOrder 永続化)。
- `app/queue/page.tsx`: `canMove = item.status === 'executable'`(全種別で上下可)。

## 4. 検証結果（Claude・実APIテスト）
- tsc0 / build0 / lint0。
- 実 `goal:goal-mqluko5f-wbefy`: prioritize→200(pinnedTop=true/priorityBoost=2/queueControl設定)、moveDown→200(queueControl.manualOrder=3 永続化)、hold→200。テスト変更は unhold/unpin で原状回復(pinnedTop=false/hold=false)。
- 即時反映: API 応答に更新後 queue を返却＋revalidate(/, /queue)＋ボタンが router.refresh。
- git push: b2b6668

## 5. 未対応 / 6. 危険ポイント
- UIでの上下/最優先の即時反映は実機確認推奨(API/データは検証済)。Goal の priority 操作はキュー表示順のみ・破壊操作なし。

## 7. 次にやるべきこと
- 実機(iPhone)で #13 の最優先・上下の即時反映を確認。

## 8. ChatGPT レビュー依頼文
````text
対象: progress 自動実行キューの優先順位操作 / runId 20260621-173721 / commit b2b6668
goal:*アイテムでcontrol API未対応+上下epic限定だった不具合を根本修正(Goalにqueue Control
追加・全種別でpin/prioritize/hold/moveが効く)。実装Codex委譲・Claude実APIテスト検証
(goal prioritize/moveDown/hold 200・manualOrder永続化)。確認観点: 種別ごとの優先制御モデル
統一は妥当か/manualOrderと自動スコアの整合。
````
