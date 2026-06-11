---
date: 2026-06-12
task: Inboxの4区分を縦積みからタブ切り替えへ変更
runId: 20260612-013213
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260612-005632]
commitHashes: [7c8994c]
reviewFileCommit:
---

# progress Inboxタブ化レポート

## 作業目的

Inbox の4区分（今日の判断/レビュー/Epic候補/AI保留）が縦積みでスクロールが長く、社長アプリとして見通しが悪い。区分ごとのタブ切り替えに変更する（ユーザー指示）。

## 実施内容

- components/newux/InboxTabs.tsx 新規: 4タブ切り替えUI。タブバーに件数バッジ（今日の判断のみ赤字強調）
- 各タブの中身は前回の4セクションと同一（今日の判断=工場停止要因のみ最大3件 / レビュー=放置可+AIにまとめて確認させる / Epic候補=放置可 / AI保留=件数のみ）
- ガイド文言「『今日の判断』タブだけ処理すれば工場は止まりません」

## 変更ファイル

- progress/components/newux/InboxTabs.tsx — 新規
- progress/app/decide/page.tsx — タブUIへ差し替え
- progress/docs/operations/current-operating-model.md — タブ構成へ更新

## 検証結果

- tsc 0 / lint 0 / build 成功 / /decide 200
- 各タブをPlaywrightで実クリックし切替確認: ![[../attachments/screenshots/2026-06-12-decide-tabs-decisions.png]] / ![[../attachments/screenshots/2026-06-12-decide-tabs-reviews.png]] / ![[../attachments/screenshots/2026-06-12-decide-tabs-aihold.png]]
- git push: e9d6ce7..7c8994c 成功

## 未対応

- iPhone実機でのタブ操作感確認はユーザー待ち

## 危険ポイント

- なし（表示構成の変更のみ。データ・API・工場ロジックは不変）

## 次にやるべきこと

- 実機確認後、タブの並び順・バッジ表示の微調整があれば対応

## ChatGPT レビュー依頼文

```
progress アプリ Inbox のタブ化（軽微UI変更）のレビューをお願いします。
runId: 20260612-013213 / commit: 7c8994c（kaeru07/ny01 main）
変更: 4区分（今日の判断/レビュー/Epic候補/AI保留）を縦積み→タブ切り替えへ。件数バッジ付き（今日の判断のみ赤字）。
確認観点: タブ化により②③④の存在に気づきにくくなるリスク（バッジで十分か）/ 今日の判断0件時のデフォルトタブは「今日の判断」のままで良いか。
```
