---
date: 2026-06-15
task: Goal Plannerと自動実行キューをデータ統合（同一source・要約/詳細の役割分担）
runId: 20260615-001449
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260614-101321, 20260614-021255]
commitHashes: [36a35c8]
---

# 2026-06-15 Goal Planner × 自動実行キュー データ統合

> ユーザー要望「Goal Plannerと自動実行キューを同じ文脈に統合」。Codex 実装 → Fable レビュー。

## 実装概要
- **ズレ原因**: Goal Planner は `goals.json`（Todo/phase 進捗・0%表示）、`/queue` は `buildAutoQueue()` 派生を**別々に**見ていた。
- **どう揃えたか**: 集計源を **`buildAutoQueue().goalProgress` に統一**（別正本を作らない）。Goal Planner＝same-source の要約ビュー、`/queue?goalId=`＝詳細ビュー、と役割分担。

## データ源整理
- 統合前: Goal Planner=goals.json(calcGoalProgress/phase)、/queue=buildAutoQueue。
- 統合後の正本/集計: **`buildAutoQueue().goalProgress`（GoalProgressRow）** が唯一の集計源。`GoalProgressRow` に `nextCandidateCount`/`manual`/`latestWorkTitle`/`nextActionTitle` を追加し `buildGoalProgress()` が AutoQueueItem[] から Goal 単位で算出（0件Goalも残す）。

## 変更ファイル
- types/auto-queue.ts（GoalProgressRow 拡張）
- lib/auto-queue.ts（buildGoalProgress 拡張）
- app/goal-planner/page.tsx（goalProgress をカードへ渡す）
- components/goals/GoalListItem.tsx（キュー要約＋導線）
- components/goals/GoalPlannerForm.tsx（role選択UI撤去・内部既定保持）
- app/queue/page.tsx（goalId 絞り込み・全体に戻る・Goalバナー）
- app/guide/page.tsx / docs/operations/current-operating-model.md

## UI変更点
- **Goal Planner カード**（＋メイン目標）に: 次回候補 / 実行可能 / 判断待ち / レビュー待ち / 候補外 / 最新作業 / 次にやること を表示。
- 導線: 「自動実行キューを見る」→`/queue?goalId=`、「ToDoを見る」→`/decide?goalId=`、「作業予約」→`/prompt-queue`。
- `/queue`: goalId 指定でカウンタ/フィルタ/一覧を当該 Goal に絞り、「全体に戻る」＋ Goal バナー表示。
- 整理した旧UI: `GoalPlannerForm` の role選択（人間/Claude/Codex）を撤去（保存は内部既定 `addToQueueRoles:['claude']`）。旧 phases/todos 表示は互換のため残置。

## 検証結果
- tsc 0 / lint 0 / build 成功。
- /goal-planner /queue /decide すべて 200。
- Goal Planner カードにキュー要約が描画（次回候補/実行可能/判断待ち/レビュー待ち/候補外/最新作業/次にやること＋導線）を HTML で確認。
- **件数整合**: `goalProgress[goal-ai-factory-os]` reviewWaiting=1 と、アイテム status クロスチェック {review_waiting:1} が一致（same-source）。latestWorkTitle 取得確認。
- role選択UI撤去（対象案件 select は存続）を確認。

## 残課題
- iPhone実機での Goal Planner カード要約・キュー遷移・375px崩れ確認。
- 旧 phases/todos 表示の段階的非表示（不要確定後）。
- 次回候補数＝executable と同義の表示重複の整理余地。

## ChatGPT レビュー依頼文
```
対象: progress Goal Planner×自動実行キュー データ統合（runId 20260615-001449 / commit 36a35c8）
変更: types/auto-queue.ts, lib/auto-queue.ts, app/goal-planner/page.tsx, components/goals/{GoalListItem,GoalPlannerForm}.tsx, app/queue/page.tsx, guide, operating-model
背景: Goal Plannerとキューが別データを見ていたズレを、buildAutoQueue().goalProgress 同一source統合で解消。目標=要約/queue=詳細。
観点:
1. 同一source(goalProgress)統合で件数整合は担保されるか・抜けはないか。
2. Goal Plannerに旧phases/todos(0%)とキュー要約が併存する是非(混乱しないか/旧表示の撤去時期)。
3. role選択UI撤去の影響(既存Goal編集/ goal import で role を使う箇所がないか)。
4. 目標カードに出す指標の過不足(次回候補=executable重複等)。
```
