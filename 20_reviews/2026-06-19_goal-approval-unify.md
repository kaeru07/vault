---
date: 2026-06-19
task: ゴール追加を「追加→承認」に統一＋承認カードを詳細化(できること/メリット/デメリット)
runId: 20260619-084033
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260619-000632, 20260619-071433]
commitHashes: [ed43306]
---

# 2026-06-19 ゴール追加の承認統一＋承認カード詳細化

> ユーザー指示: 追加したら基本的にゴール承認タブで承認ボタンを押したら追加されるようにして欲しい。承認タブでは、できるようになることやメリット・デメリットなどを詳細に書いておいて。

---

## 1. 作業目的

- ゴールの追加経路を「追加＝即active」から「追加→ゴール承認タブで承認→active」に統一する（手動追加もAI提案も同じ承認ゲートを通す）。
- 承認タブのカードに「できるようになること・メリット・デメリット」を詳細表示し、承認判断をしやすくする。

---

## 2. 実施内容

- **追加→承認の統一**: `GoalPlannerForm` の「ゴールを直接追加」を `upsertSingle`（即active）から **`POST /api/goals/propose`（source='manual'・status='proposed'）** に変更。手動追加もゴール承認タブに候補として入り、承認ボタンで初めて active になる。
- **承認カードの詳細化**: `Goal` に `proposalEnables` / `proposalPros[]` / `proposalCons[]` を追加（types/goal.ts）、`ProposeGoalInput`＋`proposeGoals`（lib/goal-writer.ts）で反映。`buildResearchGoalCandidates`（lib/research-goals.ts）が research 候補に enables/pros/cons を自動付与。`buildInbox`（lib/command-center.ts）の proposedGoals カードを **✅できるようになること / 👍メリット / 👎デメリット・注意 / 承認すると / やめると** の詳細表示に拡張（無い場合は既定文）。`InboxActions` の行 `dd` に `whitespace-pre-line` を付与し箇条書き改行を保持。ボタン文言「承認して追加する」。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/types/goal.ts | proposalEnables/Pros/Cons 追加 |
| progress/lib/goal-writer.ts | ProposeGoalInputにenables/pros/cons・proposeGoalsで反映 |
| progress/lib/research-goals.ts | 研究候補にenables/pros/cons自動付与 |
| progress/lib/command-center.ts | 承認カードを詳細表示に拡張・既定文 |
| progress/components/newux/InboxActions.tsx | row ddにwhitespace-pre-line |
| progress/components/goals/GoalPlannerForm.tsx | 直接追加をpropose(source=manual)へ・文言更新 |
| progress/app/guide/page.tsx | FAQを追加→承認の統一に更新 |
| progress/docs/operations/current-operating-model.md | 追加経路統一・承認カード詳細・frontmatter・変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0） / build: OK（next build 0）
- 手動 propose API E2E: `POST /api/goals/propose`(source=manual) → status=proposed・source=manual・proposalEnables 反映を実測。
- `.next/server` ビルドに新カード文字列（できるようになること / デメリット・注意 / 承認して追加する / ゴール承認へ追加）含有を確認。
- `/goal-planner` `/decide` 200。テストゴール削除済み。
- 注意: ゴール承認タブは client 描画のため SSR の curl grep では emoji 付きラベルが検出しづらい（build 内文字列＋データフロー E2E で確認）。実機 headless での最終描画確認は未。
- ob sync: n/a / git push: ed43306

---

## 5. 未対応

- ゴール承認カードのメリット/デメリット描画の実ブラウザ最終確認は未（build 文字列＋E2Eで代替）。
- JSON一括追加は現状 import で直 active（proposed 経由にしていない）。統一するか要判断。
- 既に active 化済みの旧候補（OpenClawを試す 等）はそのまま正式ゴール（遡及変更なし）。
- 候補タイトル・メリデメ文の質は今後の調整対象。

---

## 6. 危険ポイント

- 影響: 中（追加の既定挙動が active→proposed に変化）。ただし承認で従来どおり active になり、proposed は queue/目標タブ非表示で誤実行しない。`upsertSingle` API は残置（後方互換・未使用化）。
- `whitespace-pre-line` は全 InboxCard の行に適用（改行を含むテキストのみ影響、既存カードは改行なしで無影響）。
- ロールバック: commit ed43306 の revert。

---

## 7. 次にやるべきこと

- 実機でゴール承認カードのメリット/デメリット描画確認。
- JSON一括追加も承認経由にするかの判断。
- 候補文言の品質改善。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: ゴール追加を「追加→承認」に統一＋承認カードを詳細化(できること/メリット/デメリット)
runId: 20260619-084033
日付: 2026-06-19
GitHub commit: ed43306

## 実施内容
- 手動の直接追加もstatus=proposedで作成(POST /api/goals/propose source=manual)し、ゴール承認タブの承認ボタンで初めてactiveに統一。
- GoalにproposalEnables/Pros/Consを追加、研究候補は自動付与。承認カードを✅できること/👍メリット/👎デメリット/承認すると/やめるとに詳細化。InboxActionsにwhitespace-pre-line。

## 検証
- tsc0/build0。手動propose E2E(proposed/source=manual/enables反映)。.next内新文字列確認。

## 未対応
- 承認カード描画の実ブラウザ確認は未(build文字列+E2E代替)。JSON一括追加は直active。既存active候補は遡及変更なし。

## 確認したい観点
- 追加=即active を 追加→承認 に変えた設計の妥当性(手動追加も承認必須)
- 承認カードの情報設計(できること/メリット/デメリット)の十分性・自動生成文の質
- JSON一括追加も承認経由にすべきか
- proposed/activeの扱いとqueue非表示の安全性
````

---

## 関連

- progress runId: 20260619-084033（正本）
- 関連: lib/command-center.ts(proposedGoals) / lib/research-goals.ts / components/goals/GoalPlannerForm.tsx / [[2026-06-19_goal-approval-tab]]
- 関連アプリ: [[../02_apps/progress]]
