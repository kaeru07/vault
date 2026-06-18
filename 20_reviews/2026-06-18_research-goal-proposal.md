---
date: 2026-06-18
task: ゴール提案の抽出元を日々の調査結果に強化（優先順処理＋達成後に次提案・例Fableを試す）
runId: 20260618-220636
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy, market-research]
relatedRunIds: [20260618-125718]
commitHashes: [7705246]
---

# 2026-06-18 ゴール提案を日々の調査結果ベースに強化

> ユーザー指示: 自動実行はゴール優先順で処理／ゴールが終わったら次のゴールを提案／自動実行の最初にnewsアプリ等の日々の調査結果を参照し、効果がありそうなものからゴール作成（例: Fableを試す）。

---

## 1. 作業目的

- ゴール提案の抽出元を「日々の調査結果（news-app の AIツール調査など）」にし、効果が見込める項目を自動でゴール候補化する。
- 処理はゴール優先順、ゴール達成後は次のゴールを提案できるようにする。

---

## 2. 実施内容

- `lib/research-goals.ts` 新設:
  - `news-app/content/research/daily-ai-tools/YYYY-MM-DD.md`（直近3日・`RESEARCH_CONTENT_PATH` で変更可）の「## 導入価値評価」をパース。
  - ★4以上（即試したい/即調査/必須/PoC向き 等）を「効果がありそうなこと」として抽出 → `○○を試す / ○○を調査する` ゴール候補（★5→P0・★4→P1）を生成（`buildResearchGoalCandidates`）。
  - `proposeGoalsFromResearchIfNeeded`：承認待ち(proposed)<3件のとき `proposeGoals(source='research')` で登録。既存ゴール（全status）と同名は除外（承認済/却下済を蒸し返さない）。
- `lib/factory-runner.ts`：`runFactory` の auto+confirm **冒頭**で `proposeGoalsFromResearchIfNeeded()` を呼ぶ＝自動実行の最初に調査からゴール提案。`factory_goal_proposal_requested` ログに作成分を記録。
- 優先順処理は既存の `rankGoals` / `ensureNextGoalStepEpic`（最上位ゴールから次の一歩Epic）で実現済み。優先順で消化されて承認待ちが減ると、次回また調査から補充＝**ゴール達成後に次のゴールが提案される**。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/research-goals.ts | 新規。調査の導入価値評価★4+を抽出しゴール候補生成・proposeGoalsFromResearchIfNeeded |
| progress/lib/factory-runner.ts | auto+confirm冒頭で調査ベース提案を実行 |
| progress/app/guide/page.tsx | FAQを調査ベース提案・優先順・達成後補充に更新 |
| progress/docs/operations/current-operating-model.md | ゴール提案節拡張・frontmatter・変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0）
- build: OK（next build 0）
- 実データ抽出ドライラン: daily-ai-tools 実ファイルから「OpenClawを試す(4★)」「MCP Appsを調査する(4★)」「MCPセキュリティ対策カテゴリを試す(5★)」「OpenAI Codex SDKを試す(5★)」等を生成（★で優先度設定）。
- E2E: `proposeGoalsFromResearchIfNeeded`（PROGRESS_DATA_PATH=data/real）→ goals.json に proposed(source=research) 3件登録 → `/decide` に「🎯 ゴール承認」カード描画を確認 → テスト分を両データパス（data/real・data/sample）から後始末で削除（proposed残0）。
- `/decide` `/guide` 200・機密スキャン clean。
- ob sync: n/a / git push: 7705246

---

## 5. 未対応 / 注意

- **データパス注意**: `tsx` 素実行は `getDataPath()` 既定が `data/sample` になる（.env.local を読まないため）。pm2/本番は `data/real`。E2E は `PROGRESS_DATA_PATH=data/real` 指定で実施し後始末済み。
- 抽出は現状 `daily-ai-tools`「導入価値評価」中心。`daily-market-research`/`daily-ai-news` への拡張は未（必要なら追加）。
- 定時自動実行（11/14/16/23）冒頭で実際に提案が出るライブ確認は未（手動E2Eで代替）。
- ゴールタイトルにカテゴリ名等の冗長語が混じる場合あり（例「MCPセキュリティ対策カテゴリを試す」）。人間が Inbox で取捨選択する前提。

---

## 6. 危険ポイント

- 既存機能への影響: 低。提案は proposed（承認まで自動実行されない）。research-goals は read-only パース＋proposeGoals 呼び出しのみ。Factory 本体の実行ロジックは非変更（冒頭に提案ステップを追加するだけ・失敗は try/catch で握りつぶし本処理を止めない）。
- 提案ゴールは riskFlagsDefault 空・autonomous。承認後に ensureNextGoalStepEpic の安全ゲート（承認要/手動/危険は対象外）で二重防御。
- ロールバック: commit 7705246 の revert。

---

## 7. 次にやるべきこと

- 定時実行でのライブ確認（冒頭提案→🎯ゴール承認→承認→次の一歩Epic）。
- 抽出元の拡張（market-research/ai-news）と重複・粒度の運用観察。
- タイトル整形の改善。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: ゴール提案の抽出元を日々の調査結果に強化（優先順処理＋達成後に次提案）
runId: 20260618-220636
日付: 2026-06-18
GitHub commit: 7705246

## 実施内容
- lib/research-goals.tsで news-app daily-ai-tools「導入価値評価」★4以上を抽出し○○を試す/調査するゴール候補(★5→P0/★4→P1)をproposed登録。
- runFactory auto+confirm冒頭でproposeGoalsFromResearchIfNeededを実行(自動実行の最初に調査からゴール提案)。承認待ち<3で打ち止め、既存同名(全status)除外。
- 優先順処理は既存rankGoals/ensureNextGoalStepEpic。優先順消化で承認待ちが減ると次回補充=達成後に次提案。
- 承認は今日の判断(Inbox)🎯ゴール承認→active+autonomousで自動実行対象。

## 検証
- tsc0/build0。実データ抽出ドライランでOpenClaw/MCP Apps/MCPセキュリティ等生成。E2E: proposed登録→/decide描画→後始末。

## 未対応/注意
- 抽出はdaily-ai-tools中心(market-research/ai-news未拡張)。tsx素実行はdata/sample既定(本番data/real)。定時実行ライブ確認は未。タイトルに冗長語が混じる場合あり。

## 確認したい観点
- 調査からの抽出ロジック(導入価値評価★4+)の妥当性・拡張すべき抽出元
- 「達成後に次提案」を承認待ち<3の補充で実現する設計の妥当性(明示的なgoal-done検知にすべきか)
- 提案ゴールのタイトル品質・重複の運用
- 承認=active+autonomousで自動実行対象化する安全性
- 収益化インパクト(medium)の妥当性
````

---

## 関連

- progress runId: 20260618-220636（正本）
- 関連: lib/research-goals.ts / lib/goal-writer.ts(proposeGoals) / lib/goal-step-epic.ts / [[2026-06-18_goal-proposal-approval]]
- 調査データ: news-app/content/research/daily-ai-tools
- 関連アプリ: [[../02_apps/progress]]
