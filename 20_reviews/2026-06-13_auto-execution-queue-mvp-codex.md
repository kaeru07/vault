---
date: 2026-06-13
task: 自動実行キュー(/queue) MVP実装 — Codex実装→Fableレビュー（野鳥問題=二重正本の解消）
runId: 20260613-150148
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260613-125025]
commitHashes: [89fc8b0]
---

# 2026-06-13 自動実行キュー(/queue) MVP実装＋レビュー

> 設計(runId 20260613-125025 / docs/auto-execution-queue-design.md)の §11 MVP を Codex(gpt-5.2) が実装し、Fable がレビュー・検証した記録。

## 1. 作業目的

- なぜ: 司令塔トップの「次にやること」が未整理/低関連案件（野鳥観察系）を先頭に出していた。Epicベースの正しい優先順位で「次に何をやるか・なぜ次か」を出し、毎日少し判断するだけの状態にする。
- 背景: 原因は work-queue.json（project-tasks由来）と Epic Contract の二重正本。設計で Epic=実行正本・キュー=派生ビューに統一する方針を確定済み。
- 期待効果: 野鳥問題の解消。低優先レビュー・AI保留で工場全体が止まらない。

## 2. 実施内容

- Codex に設計書 §14（仕様書）§15（プロンプト）を渡し MVP を実装させた（コミットはレビュー後に Fable が実施）。
- Fable がレビュー: 独立に tsc/lint/build 再実行、実データでの分類検証、pin の安全性検証。
- ny01 ルールに従い運用ドキュメント（operating-model 画面表/変更履歴/frontmatter、guide FAQ）をセット更新。
- commit 89fc8b0 を push。

### 実装の要点
- `buildAutoQueue()` が Epic/Goal/ExecutionRun/Approval/Inbox から派生（work-queue.json不使用・新正本なし）。
- `deriveWorkItemStatus`（§7.1順）＋`computeQueueScore`（pin>Goal pin>P0/P1/P2+Goal boost>freshness、タイブレーク manualOrder→priority→lastRunAt）。
- 司令塔トップの次回自動実行予定/候補3/5カウンタ/Goal進捗を buildAutoQueue 表示元に差し替え。
- /queue（最優先pin/上下/保留/対象外）＋ API（GET auto-queue / POST control / POST goals priority）。
- 旧 work-queue 並べ替え UI は /legacy/queue へ無削除退避。
- 手動操作は Epic.queueControl / Goal.priorityBoost・pinnedTop に書き戻し。

## 3. 変更ファイル

| ファイル | 変更 |
|---|---|
| types/auto-queue.ts / lib/auto-queue-score.ts / lib/auto-queue.ts | 新規（型・スコア純関数・派生ビュー生成） |
| app/api/auto-queue/route.ts / control/route.ts / app/api/goals/[goalId]/priority/route.ts | 新規 API |
| app/queue/page.tsx / QueueActionButton.tsx / app/legacy/queue/page.tsx | キュー画面＋旧UI退避 |
| app/page.tsx / components/navigation/TopNav.tsx | トップ表示差し替え・ナビ |
| lib/types/operations.ts / types/goal.ts / lib/goal-reader.ts / lib/goal-writer.ts | queueControl / priorityBoost / pinnedTop 追加 |
| docs/operations/current-operating-model.md / app/guide/page.tsx | 運用ドキュメント |

## 4. 検証結果（Fableが独立実行）

- typecheck: OK（tsc --noEmit exit 0）
- lint: OK（No ESLint warnings or errors）
- build: OK（Compiled successfully / 49 pages）
- 手動確認: pm2 restart後 / ・/queue ・/legacy/queue ・/decide ・/guide すべて HTTP 200。GET /api/auto-queue が AutoQueueView 構造を返す。
- 実データ分類: 開Epic3件は全て直近runがレビュー待ちのため executable=0（waiting_user 2 / review_waiting 1）。**野鳥BirdLog(P1)はwaiting_userに分類され次回候補に出ない=野鳥問題の解消を確認**。
- 安全性: pin round-trip検証で、pinはgatingを上書きせず（BirdLogはpinしてもwaiting_userのままでexecutable/nextにならない）。
- 受け入れテスト §14.5 の1〜9合格（実機375px視覚確認のみ未実施）。
- 機密スキャン: OK。ob sync: 失敗（サブスク期限切れ）。push: 89fc8b0 済み。

## 5. 未対応

- iPhone実機での /queue 4操作・トップ表示の視覚確認（curlではclient条件描画を見られない）。
- manualOrder stickiness（下記6）。
- factoryEligible未設定Epicの扱い・goal_todoの危険gating迂回（下記6）。

## 6. 危険ポイント / レビュー指摘（非ブロッキング）

1. **manualOrder stickiness（中）**: moveUp/moveDownが全executable epicにmanualOrderを採番するため、一度並べ替えると manualOrder無しの新規高優先Epic（P0等）がブロックの下に沈む（compareItemsでmanualOrder有>無）。改善案=移動item＋隣接のみ採番、または priority変更時にmanualOrderクリア。
2. **factoryEligible未設定(None)の分類**: fe未設定Epicは rule2(fe===falseのみmanual)を通過し、直近runがレビュー待ちだと review_waiting 化する。opt-out として manual/対象外へ寄せるか要判断。
3. **goal_todoの危険gating迂回（小）**: goal_todoは decisionPolicy=autonomous固定・riskFlags無しでexecutable化しうる。現状該当データ無しだが、危険todoが将来出た場合の安全確認が必要。

変更禁止範囲（Project/Revenue/運用/Legacy主要導線・作業履歴の物理削除・既存スキーマ破壊・自動実行仕様の勝手な大幅変更）は不変更。既存 /api/queue・旧コンポーネントは削除せず /legacy/queue に退避。

## 7. 次にやるべきこと

- iPhone実機確認。
- manualOrder stickiness の改善（次イテレーション）。
- factoryEligible未設定の扱い方針をユーザー判断。
- 後回し範囲（§12: Project boost / ドラッグ並べ替え / review_waiting importance精緻化 / Factory本体ループ）。

## 8. ChatGPT レビュー依頼文

```
対象: Progress アプリ（apps/ny01/progress）自動実行キュー(/queue) MVP実装
runId: 20260613-150148 / commit: 89fc8b0 / 設計: docs/auto-execution-queue-design.md
新規: lib/auto-queue.ts, lib/auto-queue-score.ts, types/auto-queue.ts,
  app/api/auto-queue/{route,control/route}.ts, app/api/goals/[goalId]/priority/route.ts,
  app/queue/page.tsx ほか

背景: 司令塔の「次にやること」が未整理/低関連案件（野鳥観察系）を先頭に出していた。
原因はwork-queue.jsonとEpic Contractの二重正本。Epic=実行正本・キュー=派生ビューに統一した。

レビューしてほしい観点:
1. manualOrder stickiness: moveで全executable epicにmanualOrderを採番する設計は妥当か。新規高優先Epicが沈むリスクの解消案。
2. factoryEligible未設定(None)のEpicを review_waiting にする現挙動は妥当か（manual/対象外へ寄せるべきか）。
3. goal_todoが危険gating(riskFlags/decisionPolicy)を通らずexecutable化しうる点の安全性。
4. pinがgatingを上書きしない設計（pinしてもwaiting_userはexecutableにならない）で意図通りか。
5. executable=0で「次が空」を正直に出すUXは、毎日の判断運用として機能するか。
6. MVP範囲の切り方（§11）と後回し（§12）の妥当性。
```
