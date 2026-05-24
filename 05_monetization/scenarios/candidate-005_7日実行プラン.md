---
type: monetization-execution-plan
title: candidate-005（token-speed-tool）承認後 7 日実行プラン
date: 2026-05-24
issue: kaeru07/vault#60
scenario: candidate-005
status: 計画のみ（承認後に着手・progress 投入は人間）
---

# candidate-005（token-speed-tool）承認後 7 日実行プラン

> Issue #60。candidate-005 が **approved になった後**すぐ動ける状態を作る計画。
> **計画のみ。approved 化・progress 投入・本番実装はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> 起点は [[candidate-005_公開ブロッカー]] の解除順（B1→B2→B9→B4→B5→B7→B6→B8）。

## 前提（Day0・人間 + ChatGPT）

- candidate-005 が `approved`（ChatGPT 方向性承認 → 人間 status 確定）
- 作業は `90_prototypes/token-speed-tool/` 配下で完結（既存 vault リポジトリ内・別アプリ作成不要）
- 公開先 Vercel 想定（無料層・静的構成と相性最良）

## 7 日プラン（1 日単位 ToDo / 完了条件 / 依存）

| Day | ToDo | 完了条件 | 依存 |
|---|---|---|---|
| Day1 | B1: iPhone Safari 実機確認 + B9 規約明文化 | iPhone でサンプル 5 件比較表示 / フォーム / Export / Import 動作確認 + 「手動取り込み + 出典必須」ルール 1 行記録 | 承認(Day0) |
| Day2 | B2: Vercel デプロイ（無料層 / GitHub 連携） | 本番 URL 確定 / iPhone でも本番 URL で動く | Day1 |
| Day3 | B4-1: 自分の実測データ追加（Claude Code / Codex 各 2-3 件 = 計 5-6 件） | sample-data.json に 10 件以上 / source: own-log の比率増 | Day2 |
| Day4 | B5: ログ解析拡張（Ollama 出力形式対応） | Ollama 形式ログを貼り付けると model / tps / ttft が抽出される | Day2（並行可）|
| Day5 | B7: 速度比較動画 1 本構成ドラフト（nanikiru-shorts 基盤流用 / 投稿しない）| 30 秒 Shorts 構成台本ドラフト | Day3 |
| Day6 | B4-2: Gemini AI Studio / ローカル LLM 実測 3-5 件追加 | sample-data.json に 15 件以上 / 4 LLM カバー | Day3 |
| Day7 | 公開可否レビュー + 残課題整理（公開判断は人間）| Go/No-Go 判断材料が 1 枚に集約 / 30 日プランへ接続 | Day1-6 |

> Day8 以降の note 化（B8）/ AdSense 申請（B6）は **30 日プラン**へ。本 7 日は「公開できる状態 + 最初のコンテンツ + 動画ドラフト」までで完結。

## 最初の 3 タスク（progress 投入候補・承認後のみ）

1. **B1 iPhone 実機確認 + B9 規約明文化**（Day1）— ユーザー操作 + 1 行記録のみ
2. **B2 Vercel デプロイ**（Day2）— `vercel --prod` 1 回 + URL 共有
3. **B4-1 実測 5-6 件追加**（Day3）— 自分の Claude Code / Codex 利用ログから手入力

> これらは **approved 後に人間が progress 投入**する候補。AI は送らない（[[../progress連携基準]]）。
> Day1 は人間操作（実機確認 + ルール記録）/ Day2 以降は AI と人間の共同。

## 依存関係（要約）

- Day0（承認）→ Day1（実機確認 + 規約）が全ての起点。実機 NG なら Day2 以降を保留し原因対応を差し込む
- Day3（実測追加）・Day4（ログ解析拡張）は Day2 後に並行可
- Day5（動画ドラフト）は Day3 完了後（実測データを動画素材として使う想定）
- Day7 の公開判断は人間（外部公開は /vloop 停止条件）

## candidate-001 との並走スケジュール（想定）

| Day | candidate-001（mahjong） | candidate-005（token-speed-tool） | 並走可能性 |
|---|---|---|---|
| Day1 | B1 build 検証 | B1 iPhone 実機 + B9 規約 | ✅ 別作業・干渉なし |
| Day2 | B2 デプロイ状態確認 | B2 Vercel デプロイ | ✅ 別 repo（apps/mahjong vs vault repo）|
| Day3 | B3 README/ASO ドラフト | B4-1 実測追加 | ✅ |
| Day4 | B4-1 アイコン/OGP | B5 ログ解析拡張 | ✅ |
| Day5 | B4-2 スクショ | B7 動画構成 | ✅ |
| Day6 | B5 動画構成 | B4-2 実測 + 4 LLM | ✅ |
| Day7 | 公開 Go/No-Go | 公開 Go/No-Go | ✅ 同日判断可 |

> **完全並走可能**。両者とも別 repo / 別ドメインで干渉しない。candidate-001 が**本命（公開ブロッカー解消で広告収益）**、candidate-005 が**並走候補（早期試作 → 早期広告 + note）**。

## 未対応点 / 仮説

- 各 Day の所要は標準的な軽作業前提の仮置き（B2 NG 時はリスケ）
- candidate-005 未承認のため本プランは待機状態。承認まで Day1 に進めない（AI 先行着手しない）
- B2 デプロイは Vercel 想定だが、GitHub Pages / Cloudflare Pages も静的構成で動く。最終選定は人間判断
- B4 実測データは「自分の Claude Code 利用ログを記録するだけ」のため、承認なしに AI で進めることは可能（ただし**公開はしない**）

## 次の一手

1. ChatGPT が candidate-005 を方向性承認（[[../../20_reviews/candidate-005_ChatGPT承認パック]] を読む）
2. 人間が approved 判断（[[../decision-log]] 追記）
3. approved 後、最初の 3 タスクを progress 投入候補にする
4. Day1 から本プランで実行（vault repo 内 / iPhone 実機 / Vercel）

## 関連

- [[candidate-005]]
- [[candidate-005_公開ブロッカー]]
- [[candidate-005_progress投入設計]]
- [[../../20_reviews/candidate-005_ChatGPT承認パック]]
- [[candidate-001_7日実行プラン]]（並走候補・形式比較）
- [[../progress連携基準]]
- Issue: kaeru07/vault#60（前提 #61 / #62 / #63）
