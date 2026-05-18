---
type: monetization-execution-plan
title: candidate-001（mahjong）承認後 7日実行プラン
date: 2026-05-19
issue: kaeru07/vault#16
scenario: candidate-001
status: 計画のみ（承認後に着手・progress投入は人間）
---

# candidate-001（mahjong）承認後 7日実行プラン

> Issue #16。candidate-001 が **approved になった後**すぐ動ける状態を作る計画。
> **計画のみ。approved 化・progress 投入・本番実装はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> 起点は [[candidate-001_公開ブロッカー]] の解除順（B1→B2→B3→B4→B5）。**Day1 は「承認済みであること」が前提条件**。

## 前提（Day0・人間）
- candidate-001 が `approved`（[[../../20_reviews/2026-05-19_承認候補選定]] の推奨に基づき人間が判断）
- 作業は apps/mahjong リポジトリ内で完結（company CLAUDE.md 原則）。Vault には記録のみ

## 7日プラン（1日単位ToDo / 完了条件 / 依存）

| Day | ToDo | 完了条件 | 依存 |
|---|---|---|---|
| Day1 | B1: `npm run build` を実行し成否確認 | build 成功 or 失敗原因が記録されている | 承認(Day0) |
| Day2 | B2: Vercel デプロイ状態確認（未デプロイなら手順整理） | 本番URL有無が確定／無ければデプロイ手順案 | Day1 build OK |
| Day3 | B3: README→ストア/紹介文ドラフト（公開はしない） | アプリ説明・ASO 文面ドラフト完成 | Day2 |
| Day4 | B4-1: アプリアイコン/OGP 素材の要件整理＋ドラフト | アイコン/OGP の素案 | Day3 |
| Day5 | B4-2: スクリーンショット取得（dev/本番画面） | 主要画面スクショ一式 | Day2 |
| Day6 | B5: 紹介/何切る動画の構成1本（candidate-002 基盤流用・投稿しない） | 構成ドラフト1本 | Day3 |
| Day7 | 公開可否レビュー＋残課題整理（公開判断は人間） | 公開Go/No-Go判断材料が1枚に集約 | Day1-6 |

> 動画化（Day6）は [[candidate-002]] の nanikiru-shorts 基盤と連動。公開・課金・広告設定は本プラン外（人間判断）。

## 最初の3タスク（progress投入候補・承認後のみ）

1. **B1 build 検証**（Day1）— [[../progress追加ToDo案]] ToDo#1 と一致
2. **B2 デプロイ状態確認**（Day2）
3. **B3 ストア/紹介文ドラフト**（Day3）

> これらは **approved 後に人間が progress 投入**する候補。AI は送らない（[[../progress連携基準]]）。

## 依存関係（要約）
- Day0(承認) → Day1(build) が全ての起点。build NG なら Day2 以降を保留し原因対応を差し込む
- Day3(文面)・Day4-5(素材)・Day6(動画) は Day2 後に並行可
- Day7 の公開判断は人間（外部公開は /vloop 停止条件）

## 未対応点 / 仮説
- 各 Day の所要は標準的な軽作業前提の仮置き（build NG 時はリスケ）
- candidate-001 未承認のため本プランは待機状態。承認まで Day1 に進めない（AI 先行着手しない）
- B2 で「既にデプロイ済」が判明した場合 Day2 は短縮し Day3 以降を前倒し（仮説）

## 次の一手
1. 人間が candidate-001 を approved 判断（[[../decision-log]] 追記）
2. approved 後、最初の3タスクを [[../progress追加ToDo案]] 経由で progress 投入
3. Day1 から本プランで実行（apps/mahjong リポジトリ内）

## 関連
- [[candidate-001]] / [[candidate-001_公開ブロッカー]] / [[../../20_reviews/2026-05-19_承認候補選定]]
- [[../progress追加ToDo案]] / [[../収益化シナリオ承認フロー]] / [[candidate-002]]
- Issue: kaeru07/vault#16（前提 #14 / #15）
