---
date: 2026-06-18
task: ゴール提案→承認→自動実行フローを新設（自動実行中AI提案＋Inbox承認＋自動実行履歴）
runId: 20260618-125718
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260617-134311, 20260618-085742]
commitHashes: [c91e5ab]
---

# 2026-06-18 ゴール提案→承認→自動実行フロー新設

> ユーザー指示（段階的明確化）: ゴールを抽出して承認したら自動実行 / 自動実行中にAIが提案 / 承認した次回以降 / 自動実行の履歴をInboxに。
> 確認で選択: 抽出元=自動実行中にAIがその場で考えて提案、承認=今日の判断(Inbox)。

---

## 1. 作業目的

- 自動実行中にAIが「次に目指すゴール」を提案し、人間がInboxで承認したものだけを、次回以降の自動実行対象にする仕組みを作る。
- あわせて「自動実行で何が動いたか」をInboxで把握できるようにする。

---

## 2. 実施内容

- **提案**: Factory が auto+confirm でアイドル（実行可能Epicも auto-advance Goal も無い）のとき、`requestGoalProposalIfIdle()`（lib/goal-proposal.ts）が提案依頼プロンプトを生成し、自動化ログ `factory_goal_proposal_requested` に残す。実候補生成はAI実行者(Claude/Codex)が依頼に従い `POST /api/goals/propose` を呼ぶ前提（Factoryは依頼発行まで）。承認待ち3件で打ち止め。
- **登録**: `proposeGoals()`（lib/goal-writer.ts）が `status='proposed'`/`decisionPolicyDefault='autonomous'` で登録（同名 active/proposed はskip）。
- **承認**: `buildInbox().proposedGoals` → 今日の判断(Inbox)上部「🎯 ゴール承認」カード（capしない）。承認→`POST /api/goals/[id]/approve {approve:true}`→`setGoalApproval`で `status='active'`。却下→`dropped`。
- **自動実行**: active+autonomous になったゴールは既存 `ensureNextGoalStepEpic()` が次回 Factory 実行で「次の一歩」Epic を自動生成し達成まで進める。
- **自動実行の履歴**: `buildInbox().autoRuns`（factoryRun/source=factory/schedule/boot の直近10件）→ Inbox下部「🤖 自動実行の履歴」（情報表示）。
- `proposed` は buildGoalProgress・goal-planner 一覧から除外（承認前は queue/目標タブに出さない）。
- Inbox は既存判断パイプライン非変更の**追加方式**（新フィールド proposedGoals/autoRuns + InboxTabs に追加描画。承認カードは既存 InboxCardItem の汎用 action 実行を再利用）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/types/goal.ts | GoalStatusに proposed・proposalSource/proposedAt/approvedAt |
| progress/lib/goal-proposal.ts | 新規。requestGoalProposalIfIdle |
| progress/lib/goal-writer.ts | proposeGoals/setGoalApproval/VALID_GOAL_STATUS拡張 |
| progress/lib/factory-runner.ts | アイドル分岐に提案依頼を接続 |
| progress/lib/types/operations.ts | event factory_goal_proposal_requested |
| progress/app/api/goals/propose/route.ts | 新規。候補登録API |
| progress/app/api/goals/[goalId]/approve/route.ts | 新規。承認/却下API |
| progress/lib/command-center.ts | InboxViewにproposedGoals/autoRuns・TERMS proposedGoal |
| progress/components/newux/InboxTabs.tsx | 今日の判断タブに🎯ゴール承認/🤖自動実行履歴を描画 |
| progress/lib/auto-queue.ts | buildGoalProgressでproposed除外 |
| progress/app/goal-planner/page.tsx | 目標一覧からproposed除外 |
| progress/app/guide/page.tsx | FAQ追加 |
| progress/docs/operations/current-operating-model.md | 節/frontmatter/変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0）
- build: OK（next build 0・propose/approve API 生成）
- E2E手動: `POST /api/goals/propose`→goals.jsonに proposed 登録→`/decide`に「🎯ゴール承認」カード表示→`POST /approve {approve:true}`→`status=active`+`decisionPolicyDefault=autonomous`（自動実行対象）を実測。Inbox下部「🤖 自動実行の履歴」描画（20件）。`/decide` 200・白画面なし。検証用テストゴールは確認後に削除。
- 機密スキャン: clean
- ob sync: n/a（一旦中止中）
- git push: c91e5ab（origin/main）

---

## 5. 未対応

- **AIによる実候補生成は executor（Claude/Codex）が `/api/goals/propose` を呼ぶ前提**。Factory は LLM をインプロセスに持たないため「提案依頼の発行（ログ＋プロンプト）」までを担当。依頼を自動で実行して propose を呼ぶ executor 連携は未実装（手動/スクリプト/将来の executor で代替可能）。
- 定時自動実行のアイドル時に `factory_goal_proposal_requested` が出て実際に候補が POST されるライブ確認は未（E2Eは API 直叩きで代替）。
- Inbox 承認カード/履歴の実ブラウザ描画は SSR 出力（セクション見出し）で確認。headless 実クリックは未。

---

## 6. 危険ポイント

- 既存機能への影響: 低。Inboxは追加方式（既存 decisions/reviews/candidates パイプライン非変更）。proposed は queue/目標タブ/進捗から除外済みで誤実行しない（承認＝active 化まで自動実行対象にならない）。
- 承認で active+autonomous になるため、危険操作を含むゴールは riskFlagsDefault が空である点に注意（提案時は空固定）。将来、提案に危険フラグが付く場合は ensureNextGoalStepEpic 側の安全ゲート（承認要/手動/危険は対象外）で二重に防御される。
- ロールバック: commit c91e5ab の revert。

---

## 7. 次にやるべきこと

- 提案依頼を自動実行する executor 連携（アイドル検知→propose 自動呼び出し）の実装可否判断。
- 定時実行でのライブ動作確認（提案ログ→候補POST→Inbox承認→次の一歩Epic生成）。
- 提案ゴールの質（重複・粒度）を運用で観察し、提案プロンプト/重複判定を調整。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: ゴール提案→承認→自動実行フロー新設（自動実行中AI提案＋Inbox承認＋自動実行履歴）
runId: 20260618-125718
日付: 2026-06-18
GitHub commit: c91e5ab

## 実施内容
- Factoryアイドル時にAIへゴール提案を依頼(factory_goal_proposal_requested)。実候補はexecutorがPOST /api/goals/proposeで登録(status=proposed/autonomous)。
- 今日の判断(Inbox)上部「🎯ゴール承認」で承認→active(自動実行対象)/却下→dropped。承認後は既存ensureNextGoalStepEpicが次の一歩Epicを自動生成して達成まで進める。
- Inbox下部に「🤖自動実行の履歴」(factoryRun直近10件)を追加。proposedはqueue/目標タブ非表示。

## 検証
- tsc0/build0。E2E: propose→goals.json登録→/decide承認カード→approve→active+autonomous を実測。/decide 200・白画面なし。

## 未対応
- 実候補生成はexecutorがpropose APIを呼ぶ前提(Factoryは依頼発行まで)。自動呼び出し連携は未実装。
- 定時実行のライブ確認は未(API直叩きで代替)。

## 確認したい観点
- 「Factoryは依頼発行まで/実生成はexecutor」という分担の妥当性。自動でproposeを呼ぶ連携をどう実装すべきか。
- 承認=active+autonomousで即自動実行対象にする安全性(危険フラグ提案時の扱い)。
- proposed をqueue/目標タブから除外した設計の妥当性。
- Inboxを追加方式にした(既存パイプライン非変更)判断。
- 収益化インパクト評価(medium)は妥当か。
````

---

## 関連

- progress runId: 20260618-125718（正本）
- 関連: lib/goal-proposal.ts / lib/goal-writer.ts(proposeGoals,setGoalApproval) / lib/goal-step-epic.ts(ensureNextGoalStepEpic)
- 関連アプリ: [[../02_apps/progress]]
