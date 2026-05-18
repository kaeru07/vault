---
type: monetization-blocker-list
title: candidate-001（mahjong）公開ブロッカー一覧
date: 2026-05-19
issue: kaeru07/vault#15
scenario: candidate-001
status: 調査整理のみ（実装・公開はしない）
---

# candidate-001（mahjong）公開ブロッカー一覧

> Issue #15。承認推奨候補 [[candidate-001]]（apps/mahjong）の**公開できない原因を特定**する。
> **読み取り調査のみ。実装・公開・approved 化・progress 投入はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> candidate-001 は現在 `pending_approval`（未承認）。本作業は承認判断の材料。

## 調査メタ（妥当性）
- どこを調べたか: `apps/mahjong/`（README / package.json / vercel.json / public/ / next.config / git log）/ 2026-05-19 / n=1リポジトリ
- 情報源種別: 内部一次（リポジトリ実体）
- **未検証**: `npm run build` の成否・本番 Vercel ライブ状態は本タスクで実行/確認していない（「未確認」と明記。確認済みと書かない）

## ブロッカー一覧（種別 × 優先度 × 対応コスト）

| # | 種別 | 内容 | 状態 | 優先度 | 対応コスト |
|---|---|---|---|---|---|
| B1 | 技術 | `npm run build` の成否未検証（本タスクで未実行） | 未確認 | **高** | 低（build 1回） |
| B2 | 技術 | 本番 Vercel デプロイ状態未確認（vercel.json はあり＝設定済の可能性） | 未確認 | 高 | 低（デプロイ確認） |
| B3 | ストア/公開 | README が create-next-app デフォルトのまま（アプリ説明・ASO 文面なし） | 確認済（要整備） | 中 | 中（文面作成） |
| B4 | 素材不足 | `public/` が Next.js デフォルト svg のまま。アプリアイコン / OGP / スクショ未整備 | 確認済（要整備） | 中 | 中（素材作成） |
| B5 | 動画不足 | ストア/SNS 用の紹介・何切る動画なし | 確認済（未着手） | 低 | 中（candidate-002 と連動可） |
| B6 | 対応コスト | 上記の合計はおおむね軽量〜中。新規開発は不要（既存実装あり） | 推定 | — | 全体 中 |

> 種別の網羅: 技術(B1,B2) / ストア公開(B3) / 素材不足(B4) / 動画不足(B5) / 対応コスト(B6)。Issue #15 の観点を全カバー。

## 最初の解除対象（推奨順）

1. **B1 build 検証**（最優先・低コスト）— 公開可否の前提。失敗なら原因がさらにブロッカー化
2. **B2 デプロイ状態確認** — vercel.json ありなので「既にデプロイ済か」を一次確認するだけで前進
3. B3 README/ストア文面ドラフト（AI ドラフト可・公開はしない）
4. B4 素材（アイコン/OGP/スクショ）整備
5. B5 紹介動画（candidate-002 の Shorts 基盤と統合検討）

> B1→B2 が解ければ「公開できるか」が確定する。B3〜B5 は公開を磨く工程。

## 未対応点 / 仮説
- B1/B2 は**未確認**。build 実行・デプロイ確認は別アプリ(apps/mahjong)での作業のため、承認後に当該リポジトリ内で実施すべき（company CLAUDE.md 原則／本タスクは Vault への洗い出しに限定）
- 「対応コスト 中」は実装規模を見ての推定（実数値未測定）
- candidate-001 は未承認。本ブロッカー解消の着手は **approved 後に progress 投入してから**（AI が先行着手しない）

## 次の一手
1. 人間が candidate-001 を approved にするか判断（[[../../20_reviews/2026-05-19_承認候補選定]] の推奨に基づく）
2. approved 後、B1（build 検証）を最初の progress 投入 ToDo にする（[[../progress追加ToDo案]] ToDo#1 と一致）
3. Issue #16（7日実行プラン）で B1→B2→B3 の順を日次に落とす

## 関連
- [[candidate-001]] / [[../../20_reviews/2026-05-19_承認候補選定]] / [[../progress追加ToDo案]] / [[../収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#15（前提 #14 / 後続 #16）
