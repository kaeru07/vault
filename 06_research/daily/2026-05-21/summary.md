---
date: 2026-05-21
sources_collected: [ai-news, reddit, app-store-ranking]
total_items: 44
note: Phase 1 完全化 1 サイクル（Issue #48）/ 3 源稼働
---

# 2026-05-21 daily summary（3 源版・Phase 1 完全化）

## ソース別件数

| source | 件数 | 失敗 | 取得手段 |
|---|---|---|---|
| ai-news（Hacker News RSS） | 15 | 0 | curl RSS |
| reddit（r/SideProject top/day） | 15 | 0 | curl 公開 JSON + jq |
| app-store-ranking（iTunes Search API JP / mahjong） | 14 | 0 | curl 公式 API + jq |

合計 **44 件**取得。3 源とも 1 リクエストずつ・規約配慮（UA 明示・低頻度・無認証）。

## 上位トレンド分布（dedupKey 集約後）

### AI / 開発系（HN RSS 由来）
- OpenAI 数学理論反証 / Qwen3.7-Max エージェント / Show HN CPU 文字起こし / Formal Verification Gates / VSCode 拡張侵害

### 個人開発・SaaS（Reddit r/SideProject 由来）
- TV 広告自動ミュート装置 / Kickstarter Reddit 流入 / 1 時間サイドプロジェクト 18k 売上 / AI 在宅筋トレ / 株式ニュースレター

### 日本市場・麻雀アプリ（iTunes Search 由来）
- 雀魂 (807969 reviews / トップ) / 麻雀格闘倶楽部Sp (94096) / ヴィータ麻雀 (71430) / 麻雀闘龍 (37526) / Mahjong City Tours (30244)
- **何切る系専業は本検索（mahjong 一般）には少数**。雀魂のような汎用ゲーム + パズル系（ソリティア型）が市場上位

## 異常検知

- レート超過: なし（各源 1 リクエストのみ）
- 規約変更兆候: なし
- 取得 0 件: なし
- 機密混入: なし（公開記事/投稿/ストアメタのみ）
- 個人情報: なし

## 注記（candidate-001 視点）

- 雀魂・麻雀格闘倶楽部 は超大手で**何切る専業ではない**
- 検索クエリ `mahjong` 一般では「何切る」アプリは上位 14 件に出ず → **何切る専業はニッチ市場**であることを再確認
- candidate-001（mahjong 何切る公開）の差別化軸として「何切るに特化」「AI 解析」は依然有効仮説

## 関連
- [[../../05_monetization/scenarios/candidate-001]]（差別化軸の参照）
- [[../無料情報源_取得方法比較]]（Phase 1 推奨 3 源と一致確認）
- [[../logs/research-run-log]] 追記済
