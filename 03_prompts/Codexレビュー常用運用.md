---
title: Codex 読み取り専用レビュー 常用運用（提案ドラフト）
type: prompt-ops
status: proposal
created: 2026-05-18
updated: 2026-05-18
source: 00_inbox/chatgpt-import/05_次にやるToDo候補.md（高優先・出典 2026-05-16 ClaudeとCodexのAgent活用）
---

# Codex 読み取り専用レビュー 常用運用（提案ドラフト）

> `05_次にやるToDo候補.md` 高優先「Codex を読み取り専用レビューに常用する運用を確立」の**提案ドラフト**。
> 本ファイルは方針案。**正式採用・常用開始の判断はユーザー / pm**（候補ファイルの着手判断ルールに従う）。
> AI はこのドラフトを enact しない（運用切替はしない）。

## 目的

Claude Code が主導で実装・調査し、**Codex を独立した読み取り専用レビュアー**として常用する。
同一モデル系列の自己レビューより、別系統のレビューで対立的観点（盲点・前提誤り）を拾う。

## 原則（厳守）

- Codex は **読み取り専用**。Codex に commit / push / ファイル編集をさせない
- レビュー対象は **既に commit/push 済みの成果物**（または diff）に限定
- Codex の指摘は**提案**であり、採用判断は Claude Code + 人間。Codex 指摘で自動修正・自動 approved 化はしない
- 機密（APIキー/トークン/.env/個人情報）は Codex へ渡す入力からも除外（[[GitHub反映ルール]] の機密スキャン準拠）
- 収益化シナリオの `candidate/pending_approval` を Codex レビュー結果で `approved` にしない（[[../05_monetization/収益化シナリオ承認フロー]]）

## 入力（Codex へ渡すもの）

- 対象 commit hash / 変更ファイル一覧（diff）
- 関連 ExecutionRun の `rawReport`（progress 正本。機密除去済み）
- レビュー観点（対象 ToDo の完了条件・禁止事項）

## 標準フロー

1. Claude Code が作業 → commit/push → progress POST（既存ルール）
2. レビュー依頼文を生成（`20_reviews/` の ChatGPT 依頼文と同形式を流用）
3. Codex に **読み取り専用**で渡す（execution-runs の該当 run + diff）
4. Codex が標準出力でレビュー要約（盲点・前提誤り・代替案）
5. Claude Code が指摘を分類: 「採用（次サイクルで修正）」/「却下（理由記録）」/「人間判断」
6. 結果を `04_reviews/` または当該 `20_reviews/` レビューファイルに 1〜3 行追記
7. 修正が必要なら**別 ExecutionRun**として実施（Codex は修正しない）

## 常用トリガ（案）

- `monetizationImpact: high/medium` の作業
- 設計・運用ポリシー変更（[[Claude-Code標準運用]] の POST 必須範囲拡大と同条件）
- 破壊的変更を含む可能性がある作業（実行前レビュー）
- 上記以外は任意（過剰レビューを避ける）

## 既存実験との関係

- 先行検証: `20_reviews/2026-05-15_progress-codex-experiment.md` / `20_reviews/2026-05-16_codex-sdk-experiment.md` / progress `CODEX.md`
- 本ドラフトはそれらの知見を**常用ルール化**する位置づけ（実装連携の有無は別途）

## 未対応点 / 仮説
- Codex 呼び出し手段（CLI / SDK / 手動）は本ドラフトでは固定しない（環境依存。ユーザー判断）
- 「常用」の頻度上限は未確定。仮に high/medium と設計変更時を必須、他は任意と置いた
- 候補ファイルの着手判断はユーザー/pm のため、本ファイルは status: proposal で据え置き

## 次の一手
1. ユーザー / pm が本ドラフトを採用するか判断
2. 採用時、Codex 呼び出し手段を1つ確定し [[Claude-Code標準運用]] に1行追記
3. 最初の常用対象を1件選び試行 → 効果を `04_reviews/` に記録

## 関連
- [[Claude-Code標準運用]] / [[GitHub反映ルール]] / [[../05_monetization/収益化シナリオ承認フロー]]
- 出典候補: 00_inbox/chatgpt-import/05_次にやるToDo候補.md
