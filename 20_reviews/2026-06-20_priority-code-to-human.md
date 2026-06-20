---
date: 2026-06-20
task: 統合ゴールを平易な名前に改名＋アプリ全体のP0/P1/P2表記を高/中/低へ統一
runId: 20260620-163521
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260620-144248]
commitHashes: [a5fc103]
---

# 2026-06-20 優先度コード(P0/P1/P2)を人間語へ統一＋統合ゴール改名

## 1. 作業目的
- ユーザー指示: 「progressに寄せる」方針を承認。「P1/P2の用語だと分かりにくい。アプリ上もこの言葉を使わないように変換して」。

## 2. 実施内容
- **ゴール改名（データ操作）**: enrich-proposals API に newTitle/summary/cons 編集を追加し、【運用統合P1〜P6】を ①〜⑥ の平易な日本語へ改名。enables/cons/summary 内の P 番号も除去（updated=6）。
- **アプリ全体の P0/P1/P2 廃止（コード）**: `lib/epic-priority-label.ts` 新設（P0→高 / P1→中 / P2→低・内部値は据え置き）。司令塔（app/page.tsx）・自動実行キュー（/queue: フィルタ/チップ/カード/「最優先で次回実行」ボタン/スコア要因 reasonFactors）・Epic詳細・おすすめEpic一覧/詳細・承認パネル（InlineApprovalPanel/approvals）・Epic作成フォーム・JSON取込プレビュー・legacy home の優先度表示を高/中/低へ。reasonFactors の生 P0 は auto-queue-score.ts:139 と auto-queue.ts:279 の2経路を修正。guide FAQ の P0/P2 表記も言い換え。
- **方針記録**: 06_research 点検docに「正本は progress に寄せる（ユーザー承認）」を追記。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| lib/epic-priority-label.ts | 新規: P0/P1/P2→高/中/低 表示変換 |
| lib/auto-queue-score.ts / auto-queue.ts | reasonFactorsの生priorityを優先度ラベルへ |
| app/queue/page.tsx / app/page.tsx | 主要画面の優先度表示を人間語へ |
| app/epic/[epicId] / recommended-epics/[id] / approvals / guide / legacy/home | 各画面の優先度表示・FAQ言い換え |
| components/{dashboard/FactoryActions, epic/EpicCreateForm, epic/EpicJsonImport, epic/InlineApprovalPanel, recommended/RecommendationList} | 優先度ドロップダウン/バッジ/プレビューを人間語へ |
| app/api/goals/enrich-proposals/route.ts | newTitle/summary/cons編集を追加(ゴール改名用) |

（データ操作: 統合ゴール6件を①〜⑥へ改名）

## 4. 検証結果
- typecheck/build: OK
- 手動: pm2 restart後 / /queue /decide /legacy/home /recommended-epics いずれも200。画面上の `>P0/P1/P2<` リーク=0、優先度 高/中/低 が描画。ゴール承認に①〜⑥表示・「運用統合P」表記=0。
- 機密スキャン: OK / ob sync: n/a / git push: a5fc103 pushed

## 5. 未対応（意図的な残置）
- EpicJsonImport の記入例placeholder（"priority":"P1"）は入力フォーマット説明のため残置。
- PromptCopy/QueueControls のコピー用プロンプト文の P 表記は AI 向けエクスポートのため残置。
- EpicPriority 型（P0/P1/P2）は内部値として維持（表示のみ変換）。
- task優先度（high/medium/low）の英語表示統一は別軸（今回対象外）。

## 6. 危険ポイント
- 表示変換のみ。優先度の値・並び順ロジックは不変（フィルタ value は P0/P1/P2 のまま）。

## 7. 次にやるべきこと
- 統合ゴール①（対応表）を承認して着手。
- task優先度の英語表示を日本語化するか判断。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: 統合ゴール改名＋アプリ全体のP0/P1/P2を高/中/低へ統一
runId: 20260620-163521 / commit: a5fc103

## 目的
「progressに寄せる」承認。P1/P2の内部語をアプリ画面から無くし人間語へ。

## 実施
統合ゴール6件を①〜⑥へ改名。epic-priority-label.tsで全画面のP0/P1/P2を高/中/低へ
(司令塔/キュー/Epic/おすすめ/承認/フォーム/legacy/スコア要因/FAQ)。値は据え置き。

## 検証
tsc/build OK。/ /queue /decide /legacy 実描画200でP0/P1/P2リーク0・高/中/低描画。

## 確認したい観点
1. 画面からP表記が消え分かりやすくなったか(見落とし箇所の有無)。
2. コピー用プロンプト文のP表記を残した判断は妥当か(AI向け)。
3. task優先度の英語(high/medium/low)も日本語化すべきか。
````
