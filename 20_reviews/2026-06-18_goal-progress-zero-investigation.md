---
date: 2026-06-18
task: 調査 — 自動実行のゴール進捗が0%になる原因解析と対応方針
runId: 20260618-012023
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260617-134311, 20260618-005653]
commitHashes: []
---

# 2026-06-18 自動実行のゴール進捗が0%になる原因解析

> 調査のみ（ファイル変更なし）。原因の特定と、すべきことの整理。

---

## 1. 作業目的

- なぜ: /queue・Goal Planner で全ゴールの進捗が 0% と表示される理由を特定し、対応方針を出す。

---

## 2. 実施内容（調査）

- 進捗計算の実装を確認: `goalAchievement`（lib/goal-reader.ts:99）と `buildGoalProgress`（lib/auto-queue.ts:328-371）。
- 実データ `data/real/goals.json` を確認。
- ライブ `/api/auto-queue` の goalProgress 実値を確認。
- 表示経路 `app/goal-planner/page.tsx:195` / `components/goals/GoalListItem.tsx:23` / `/queue` を確認。

---

## 3. 根本原因

**表示される進捗 `buildGoalProgress().ratio` は `done/total` で、`total`/`done` は「todos＋キューアイテム数」だけを数える。`goal.target/current`（数値指標）を一切使わない。**

- 4ゴールはすべて **todos=0**、`target/current` で管理:
  - goal-ai-factory-os: target15 / current60（数値なら 400%→cap100%）
  - goal-execution-review-loop: 80 / 10（→13%）
  - goal-ai-executor-ops: 90 / 30（→33%）
  - goal-value-validation-pipeline: 5 / 0（→0%）
- 開いている Epic は `status==='done'` にならず、完了/クローズ Epic は items から除外される → `done≒0`。
- したがって `ratio = done/total ≒ 0` → **全ゴール常に 0%**。
- ライブ実測（/api/auto-queue）: 全4ゴール `ratio:0`。

**乖離**: `target/current` を使う `goalAchievement()` は存在するが、表示 ratio に使われていない。運用ドキュメントは「todoあれば todo完了率 / なければ紐付くEpic平均 / それもなければ数値指標」と明記しているが、**「数値指標(target/current)」フォールバックが未実装**。

**二重計算の不整合**: `goalAchievement`（target/current）と `buildGoalProgress`（todos+items）が併存し、表示は後者（0%）。`app/goal-planner/page.tsx:195` と `GoalListItem.tsx:23` は `queueProgress?.ratio ?? calcGoalProgress().ratio` で、どちらも target/current 不使用。

---

## 4. すべきこと（対応方針）

1. **主因の修正**: `buildGoalProgress` を運用ドキュメント通りのフォールバックにする。
   - todos>0 → todoDone/todoTotal
   - 紐付くEpicあり → Epic の doneCriteria 完了率の平均（`doneCriteriaDone/doneCriteriaTotal`。`status==='done'` の件数ではなく、開いてるEpicの進捗も反映）
   - どちらも無し → `goalAchievement(goal)`（target/current の数値指標）
2. **進捗計算の単一化**: /queue・Goal Planner・command-center が1つの進捗関数を共有し、`goalAchievement` vs `buildGoalProgress` の二系統を解消。
3. **データ是正**: goal-ai-factory-os の current60 > target15（cap で100%）。target/current のスケール不整合を直す。
4. **方針確定（設計判断）**: 進捗の「正本」を *数値指標(target/current)* とするか *作業完了ベース* とするか。両者が大きく乖離するため、どちらを主にするか決める必要がある（ドキュメントは「数値指標は最終フォールバック」＝作業完了優先の立場）。

---

## 5. 未対応

- 修正の実装は未着手（本作業は調査のみ）。実装可否・進捗正本の方針はユーザー判断。

---

## 6. 危険ポイント

- 進捗計算を変えると、表示が大きく動く（例: goal-ai-factory-os が 0%→100%）。ユーザーの期待とズレないよう「正本」を先に決めるべき。
- データ修正（target/current）は値の意味づけを伴うためユーザー確認推奨。

---

## 7. 次にやるべきこと

- 上記「4. すべきこと」1の主因修正を実装するか判断。
- 進捗の正本（数値指標 vs 作業完了）を確定。
- 確定後、進捗関数を単一化して全画面に適用。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の調査報告です。レビューしてください。

対象アプリ: ny01/progress
作業: 自動実行のゴール進捗が0%になる原因解析
runId: 20260618-012023
日付: 2026-06-18

## 根本原因
- 表示進捗 buildGoalProgress().ratio は done/total(todos＋キューアイテム数のみ)で算出し、goal.target/current(数値指標)を使わない。
- 4ゴールは全て todos=0・target/current管理。開いてるEpicはdoneにならず完了Epicはitemsから除外→done≒0→全ゴール常に0%。
- target/currentを使うgoalAchievement()は存在するが表示に未使用。運用docの「数値指標フォールバック」が未実装。

## すべきこと
1. buildGoalProgressをdoc通りのフォールバックに(todo完了率→Epic doneCriteria完了率平均→goalAchievement)
2. Epic進捗はdone件数でなくdoneCriteria完了率平均で算出(開いてるEpicも反映)
3. 進捗計算を単一関数に集約し二系統の不整合解消
4. データ是正(goal-ai-factory-os current60>target15)
5. 進捗の正本(数値指標 vs 作業完了)の方針確定

## 確認したい観点
- 進捗の正本をどちらにすべきか(数値指標 vs 作業完了)
- フォールバック設計の妥当性
- 単一化の設計(どの関数を正本にするか)
- target/currentの意味づけ・データ運用
````

---

## 関連

- progress runId: 20260618-012023（正本）
- 関連: lib/auto-queue.ts:328-371（buildGoalProgress）/ lib/goal-reader.ts:99（goalAchievement）/ app/goal-planner/page.tsx:195
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
