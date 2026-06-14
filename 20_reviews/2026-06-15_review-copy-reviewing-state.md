---
date: 2026-06-15
task: レビュー用コピーをコピーしたら「レビュー中」表示にする
runId: 20260615-010322
targetApp: ny01/progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: []
commitHashes: [fd8fc8b]
---

# 2026-06-15 レビュー用コピー「レビュー中」表示

> ユーザー要望「レビュー用コピー押したら、レビュー中と表示するようにして」。

## 実施内容
- `components/review-copy/ReviewCopyButton.tsx`（クライアント状態のみ）:
  - 「全体をコピー」成功で `startReviewing()` → 主ボタンを **「🔍 レビュー中」**（amber）に切替。
  - 状態は `localStorage('reviewCopy:reviewing')` に保持し、ChatGPT/Fableへ貼って戻る画面遷移・再読み込みでも維持。
  - モーダル内に「🔍 レビュー中（ChatGPT/Fableで確認中）」バナー＋「レビューを終了」ボタンで解除。
- `docs/operations/current-operating-model.md`：レビュー用コピー節・変更履歴・frontmatter 更新。

## 変更ファイル
components/review-copy/ReviewCopyButton.tsx / docs/operations/current-operating-model.md

## 検証
- tsc 0 / lint 0 / build 成功。
- `/` 200。idle 表示は「📋 レビュー用コピー」、コピー成功後にクライアント状態で「🔍 レビュー中」へ（localStorage保持）。

## 残課題
- iPhone実機で コピー→レビュー中表示→レビューを終了 の動作確認。
- 必要ならレビュー中の経過時間・対象 runId の併記も検討。

## ChatGPT レビュー依頼文
```
対象: progress レビュー用コピー「レビュー中」表示（runId 20260615-010322 / commit fd8fc8b）
変更: components/review-copy/ReviewCopyButton.tsx, docs/operations/current-operating-model.md
背景: 「レビュー用コピー」でコピーしたら「レビュー中」と表示してほしい。localStorageで状態保持・モーダルで終了。
観点: 1.localStorage保持のUXは妥当か(端末/タブ間で状態が共有されない点) 2.「レビューを終了」以外に自動解除条件(時間経過等)が要るか 3.レビュー中の対象や時刻の可視化要否。
```
