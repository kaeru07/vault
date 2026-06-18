---
date: 2026-06-19
task: 今日の判断(Inbox)に「ゴール承認」タブを新設＋初回候補を生成
runId: 20260619-003157
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260618-125718, 20260618-220636]
commitHashes: [b6b730a]
---

# 2026-06-19 ゴール承認タブの新設＋初回候補生成

> ユーザー指示: todoにgoal承認タブを追加して。自動実行時に追加したゴール候補の承認依頼を入れて。初回として候補出してみて。

---

## 1. 作業目的

- 自動実行が提案したゴール候補（proposed）の承認/却下を、今日の判断(Inbox)の専用タブに集約する（従来は今日の判断タブ上部のセクション）。
- 初回として実際に候補を生成し、承認できる状態にする。

---

## 2. 実施内容

- `components/newux/InboxTabs.tsx`：`TabKey` に `goalApproval` を追加。`tabFromQuery` / タブ配列（「ゴール承認」count=承認待ち件数・alert付き）/ quickFilters / tabCounts / `filteredProposedGoals` を追加。
- 提案ゴール（`inbox.proposedGoals`）の描画を「今日の判断」タブ上部のセクションから **専用タブ `?tab=goalApproval`** へ移設。スコープ/検索フィルタ対応、空状態ガイド（「自動実行のたびに調査結果から候補が追加されます」）付き。自動実行履歴（autoRuns）は今日の判断タブに残置。
- 初回候補：`proposeGoalsFromResearchIfNeeded`（PROGRESS_DATA_PATH=data/real）を実行し、調査結果から3件を proposed 登録。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/components/newux/InboxTabs.tsx | goalApprovalタブ新設・proposedGoals描画を専用タブへ移設・空状態ガイド |
| progress/app/guide/page.tsx | FAQの提案ゴール表示先を「ゴール承認タブ」に更新 |
| progress/docs/operations/current-operating-model.md | Inbox5タブ化・承認はgoalApprovalタブ・frontmatter・変更履歴 |
| progress/data/real/goals.json | 初回候補3件(proposed/source=research)生成・保持（実行時データのためgitコミット対象外） |

---

## 4. 検証結果

- typecheck: OK（tsc 0）
- build: OK（next build 0）
- 手動確認: `/decide?tab=goalApproval` 200・「ゴール承認」タブ＋「自動実行が提案した目標」見出し＋候補3件描画。
- 初回候補（生成・保持）: 「MCPセキュリティ対策カテゴリを試す」(P0) / 「OpenClawを試す」 / 「MCP Appsを調査する」（proposed・source=research）。
- 承認すると既存フロー（active+autonomous → ensureNextGoalStepEpic）で自動実行対象になる。
- ob sync: n/a / git push: b6b730a（コード/ドキュメントのみ。data/realは実行時データのため除外）

---

## 5. 未対応

- 定時自動実行（11/14/16/23）冒頭で候補が補充され「ゴール承認」タブに積まれるライブ確認は未（手動生成で代替）。
- 候補タイトルに冗長語が混じる場合あり（例「MCPセキュリティ対策カテゴリを試す」）。
- モバイル下タブからゴール承認タブへの直接導線は未（/decide内のタブ切替で到達）。

---

## 6. 危険ポイント

- 既存機能への影響: 低。タブ追加＋既存セクションの移設のみ。判断/レビュー/候補/AI保留の各タブロジックは非変更。
- data/real/goals.json に proposed を3件追加（実行時データ）。承認するまで queue/目標タブには出ない（proposed除外済み）。
- ロールバック: commit b6b730a の revert（コード）。候補は goal-planner 等から却下/削除可。

---

## 7. 次にやるべきこと

- 定時実行でのライブ確認（冒頭補充→ゴール承認タブ→承認→次の一歩Epic）。
- タイトル整形、モバイル導線の検討。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: 今日の判断(Inbox)に「ゴール承認」タブを新設＋初回候補を生成
runId: 20260619-003157
日付: 2026-06-19
GitHub commit: b6b730a

## 実施内容
- InboxTabsにgoalApprovalタブを新設し、自動実行が提案したゴール候補(proposed)の承認/却下を専用タブ(?tab=goalApproval)に集約(従来は今日の判断タブ上部のセクション)。空状態ガイド付き。
- 初回候補3件を調査結果から生成・保持(MCPセキュリティ対策カテゴリを試す[P0]/OpenClawを試す/MCP Appsを調査する)。
- 承認で既存フロー(active+autonomous→ensureNextGoalStepEpic)で自動実行対象。

## 検証
- tsc0/build0。/decide?tab=goalApproval 200・タブ＋候補3件描画。

## 未対応
- 定時実行のライブ確認は未(手動生成で代替)。タイトル冗長語。モバイル下タブ導線未。

## 確認したい観点
- ゴール承認を専用タブに分離した設計の妥当性(今日の判断との役割分担)
- 初回候補の質・タイトル整形の要否
- data/realに候補を直接生成して保持した判断(実行時データ・gitは除外)
- モバイルからの到達性
````

---

## 関連

- progress runId: 20260619-003157（正本）
- 関連: components/newux/InboxTabs.tsx / lib/research-goals.ts / [[2026-06-18_goal-proposal-approval]] / [[2026-06-18_research-goal-proposal]]
- 関連アプリ: [[../02_apps/progress]]
