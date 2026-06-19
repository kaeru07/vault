---
date: 2026-06-20
task: 自動実行アイドル時のゴール生成モード（progress優先で承認対象を補充）
runId: 20260620-073504
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: [c650726]
---

# 2026-06-20 自動実行アイドル時のゴール生成モード（progress優先で承認対象を補充）

> 1 作業 = 1 ファイルの自動生成レビュー。

---

## 1. 作業目的

- なぜこの作業をするのか: 自動実行（Factory）で「自動実行対象が無い（`!scan.picked`）」アイドル状態のとき、承認対象（status='proposed' のゴール）が空のまま残ると、次回の自動実行も空になってしまう。これを構造的に防ぐ。
- 背景: 既存のアイドル処理 `requestGoalProposalIfIdle()` は「AIに提案を依頼するプロンプトをログに出すだけ」で承認対象を実際には増やさない。`proposeGoalsFromResearchIfNeeded()` は news-app の調査データからのみ提案し、progress アプリ内部の改善は見ない。→ 調査から候補が出ない局面では承認対象が空のままになる。
- 期待効果: アイドル時間を「ゴール生成モード」に充て、progress 優先で改善事項・試した方がいいことを承認対象へ自動補充。承認すれば次回自動実行が空にならない循環を作る。

---

## 2. 実施内容

- `lib/improvement-goals.ts` 新設: `proposeImprovementGoalsIfIdle()`
  - シグナル優先順（progress 優先）:
    1. 失敗/エラー Run の解消（改善事項）… その後に同名タイトルの成功 Run が無いものだけ抽出
    2. 未消化 nextActions のゴール化（試した方がいいこと）
    3. progress 改善 seed（1・2 で枠が埋まらない時のフォールバック＝空回避の保険・3件）
  - 承認待ち上限 `MAX_PENDING_PROPOSALS=3` を尊重し、残り枠だけ埋める
  - 既存ゴール（全 status）と同名タイトルは除外（承認/却下済みを蒸し返さない）
  - `proposeGoals(..., { source: 'factory_idle_improvement' })` で status='proposed' 登録
- `lib/factory-runner.ts`: アイドル分岐（`!scan.picked && mode==='auto' && opts.confirm`）で `proposeImprovementGoalsIfIdle()` を呼び実登録。従来の `requestGoalProposalIfIdle()`（プロンプトのみ）は補助フォールバックとして併存。
- 設計判断: GoalStatus は既存の `proposed` を再利用（VALID 配列の変更不要）。承認フロー（Inbox / `setGoalApproval`）はそのまま使う。research-goals.ts のパターンを踏襲。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/improvement-goals.ts | 新規。アイドル時の改善ゴール生成（失敗Run→nextActions→seed・上限3・重複除外・progress優先） |
| progress/lib/factory-runner.ts | アイドル分岐で `proposeImprovementGoalsIfIdle()` を呼び実登録。`requestGoalProposalIfIdle` は補助フォールバック化 |

---

## 4. 検証結果

- typecheck: OK（`tsc --noEmit` exit 0）
- build: OK（`npm run build` exit 0）
- lint: OK（`next lint` 変更2ファイル warning/error 0）
- 手動確認: 実データで入力存在を確認（proposed=3=上限のため現状は「上限尊重で新規提案せず」が正しく働く。失敗Run 1件 / 全223 runs の nextActions が補充ソースとして存在）。proposed が3未満に減った局面での実生成は未実行（副作用＝実データに proposed を作るため保留）。
- 機密パターン事前チェック: OK（変更はコードのみ・秘密情報なし）
- ob sync: n/a（ob sync 一旦中止中）
- git push: c650726 / pushed（origin/main）

---

## 5. 未対応

- proposed 枠が3未満の局面での実機アイドル実行による候補実生成の確認（実データ汚染を避けるため未実行）
- seed リスト（progress 改善の定番3件）の内容精査はユーザー判断待ち
- 運用ドキュメント（/guide・current-operating-model.md）への反映は未（機能の挙動が固まってから）

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。アイドル分岐に1ステップ追加のみ。既存の research 提案 / `ensureNextGoalStepEpic` / `requestGoalProposalIfIdle` は温存。
- DB / 認証 / 本番 / 機密: 触れていない。書き込みは goals.json への proposed 追加のみ（承認するまで自動実行対象にならない安全側）。
- ロールバック手段: commit c650726 を revert すれば旧挙動（プロンプトのみ）に戻る。
- 観察項目: アイドル自動実行のたびに承認対象が増えすぎないか（上限3で抑制済みだが seed の蒸し返しが無いか）。

---

## 7. 次にやるべきこと

- proposed を1〜2件に減らした状態でアイドル自動実行を回し、失敗Run/nextActions から候補が実生成されることを確認
- seed 3件の妥当性をユーザーが精査（progress 定番改善テーマの過不足）
- 挙動確定後に /guide と current-operating-model.md へ反映

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の司令塔・ポート3010）
作業: 自動実行アイドル時のゴール生成モード（progress優先で承認対象を補充）
runId: 20260620-073504
日付: 2026-06-20
GitHub commit: c650726（kaeru07/ny01 main）

## 作業目的
自動実行(Factory)で「自動実行対象が無い」アイドル時に、承認対象(proposedゴール)が
空のまま残ると次回の自動実行も空になる。これを構造的に防ぎ、アイドル時間を
progress優先の改善ゴール生成に充てる。

## 実施内容
- lib/improvement-goals.ts 新設: proposeImprovementGoalsIfIdle()
  優先順=(1)失敗/エラーRunの解消(後に成功Run無しのみ) → (2)未消化nextActionsの
  ゴール化 → (3)progress改善seed(空回避の保険)。上限3・重複除外・progress優先。
  proposeGoals(source=factory_idle_improvement)でstatus=proposed登録。
- lib/factory-runner.ts: アイドル分岐で上記を呼び実登録。従来のプロンプトのみの
  requestGoalProposalIfIdleは補助フォールバックとして併存。

## 変更ファイル
- progress/lib/improvement-goals.ts（新規）
- progress/lib/factory-runner.ts（アイドル分岐に1ステップ追加）

## 検証結果
- typecheck / build / lint: すべてOK
- 手動確認: 現在proposed=3(上限)で「上限尊重で新規提案せず」が正しく動作。
  失敗Run1件/全223runsのnextActionsが補充ソースとして存在。実生成は実データ
  汚染回避のため未実行。
- 機密スキャン: OK(コードのみ)

## 未対応
- proposed枠<3での実機での候補実生成確認
- seed3件の内容精査(ユーザー判断)
- /guide・運用ドキュメント反映

## 危険ポイント
- 影響低(アイドル分岐に1ステップ追加のみ)。書き込みはgoals.jsonへのproposed追加のみ。
- GoalStatusは既存proposedを再利用(VALID配列変更なし)。

## 確認したい観点
1. 改善候補のソース設計(失敗Run→nextActions→seed)は妥当か。優先順は適切か。
2. seedフォールバックでの「空回避保証」と、ノイズ提案(承認対象が無意味に増える)の
   バランスは適切か。上限3で十分か。
3. nextActionsをそのままゴール化する粒度の問題(大きすぎ/小さすぎ)への対処は必要か。
````
