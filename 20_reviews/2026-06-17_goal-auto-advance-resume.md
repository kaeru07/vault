---
date: 2026-06-17
task: ToDo/Epic無しの未達成Goalを「達成まで自動で進める」(type=goal + 次の一歩Epic自動生成) — 中断作業の再開・完了
runId: 20260617-134311
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260617-010618, 20260616-225004]
commitHashes: [13766cb]
---

# 2026-06-17 ToDo/Epic無しの未達成Goalを「達成まで自動で進める」

> 1 作業 = 1 ファイル の自動生成レビュー。
> 前セッションで未コミット・未検証だった機能を再開して検証・完成させた。

---

## 1. 作業目的

- なぜこの作業をするのか: 前セッションで progress 作業ツリーに未コミットで残っていた「goal-resume」機能（todo も Epic も無い目標を自動実行キューに載せ、Factory が次の一歩を自動生成する）を、検証して完成・コミットするため。
- 背景: ユーザー指示「中断作業再開」。直近 run 20260617-010618（Goal順）は commit 済みだが、その後 auto-queue / factory-runner などに未コミットの変更と新規ファイル `lib/goal-step-epic.ts` が残っていた。
- 期待効果: 「目標を置いただけで配下に作業が無い」状態でも AI 工場が止まらず、目標達成に向けて自走する。

---

## 2. 実施内容

- 中断作業の特定: progress repo の git status / diff から、未コミットの goal-resume 機能を復元。新規 `lib/goal-step-epic.ts`（68行）＋ auto-queue/factory-runner/types の変更が整合していることを確認。
- 主な変更（既存・前セッション分の確定）:
  - `AutoQueueItem.type` に `'goal'` を追加。
  - `buildAutoQueue()` で「ToDo も open Epic も無い未達成 active Goal（達成率<100%）」を `type='goal'`・「○○（達成まで自動で進める）」として items に追加（`toGoalItem()`/`goalPriority()`）。
  - `runFactory()` が auto+confirm かつ実行可能 Epic 0 件のとき `ensureNextGoalStepEpic()` を呼び、Goal順最上位の対象 Goal に「次の一歩」Epic `epic-goalstep-<goalId>` を1つだけ生成して再 scan。自動化ログ `factory_goal_step_epic_created` を記録。
  - `/queue` で `type='goal'` でも pin/あとで(hold) 操作を許可。
- 今回追加した運用ドキュメント4点セット（ny01 ルール）:
  - guide FAQ「ToDoも大きな作業も無い目標は自動で進む？」追加。
  - `command-center.ts` TERMS に `goalStepEpic`（達成まで自動で進める／次の一歩）追加。
  - operating-model.md の「自動実行キューの使い方」「AI工場のパイプライン」「用語対応表」更新。
  - frontmatter `updated`/`updateNote` ＋ 変更履歴 追加。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/types/auto-queue.ts | `AutoQueueItem.type` に `goal` 追加 |
| progress/lib/auto-queue.ts | `toGoalItem`/`goalPriority` 新設・未達成Goalをキュー投入 |
| progress/lib/goal-step-epic.ts | 新規。`ensureNextGoalStepEpic`/`isAutoAdvanceGoal`。次の一歩Epicをidempotentに自動生成 |
| progress/lib/factory-runner.ts | 実行可能Epic0件時に `ensureNextGoalStepEpic` を呼び再scan |
| progress/lib/types/operations.ts | `AutomationLogEntry.event` に `factory_goal_step_epic_created` 追加 |
| progress/app/queue/page.tsx | `type='goal'` でもpin/hold操作許可 |
| progress/lib/command-center.ts | TERMS に `goalStepEpic` 追加 |
| progress/app/guide/page.tsx | FAQ追加 |
| progress/docs/operations/current-operating-model.md | 運用モデル本文/用語/frontmatter/変更履歴 更新 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit 0）
- build: OK（next build 0）
- lint: n/a
- 手動確認: pm2 restart 後 `/queue` `/guide` 実描画 HTTP200。`/queue` に `type='goal'` の「達成まで自動で進める」6件がライブ表示・参照JSチャンク200・白画面/エラーマーカーなし。`/guide` に新FAQ・新用語が描画（次の一歩×10 / 達成まで自動で進める×8）。
- 機密パターン事前チェック: OK（ヒットは riskFlag enum / 危険語検出キーワードのみ。実値なし）
- ob sync: n/a（ob sync 一旦中止中）
- git push: 13766cb（origin/main へ push 済み）

---

## 5. 未対応

- step-epic の生成は **ライブ未観測**（現状、実行可能 Epic が存在するため auto 実起動で fallback 経路に到達していない）。次回定時自動実行で実行可能 Epic 0 件になったときに `factory_goal_step_epic_created` ログと `epic-goalstep-<id>` 生成を観測する必要がある。
- 同じ progress 作業ツリーには、本作業と無関係な未コミット変更（verify-todos / inbox-review-copy / GoalTodoAddForm / 別アプリ ai-trend-sns 等、過去の完了扱い run の残置）が残っている。本作業では触らず残置した（別途棚卸し要）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。追加は加算的（新 type / 新ファイル / fallback 1分岐）。`type='goal'` アイテムは「既に作業 item を持つ Goal には出さない」ガードあり。Factory への影響は「実行可能 Epic 0 件のとき**だけ**」かつ auto+confirm 経路に限定（read 経路・dry_run・manual では epic を作らない）。
- DB / 認証 / 本番 / 機密に触れたか: なし。
- ロールバック手段: commit 13766cb の revert。
- 観察すべき項目: step-epic が無限に増えないか（idempotent ガード=open epic を持つ Goal は対象外＝1 Goal 同時1つ。step-epic が done でも Goal 未達成なら次回また生成して達成まで繰り返す設計が意図通りか）。承認要/手動/危険 Goal が誤って自動実行されないか。

---

## 7. 次にやるべきこと

- フォローアップ作業: 次回定時自動実行（11/14/16/23時）で実行可能 Epic 0 件になったタイミングを捉え、step-epic 自動生成のライブ動作を確認。
- ユーザー判断待ち事項: なし（前進優先ルールで commit/push 自動実施済み）。
- 観察項目: step-epic 生成ループの収束性（Goal 達成度の前進と1 Goal 1 step-epic の維持）。
- 関連 ToDo の追加候補: progress 作業ツリーに残る未コミット残置（verify-todos 等）の棚卸し・コミット可否判断。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: ToDo/Epic無しの未達成Goalを「達成まで自動で進める」(type=goal + 次の一歩Epic自動生成) — 中断作業の再開・完了
runId: 20260617-134311
日付: 2026-06-17
GitHub commit: 13766cb

## 作業目的
- 前セッションで未コミット・未検証で中断していた「目標に紐づく作業(ToDo/Epic)が無くても、AI工場が次の一歩を自動生成して目標達成まで自走する」機能を検証・完成・コミットした。

## 実施内容
- AutoQueueItem.type に 'goal' を追加。buildAutoQueue で ToDo も open Epic も無い未達成 active Goal を type='goal'「○○(達成まで自動で進める)」としてキューに投入。
- runFactory が auto+confirm かつ実行可能 Epic 0 件のとき ensureNextGoalStepEpic を呼び、Goal順最上位の対象 Goal に 次の一歩 Epic(epic-goalstep-<id>) を1つだけ idempotent に生成して再scan。承認要/手動/危険 Goal は自動実行せず解消手順を表示。
- 運用ドキュメント4点(guide FAQ / TERMS / operating-model本文 / frontmatter+変更履歴)更新。

## 変更ファイル
- types/auto-queue.ts, lib/auto-queue.ts, lib/goal-step-epic.ts(新規), lib/factory-runner.ts, lib/types/operations.ts, app/queue/page.tsx, lib/command-center.ts, app/guide/page.tsx, docs/operations/current-operating-model.md

## 検証結果
- typecheck / build: tsc0 / next build0
- 手動確認: /queue /guide 実描画200・goalアイテム6件ライブ表示・白画面なし・新FAQ/用語描画
- 機密スキャン: clean(riskFlag enum/危険語キーワードのみ)

## 未対応
- step-epic の自動生成はライブ未観測(現状 実行可能Epicがあるため fallback 未到達)。次回 実行可能Epic 0 件時に観測予定。
- 同作業ツリーに無関係な未コミット残置あり(本作業では触らず)。

## 危険ポイント
- 追加は加算的・fallback は「実行可能Epic 0件のときだけ」かつ auto+confirm 限定。read経路/dry_run/manualでは生成しない。

## 確認したい観点
- step-epic の無限増殖防止(idempotentガード=1 Goal 同時1つ)が設計通りに効くか
- 承認要/手動/危険 Goal が type=goal で出ても自動実行されない安全設計が妥当か
- 「実行可能Epic 0件のときだけ次の一歩を作る」トリガ条件の妥当性(飢餓/暴走の両リスク)
- 既存の Goal順・自走化アンカー(goal-ai-factory-os)との優先順位整合
- 収益化インパクトの評価は妥当か(low)
````

---

## 関連

- progress runId: 20260617-134311（正本）
- 関連 run: 20260617-010618（Goal順）/ 20260616-225004（Goal/Todo/Queue統合）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
