---
date: 2026-06-19
task: 運用ページをタブ化し「自動実行レポート」タブを追加
runId: 20260619-213955
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260619-125513]
commitHashes: [c914da5]
---

# 2026-06-19 運用ページの自動実行レポートタブ

> ユーザー指示: 運用タブをメイン画面に追加して、その中に自動実行内容の報告タブを作って。

---

## 1. 作業目的

- 運用ページを主要タブから開けるようにし（既存対応済み）、その中で「AI工場が自動で何をしたか・次に何をするか」をまとめて見られる報告タブを作る。

---

## 2. 実施内容

- 運用は既に下タブ/上タブの主要タブ（メイン画面のナビ）に追加済み。
- `app/guide/page.tsx` を `?tab=report` で分岐するタブUI化（`GuideTabBar`: 使い方ガイド / 自動実行レポート）。report タブは早期 return で軽量描画。
- 新規 `components/operations/AutoExecReport.tsx`（server component）:
  - AI工場の状態（稼働中/停止＝automation config）＋次回予定（11/14/16/23 JST）
  - これまでの自動実行サマリー（完了/一部完了/失敗/計）
  - 直近の自動実行 12件（タイトル・状態・概要・日時）
  - 自動化の動き（factory_goal_proposal_requested / factory_goal_step_epic_created / factory_dispatch 等の最近ログを人間語ラベルで）
  - 承認待ちゴール候補件数 → /decide?tab=goalApproval、進行中の目標件数 → /goal-planner、全実行履歴 → /logs リンク
  - データ: readExecutionRuns(factory run 抽出)/getAutomationLog(40)/readGoals/getAutomationConfig
- guide FAQ「自動実行の状況をまとめて見たい」追加。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/components/operations/AutoExecReport.tsx | 新規。自動実行レポート |
| progress/app/guide/page.tsx | タブ化(GuideTabBar)・report分岐・FAQ |
| progress/docs/operations/current-operating-model.md | 運用タブ化/レポート/frontmatter/変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0） / build: OK（next build 0）
- 手動: `/guide` にタブバー（使い方ガイド/自動実行レポート）描画。`/guide?tab=report` 200・全セクション（状態と次回予定/サマリー/直近の自動実行/自動化の動き/承認待ちゴール候補）描画・白画面なし。
- ob sync: n/a / git push: c914da5

---

## 5. 未対応

- レポートに「次回実際に動くEpic（選定候補）」を出すかは未（次回予定は時刻のみ）。
- 実機での数値・リンク最終確認は未（curl描画確認で代替）。

---

## 6. 危険ポイント

- 影響: 低。新規 server コンポーネント＋guide のタブ分岐のみ。既存ガイド内容は guide タブにそのまま残置。読み取り専用（実行ロジック非干渉）。
- ロールバック: commit c914da5 の revert。

---

## 7. 次にやるべきこと

- 次回実行の選定候補（次に動くEpic）の表示検討。
- 実機確認。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: 運用ページをタブ化し「自動実行レポート」タブを追加
runId: 20260619-213955
日付: 2026-06-19
GitHub commit: c914da5

## 実施内容
- /guide を ?tab=report で分岐するタブUIに(使い方ガイド/自動実行レポート)。
- 新規 AutoExecReport(server): AI工場の状態・次回予定(11/14/16/23)・自動実行サマリー(完了/一部/失敗/計)・直近の自動実行12件・自動化の動き(最近ログ)・承認待ちゴール候補→承認タブリンク。

## 検証
- tsc0/build0。/guide?tab=report 200・全セクション描画・白画面なし。

## 未対応
- 次回実際に動くEpic(選定候補)の表示は未。実機最終確認は未。

## 確認したい観点
- 自動実行レポートの情報設計(社長がひと目で把握できるか)
- 次回選定候補(次に動くEpic)を出すべきか
- ログのイベント→人間語ラベルの網羅性
````

---

## 関連

- progress runId: 20260619-213955（正本）
- 関連: components/operations/AutoExecReport.tsx / app/guide/page.tsx / [[2026-06-19_proposed-fix-bulk-import]]
- 関連アプリ: [[../02_apps/progress]]
