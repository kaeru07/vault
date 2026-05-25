---
type: monetization-execution-plan
title: candidate-006（llm-chooser）承認後 7 日実行プラン
date: 2026-05-25
issue: kaeru07/vault#61
scenario: candidate-006
status: 計画のみ（承認後に着手・progress 投入は人間）
---

# candidate-006（llm-chooser）承認後 7 日実行プラン

> Issue #61 / #80。candidate-006 が **approved になった後**すぐ動ける状態を作る計画。
> **計画のみ。approved 化・progress 投入・本番実装はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> 起点は [[candidate-006_公開ブロッカー]] の解除順（B1→B2→B3→B4→B5→B8→B7→B6）。

> [!note] 用語注（Issue #69）
> - approved / 承認済み = ChatGPT + あなたの判断で「実装着手 OK」になった状態
> - Day0 = 承認判断の日 / Day1 = 承認翌日（実作業 1 日目）
> - B1〜B8 = 公開ブロッカー（公開を阻む問題）の種別番号
> - progress 投入 = progress（作業履歴アプリ・localhost:3010）への ToDo 追加
> - Vercel = 静的サイト・Next.js を無料層でホストできる代表サービス
> - 判定基準客観化 = 「どの LLM が合うか」の判定根拠を観察可能な指標で裏付けすること（N-03 既知制約）
> - LLM Chooser = 質問 4 種に答えて Claude / Codex / Gemini / ローカル LLM を提案するクイズ形式の静的 HTML
> - Shorts = YouTube Shorts などの短尺動画
> - nanikiru-shorts 基盤 = 既存の麻雀 Shorts 動画自動生成パイプライン（流用可能）

## 前提（Day0・人間 + ChatGPT）

- candidate-006 が `approved`（ChatGPT 方向性承認 → 人間 status 確定）
- 作業は `90_prototypes/llm-chooser/` 配下で完結（既存 vault リポジトリ内・別アプリ作成不要）
- 公開先 Vercel 想定（無料層・静的構成と相性最良）

## 7 日プラン（1 日単位 ToDo / 完了条件 / 依存）

| Day | ToDo | 完了条件 | 依存 |
|---|---|---|---|
| Day1 | B1: iPhone Safari 実機確認 + B2 判定基準客観化方針 1 ページ | iPhone でクイズ動作確認 + 「観察可能な指標で裏付ける方針」1 ページ記録 | 承認(Day0) |
| Day2 | B3: Vercel デプロイ（無料層 / GitHub 連携） | 本番 URL 確定 / iPhone でも本番 URL で動く | Day1 |
| Day3 | B4-1: Claude Code 利用ログ蓄積（直近 1 週間分の使い分け実例 5 件以上） | 利用ログ 5 件が判定根拠としてカード裏面に追記 | Day2 |
| Day4 | B4-2: Codex / Gemini / ローカル LLM 各 2 件以上の使い分け実例追加 | 利用ログ計 11 件以上 / 判定基準が個人観察→ログ参照ベースに昇格 | Day3 |
| Day5 | B5: 質問重み調整 UI（質問ごとに重みを slider で調整可能に） | 質問重み調整が UI から可能 / 即座に判定結果に反映 | Day2（並行可）|
| Day6 | B8: 「LLM 使い分けチートシート Markdown」テンプレドラフト（販売はしない） | 30 行程度のチートシート Markdown 完成 | Day4 |
| Day7 | B7: 速度比較動画 1 本構成ドラフト（nanikiru-shorts 基盤流用 / 投稿しない）+ 公開可否レビュー | 30 秒 Shorts 構成台本ドラフト + 公開 Go/No-Go 判断材料 1 枚集約 | Day1-6 |

> 動画化（Day7）は [[candidate-002]] の nanikiru-shorts 基盤と連動。公開・課金・広告設定は本プラン外（人間判断）。

## 最初の 3 タスク（progress 投入候補・承認後のみ）

1. **B1 + B2 iPhone 実機確認 & 判定基準客観化方針**（Day1）
2. **B3 Vercel デプロイ**（Day2）
3. **B4-1 Claude Code 利用ログ蓄積**（Day3）

> これらは **approved 後に人間が progress 投入**する候補。AI は送らない（[[../progress連携基準]]）。

## 依存関係（要約）

- Day0(承認) → Day1(B1+B2) が全ての起点
- Day3-4(B4 ログ蓄積) は Day2(デプロイ) 後に並行可
- Day5(B5 UI) は Day2 後に並行可
- Day6(B8 テンプレ)・Day7(B7 動画) は Day4 後

## 未対応点 / 仮説

- 各 Day の所要は標準的な軽作業前提の仮置き
- B2 の客観化は **N-03 既知制約**。完全な客観化は難しく「観察可能な指標で裏付ける」アプローチを採る
- candidate-006 は ChatGPT 方向性レビュー前段階のため本プランは待機状態。承認まで Day1 に進めない（AI 先行着手しない）

## 次の一手

1. ChatGPT が candidate-006 を方向性レビュー（[[../../20_reviews/candidate-006_ChatGPT承認パック]]）
2. ユーザーが iPhone 実機で `90_prototypes/llm-chooser/index.html` 動作確認
3. 承認後、人間が最初の 3 タスクを progress 投入 → Claude が /focus か /vloop で実行
4. Day1 から本プランで実行（90_prototypes/llm-chooser 配下）

## 関連

- [[candidate-006]] / [[candidate-006_公開ブロッカー]] / [[candidate-006_progress投入設計]]
- [[../../20_reviews/candidate-006_ChatGPT承認パック]]
- [[../../90_prototypes/llm-chooser/README]]
- [[candidate-005_7日実行プラン]]（形式参考）
- [[candidate-002]]（nanikiru-shorts 基盤・動画流用元）
- Issue: kaeru07/vault#61（前提 #63 / #80）
