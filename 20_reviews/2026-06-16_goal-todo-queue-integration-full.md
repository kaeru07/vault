---
date: 2026-06-16
task: Goal/Todo/Queue/JSON取込のデータ統合（フル・8項目）中断実装の検証＋確定
runId: 20260616-225004
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260616-184355, 20260615-001449]
commitHashes: [452f1eb]
---

# 2026-06-16 Goal/Todo/Queue/JSON取込のデータ統合（フル・8項目）中断実装の検証＋確定

> 1 作業 = 1 ファイルの自動生成レビュー。

---

## 1. 作業目的

- なぜこの作業をするのか: 「途中で中断したタスクを再開して」という指示。中断タスクの実体を特定し完了させる。
- 背景: 実装前監査(runId 20260616-184355)で「Todo手動追加がGoal非紐づけ＝二重Todo系統(GoalTodo埋込 vs project-tasks)が分断の主因」と判明し、スコープがユーザー判断待ちで中断。調査の結果、監査後にフル統合実装が**未コミット作業ツリーに着手済み・未検証**で止まっていたことを確認。フル(8項目統合)スコープをユーザーが承認。
- 期待効果: Todoを選択Goalに紐づけ、Goalを正本に寄せて二重系統を解消。QueueにtodoId/sourceを持たせ追跡可能にする。

---

## 2. 実施内容

- 主な変更:
  - types: Task/NewTaskInput に `goalId`/`phaseId`、GoalTodo に `source`/`queueControl`/`decisionPolicy`/`riskFlags`/`dueHint`、Goal に `decisionPolicyDefault`/`riskFlagsDefault`/`notes`、AutoQueueItem に `todoId`/`source` enum、AutoQueueControlAction に `prioritize`/`complete` を追加。
  - lib: progress-writer(goalId/phaseId永続化・update対応) / goal-reader(新フィールド正規化) / goal-writer / auto-queue / command-center(用語追加) を統合フィールド対応。
  - api: tasks・tasks/[taskId]・goals・auto-queue/control(prioritize/complete)・execution-runs を対応。
  - UI: TodoManager / JsonImportManager / GoalPlannerForm / GoalListItem / InboxTabs / queue / tasks / tasks/import を Goal-linked Todo 追加に対応。
- 関連調査・判断:
  - 作業ツリーには本作業のコードに加えランタイムデータ蓄積(execution-runs.json +4172行等)・別アプリ(ai-trend-sns/news-app)の変更が混在。**本統合タスクのソース22ファイルのみ選択コミット**し、データ系/別アプリは対象外として残置。
  - 変更は加算的（フィールド追加中心）で既存データと後方互換。破壊的スキーマ変更ではない。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| types/{goal,auto-queue,progress}.ts | 統合フィールド追加(goalId/phaseId/todoId/source/queueControl/decisionPolicy等) |
| lib/{progress-writer,goal-reader,goal-writer,auto-queue,command-center}.ts | 永続化・正規化・キュー連携・用語追加 |
| app/api/{tasks,tasks/[taskId],goals,auto-queue/control,execution-runs}/route.ts | goalId/phaseId受理・control(prioritize/complete)対応 |
| app/{goal-planner,queue,tasks,tasks/import}/page.tsx | Goal-linked Todo追加UI |
| components/{goals/GoalListItem,goals/GoalPlannerForm,tasks/JsonImportManager,tasks/TodoManager,newux/InboxTabs}.tsx | Goal紐づけ・取込UI |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit, 0 errors）
- build: OK（next build, exit 0・全ルートコンパイル）
- lint: n/a（未実行・tsc/buildで代替）
- 手動確認: pm2 restart 後 /queue /tasks/import /goal-planner HTTP200 + 参照JSチャンク200（白画面なし）。/tasks も200。
- 機密パターン事前チェック: OK（ヒットは `'auth_secret'` riskFlag enum のみ・実値なし）
- ob sync: n/a（ob sync 一旦中止中）
- git push: 452f1eb（origin/main push済）

---

## 5. 未対応

- ny01ドキュメントセット更新ルールの残り: `guide/page.tsx` と `docs/operations/current-operating-model.md` の追補（本コミットでは未対応＝ルール上のフォローアップ）。
- Goal-linked Todo追加のAPI機能スモーク（POST /api/tasks に goalId付与→反映確認）は実データ汚染回避のため未実施。
- ランタイムデータ蓄積(execution-runs.json等)・別アプリ(ai-trend-sns/news-app)の未コミット変更は本タスク対象外として残置。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。フィールド追加中心で後方互換。auto-queue派生ロジックは表示層、判定系は不変の想定。
- DB / 認証 / 本番 / 機密: 触れていない（ローカルJSONデータのみ。Vercel/Supabase本番反映なし）。
- ロールバック手段: `git revert 452f1eb`（単一コミット）。
- 観察すべき項目: Goal-linked Todoが goals.json / project-tasks.json のどちらに書かれるか、Queue上で todoId/source が正しく付くか。

---

## 7. 次にやるべきこと

- フォローアップ: guide/page.tsx + current-operating-model.md の更新（ny01ドキュメントセット4点ルール）。
- ユーザー判断待ち事項: なし（フルスコープ承認済）。
- 観察項目: 数日運用してTodo追加→Goal紐づけ→Queue反映が一貫しているか。
- 関連ToDo候補: API機能スモークテストの追加。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: Goal/Todo/Queue/JSON取込のデータ統合（フル・8項目）中断実装の検証＋確定
runId: 20260616-225004
日付: 2026-06-16
GitHub commit: 452f1eb

## 作業目的
- 中断していたGoal/Todo/Queue統合実装(未コミット・未検証)を、フルスコープで検証し確定する。Todoを選択Goalに紐づけ二重Todo系統を解消。

## 実施内容
- Task/NewTaskInput に goalId/phaseId、GoalTodo に source/queueControl/decisionPolicy/riskFlags、AutoQueueItem に todoId/source、control に prioritize/complete を追加。lib/api/UI を統合フィールド対応。

## 変更ファイル
- types/{goal,auto-queue,progress}.ts、lib/{progress-writer,goal-reader,goal-writer,auto-queue,command-center}.ts、app/api/{tasks,goals,auto-queue/control}、app/{goal-planner,queue,tasks,tasks/import}、components/{goals,tasks,newux}/* （計22ファイル）

## 検証結果
- typecheck OK / build OK / lint n/a
- 手動確認: pm2 restart後 /queue /tasks/import /goal-planner 実描画200+チャンク200（白画面なし）
- 機密スキャン: clean

## 未対応
- ny01ドキュメントセット(guide/page.tsx, current-operating-model.md)の追補
- API機能スモーク(goalId付与→反映)

## 危険ポイント
- フィールド追加中心で後方互換。本番・DB・機密には触れていない。

## 次にやるべきこと
- ドキュメントセット更新、API機能スモークテスト

## 確認したい観点
- 二重Todo系統の解消が設計として妥当か（Goal正本に寄せる方針）
- 後方互換が本当に保たれているか（既存project-tasks.jsonのTodo）
- Goal-linked Todoがgoals.json/project-tasks.jsonどちらに書かれるべきか
- 既存機能を壊していないか
- 収益化インパクトの評価は妥当か（medium）
````

---

## 関連

- progress runId: 20260616-225004（正本）
- 関連 run: 20260616-184355（実装前監査）, 20260615-001449（Goal Plannerと自動実行キューのデータ統合）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
