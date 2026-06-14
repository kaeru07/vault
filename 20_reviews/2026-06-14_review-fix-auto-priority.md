---
date: 2026-06-14
task: レビュー「修正する」のfixPromptを定時自動実行の最優先で実行（承認不要・安全ゲート）
runId: 20260614-175356
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260614-100448, 20260614-085241]
commitHashes: [b823fc1]
---

# 2026-06-14 レビュー修正プロンプトの最優先自動実行

> ユーザー選択=オプション3（承認なしで自動実行・最優先）。Codex 実装 → Fable レビュー。

## 1. 実施内容
- 新規 `runReviewFixDispatch()`（`lib/review-fix-runner.ts`）を `runScheduledFactory` の **先頭**（Epic factory・Prompt Queue より前）に接続＝**次回自動実行で最優先**。
- 対象: `reviewStatus==='needs_followup'` ＋ `fixPrompt` 非空（未消化＝`source='review_fix'`/auto/completed の followup を持つ元 run は除外）。`fixRequestedAt` 昇順・cap 1〜2。
- 安全ゲート: `classifyCodexEligibility` ＋ hard-deny（課金/billing/deploy/本番/production/secret/.env/認証/migration/**削除**/destructive/force）。危険該当は実行せず `needs_followup` のまま reviewMemo に理由。
- mode別: dry_run=記録のみ（元 run 不変）/ auto+confirm（定時のみ）=`getAdapter('claude')` 実起動。成功時のみ元 run を `reviewed` に更新して消化（再ディスパッチ防止）。
- 記録: `source='review_fix'` / `followupOfRunId=<元runId>` / `promptUsed`。

## 2. 変更ファイル
lib/review-fix-runner.ts（新規）/ lib/factory-schedule.ts / app/guide/page.tsx / docs/operations/current-operating-model.md

## 3. 検証（Fable）
- tsc 0 / lint 0 / build 成功。
- スケジューラ呼び出し順: **review-fix → Epic factory → Prompt Queue**（コード確認）。
- 実データ読み取り検証（pickTargets＋安全ゲート再現）:
  - `20260614-021255`（inboxフィルター）→ **安全OK＝次回定時で最優先 auto 実行対象**。
  - `20260614-092943`（Goal Planner統合）→ 本文に「**削除**」を含むため **安全ゲートでブロック＝自動実行されず**（needs_followup のまま・人手対応）。
  - danger regex sanity:「本番DBを削除 force」→ True。
- ※ auto+confirm の実 adapter 起動は安全方針によりテストせず（dry_run／読み取り＋コードレビューで担保）。runner ロジックは検証済みの prompt-queue-runner と同型。

## 4. 重要な注意 / リスク
- **次回定時（14/16/23時）から、needs_followup＋fixPrompt（安全）が実 executor で自動実行される**（承認なし）。ユーザーの1件目は実行対象、2件目は「削除」でブロック。
- 過剰ブロック: 「削除」等の一般語を含む正当な修正指示もブロックされる（安全側の失敗）。語を避けて再登録 or 手動対応が必要。
- 軽微: blocked 時に reviewMemo が毎定時 追記され bloat しうる（重複抑制は未対応）。
- consume-dedup は auto のみ（dry_run 反復は review_fix 記録が増える＝テスト時のみ）。

## 5. 次にやるべきこと
- 翌日の定時ログ（ExecutionRun `source='review_fix'`）で 1件目が auto 実行され元 run が reviewed になるか確認。
- 2件目は「削除」回避の文言で再登録するか手動/別Epic化を判断。
- reviewMemo 追記の重複抑制・過剰ブロック調整を運用後に検討。

## ChatGPT レビュー依頼文
```
対象: progress レビュー修正プロンプトの最優先自動実行（runId 20260614-175356 / commit b823fc1）
変更: lib/review-fix-runner.ts(新規), lib/factory-schedule.ts, app/guide/page.tsx, docs/operations/current-operating-model.md
背景: 「修正する」のfixPromptを承認なしで定時自動実行の最優先に。安全ゲート(hard-deny＋classifyCodexEligibility)通過分のみauto実行、危険語はブロック。
観点:
1. 承認なし自動実行をスケジューラ最優先に置く設計の妥当性・暴走防止(cap/安全ゲート/消化マーキング)。
2. hard-denyに『削除』等の一般語を含めることで正当な修正までブロックする過剰ブロックの是非(語彙の調整方針)。
3. 消化マーキング(成功時のみ元run→reviewed/auto限定dedup)に抜け・二重実行リスクはないか。
4. review-fix→Epic→Prompt Queueの実行順と1起動cap=1のスループット。
```
