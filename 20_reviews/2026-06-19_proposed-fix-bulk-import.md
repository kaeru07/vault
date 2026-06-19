---
date: 2026-06-19
task: 【重大修正】proposed読込時のactive矯正バグ修正＋ChatGPT一括取込＋手動追加に任意プロンプト
runId: 20260619-125513
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260619-084033, 20260619-085000]
commitHashes: [53c6dc8]
---

# 2026-06-19 proposed読込バグ修正＋ChatGPT一括取込

> ユーザー報告「todoのゴール判断にみつかりません」「openclew等を承認前に戻して」＋要望「ChatGPTからゴール+todoを一括追加できるプロンプトのコピーボタン／それに耐える仕組み／手動追加に任意プロンプト」。

---

## 1. 作業目的・背景

- 承認タブに提案ゴールが出ない原因を突き止めて直す。
- ChatGPT出力のJSONでゴール＋ToDoを一括追加できるようにする（堅牢に）。
- 手動追加に任意のプロンプト欄を戻す。

---

## 2. 根本原因（重大バグ）

`lib/goal-reader.ts` の `normalizeGoal` が `VALID_STATUSES`（`['active','paused','done','dropped','archived']`＝**'proposed' 未含有**）で status を検証し、未含有なら `'active'` に矯正していた。そのため **goals.json に proposed と書いても、読込時に active に変換**され、
- ゴール承認タブ（`status==='proposed'` でフィルタ）が常に空＝「みつからない」
- 承認ゲートが実質素通り（提案＝即active相当）
になっていた。`VALID_STATUSES` に `'proposed'` を追加して修正。

---

## 3. 実施内容

- **修正**: `lib/goal-reader.ts` `VALID_STATUSES` に `'proposed'` 追加。
- **一括取込の堅牢化**: `lib/goal-writer.ts` `validateGoalImport`/`importGoal` で projectId を任意化（未存在は警告のみ）、phases 未指定時は既定フェーズを自動生成して todos を紐づけ。`app/api/goals/route.ts` を `{goals:[...]}`/配列/単一すべて受ける複数取込に拡張（集約レスポンス: createdCount/phaseCount/todoCount/warnings）。
- **ChatGPTボタン**: `GoalPlannerForm` に「📋 ChatGPT用プロンプトをコピー」ボタン＋ゴール+ToDo生成テンプレ（既存案件一覧を埋め込み）。複数取込結果（createdCount）表示対応。
- **手動追加**: 説明欄を「プロンプト・説明(任意)」に。

---

## 4. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/goal-reader.ts | VALID_STATUSESに proposed 追加（重大バグ修正） |
| progress/lib/goal-writer.ts | import: projectId任意・phases自動生成 |
| progress/app/api/goals/route.ts | 複数ゴール取込（{goals:[...]}/配列/単一） |
| progress/components/goals/GoalPlannerForm.tsx | ChatGPT用プロンプトのコピーボタン＋テンプレ・複数取込結果・任意プロンプト |
| progress/app/guide/page.tsx | FAQにChatGPT一括手順 |
| progress/docs/operations/current-operating-model.md | 重大修正/一括取込/frontmatter/変更履歴 |

---

## 5. 検証結果

- typecheck: OK（tsc 0） / build: OK（next build 0）
- バグ修正実測: 修正前 `/api/auto-queue` goalProgress=7（proposed3件も active扱い）→ 修正後=4（proposed3件除外）。`/decide?tab=goalApproval` approve URL=6（3件×2）・できること/メリット/デメリット描画。
- 一括取込 E2E: `{goals:[A,B]}` → createdCount=2 / todo=3 / projectId任意（Aは案件なし）/ phases自動生成（各1）/ 未存在projectIdは警告のみで取込成功（テストゴール削除）。
- `/goal-planner` に「ChatGPT用プロンプトをコピー」「プロンプト・説明」描画。gp/decide 200。
- ob sync: n/a / git push: 53c6dc8

---

## 6. 未対応・注意

- ChatGPT一括取込（JSON import）は現状 importGoal 経由で **直 active**（手動の直接追加は proposed 経由だが、JSON一括は明示バルクのため active）。承認経由に統一するかは要判断。
- 承認カードのメリット/デメリット描画の実機最終確認は未（build文字列＋E2E代替）。
- 既に手動で承認前へ戻した3件（OpenClaw等）は proposed のまま承認タブに表示中。

---

## 7. 危険ポイント

- VALID_STATUSES修正は読込挙動を変える（これまで active 扱いだった proposed が正しく proposed になる）。これにより「以前は active だった提案ゴールが proposed に見える」状態になるが、これが本来の正しい挙動。
- import の projectId/phases 緩和で不正データも取り込まれやすくなる（warning で通知）。
- ロールバック: commit 53c6dc8 の revert。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: 【重大修正】proposed読込時のactive矯正バグ修正＋ChatGPT一括取込＋手動追加に任意プロンプト
runId: 20260619-125513
日付: 2026-06-19
GitHub commit: 53c6dc8

## 根本原因
- goal-readerのnormalizeGoalがVALID_STATUSESに'proposed'を含まず、読込時にproposed→activeへ矯正。ゴール承認タブが常に空・承認ゲート素通りだった。VALID_STATUSESにproposed追加で修正。

## 実施内容
- 一括取込: validateGoalImport/importGoalでprojectId任意・phases未指定時に既定フェーズ自動生成。/api/goals routeを{goals:[...]}/配列/単一受けに拡張。
- GoalPlannerFormにChatGPT用プロンプトのコピーボタン＋ゴール+ToDo生成テンプレ。手動追加に任意プロンプト。

## 検証
- tsc0/build0。修正前goalProgress=7→修正後4。/decide?tab=goalApproval approveURL=6。複数ゴール一括E2E(2件/todo3/projectId任意/phases自動)。

## 未対応
- JSON一括は直active(手動直接追加はproposed経由)。承認カード実機確認は未。

## 確認したい観点
- VALID_STATUSES漏れの再発防止(GoalStatus定義とreader/writerのVALID配列を単一ソース化すべきか)
- 一括取込を承認(proposed)経由に統一すべきか
- import緩和(projectId/phases任意)の妥当性とバリデーション
- ChatGPTプロンプトテンプレの出力安定性
````

---

## 関連

- progress runId: 20260619-125513（正本）
- 関連: lib/goal-reader.ts(VALID_STATUSES) / lib/goal-writer.ts / app/api/goals/route.ts / [[2026-06-19_goal-approval-unify]]
- 関連アプリ: [[../02_apps/progress]]
