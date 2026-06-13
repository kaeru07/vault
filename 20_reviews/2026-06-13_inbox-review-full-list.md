---
date: 2026-06-13
task: Inboxレビュー待ちを未消込リスト化（全件表示・完了日時・最新順・消し込み/あとで/要修正/レビュー済み・AI一括整理を全件化）
runId: 20260613-134639
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: []
commitHashes: [2dc7134]
---

# 2026-06-13 Inboxレビュー待ちUI改修

## 1. 作業目的

- なぜ: Inboxのレビュー待ちが「ほか33件」「処理すると次が出ます」で隠れ、全体が見えなかった。AI確認も10件固定で「隠れている」印象を生んでいた。
- 背景: レビュータブが `SECTION_DISPLAY_LIMIT=5` で截断、`AiCheckButton` が `limit:10` 固定。完了日付が見えず最新作業が分かりにくい。
- 期待効果: レビュー対象を隠さず全件表示し、人間がレビュー完了で消し込む運用に変更。完了日時を見やすく、最新完了から上に並べる。

## 2. 実施内容

- レビュー待ちを隠さず全件表示（隠れ表示の文言・5件截断を廃止）。
- 各カードに完了日時（YYYY/MM/DD HH:mm）を表示、completedAt（finishedAt→startedAtフォールバック）降順で最新を上に。
- 件数サマリー（未確認/要修正/あとで/レビュー済み）＋フィルタタブ＋50件明示ページング（全◯件中◯〜◯件）。
- 状態遷移を整理:
  - 問題なし → `reviewed`（一覧から消込・「レビュー済み」タブに残置＝物理削除なし、未確認に戻す可）
  - あとで → 新ReviewStatus `snoozed`（後回しで一覧残置・バッジ表示）
  - 修正する → `needs_followup`（要修正で残置＋修正依頼が次作業候補へ／既存挙動）
- 「AIにまとめて確認させる（10件固定）」→「未確認レビューをAIで一括整理（未確認全件・サーバ安全上限200件）」。危険・要判断は必ず残し最終判断は人間。
- ny01ルールに従い運用ドキュメント（guide FAQ / current-operating-model.md frontmatter＋本文＋変更履歴）をセット更新。

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| types/execution-run.ts | ReviewStatusに `snoozed` 追加 |
| app/api/execution-runs/[runId]/route.ts | VALID_REVIEW_STATUSESに `snoozed` 追加 |
| lib/ai-review.ts | 一括レビューのバッチ上限 20→200 |
| lib/command-center.ts | fmtDateTime/runCompletedAt追加。InboxCard/InboxView拡張。レビューカード生成統一・snoozed/needs_followup残置・reviewed履歴生成・completedAt降順ソート |
| components/newux/InboxActions.tsx | 状態バッジ＋完了日時表示 |
| components/newux/InboxTabs.tsx | レビュータブ全件表示・件数サマリー・フィルタ・50件ページング |
| components/newux/AiCheckButton.tsx | 10件固定→未確認全件・文言変更 |
| app/guide/page.tsx | レビュータブの見方・状態遷移FAQ |
| docs/operations/current-operating-model.md | レビュー運用更新＋frontmatter＋変更履歴 |

## 4. 検証結果

- typecheck: OK（tsc --noEmit exit 0）
- build: OK（Compiled successfully / 48/48 pages）
- lint: OK（No ESLint warnings or errors）
- 手動確認: pm2 restart後、/ ・/decide ・/guide ・/logs すべて HTTP 200。/decide のRSCに completedAtText（"2026/06/13 11:03"形式・89件）/ reviewCounts / reviewedHistory / reviewedTotal の反映を確認。reviewsはcompletedAt降順。guideに新文言・最終更新（updateNote）反映を確認。
- 機密パターン事前チェック: OK（実値なし）
- ob sync: 失敗（Obsidian Syncサブスク期限切れ・最終応答参照）
- git push: progress 2dc7134 pushed / vault は最終応答参照

## 5. 未対応

- iPhone実機でのフィルタ切替・ページング・タップ操作の表示崩れ確認（curlではclient条件レンダリング部分を目視できないため実機確認が残る）。
- Epic候補タブの「処理すると次が出ます」隠れ表示は今回スコープ外（レビュー待ちのみ対象）。
- あとで（snoozed）の自動解除・期限運用は将来検討。

## 6. 危険ポイント

- 変更禁止範囲（Project/Revenue/運用/Legacy導線・作業履歴の物理削除・ToDo/実行キュー/自動実行仕様）は不変更。
- reviewed は物理削除せず「レビュー済み」タブ（直近200件）で参照。総数は reviewedTotal で保持。
- snoozed はDBスキーマ破壊ではなくReviewStatusの列挙追加（後方互換・既存データ影響なし）。

## 7. 次にやるべきこと

- iPhone実機確認（レビュータブのフィルタ・ページング・バッジ・完了日時の崩れ）。
- 必要なら Epic候補タブも同様に隠れ表示を解消。

## 8. ChatGPT レビュー依頼文

```
対象: Progress アプリ（apps/ny01/progress）Inboxレビュータブの全件表示改修
runId: 20260613-134639 / commit: 2dc7134
変更ファイル: types/execution-run.ts, app/api/execution-runs/[runId]/route.ts, lib/ai-review.ts,
  lib/command-center.ts, components/newux/{InboxActions,InboxTabs,AiCheckButton}.tsx,
  app/guide/page.tsx, docs/operations/current-operating-model.md

背景: レビュー待ちが「ほか◯件・処理すると次が出ます」で隠れ、AI確認も10件固定だった。
レビュー待ちを未消込リストとして全件表示し、完了日時・最新順・消し込み運用に変更した。

レビューしてほしい観点:
1. 状態遷移（問題なし→reviewed消込／あとで→snoozed／修正する→needs_followup）に抜け・戻れない袋小路はないか。
2. snoozed をReviewStatusに追加した影響範囲（factory-metrics/ai-review/queue等）に漏れはないか。
3. reviewed履歴を直近200件に制限した方針は妥当か（古いものが参照不能になるリスク）。
4. completedAt=finishedAt→startedAtフォールバックは「最も妥当な完了日付」として適切か。
5. AI一括整理を未確認全件（上限200件）にしたことで、一度に大量の状態書き込み・Approval生成が起きるリスクをどう見るか。
6. スマホUI（フィルタ＋50件ページング＋バッジ＋完了日時）の操作性・情報量。
```
