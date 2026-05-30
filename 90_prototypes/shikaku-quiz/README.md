---
title: 資格クイズ — ITパスポート対策 MVP（新規アプリ）
type: prototype
created: 2026-05-31
updated: 2026-05-31
status: MVP 実装完成（動作版）
tags: [prototype, quiz, shikaku, study, monetization, no-api]
---

# 資格クイズ — ITパスポート対策 MVP

> 完全新規アプリ（既存コードなし・作りかけではない）。Vault横断分析の延長で、収益化しやすい「資格/学習アプリ案」を**実際に動くMVPとして実装**したもの。
> 静的 HTML 1 枚・外部 CDN ゼロ・オフライン / file:// 動作。

## ファイル構成

| ファイル | 役割 |
|---|---|
| `index.html` | UI + ロジック + 問題データ（HTML/CSS/JS インライン） |
| `README.md` | 本ファイル（使い方・収益化・既知制約・次の改善） |

## 使い方

### ローカル表示（PC）
```
cd /root/company/obsidian-sync-vault/90_prototypes/shikaku-quiz
open index.html   # またはブラウザにドラッグ&ドロップ
```

### iPhone（Obsidian Sync 経由）
ob sync 後、iPhone の Obsidian でこのフォルダの `index.html` を確認。

### 公開（収益化時）
Vercel / Netlify / GitHub Pages / Cloudflare Pages のいずれかに `index.html` を置くだけ（ビルド不要）。

## 機能（MVP）

- 分野選択（全分野 / ストラテジ / マネジメント / テクノロジ）
- 4 択出題 → 即時正誤 + 解説 → 次へ
- 出題順シャッフル / 進捗バー / 現在スコア
- 結果画面：総合スコア・正答率・**分野別正答率**
- 自己ベストを localStorage に保存
- 広告枠（AdSense）プレースホルダ 2 箇所（スタート / 結果下）

## 収益化導線

- **チャネル**: Web 公開 → Google AdSense（学習系は滞在・反復が多く相性可）
- **市場**: ITパスポートは受験者数が多く、すきま時間の反復需要が大きい（日本語・競合あり）
- **拡張**: 問題数追加で回遊増 → 後から「広告なし版」課金 / 他資格（簿記3級・基本情報・宅建 等）へ横展開
- **送客**: 1 問を画像/Shorts 化して SNS → サイト誘導（nanikiru-shorts のパイプライン流用余地）

## 既知制約 / 未確認

- 問題は **MVP 用の自作サンプル 12 問**。過去問の転載はしていない（著作権配慮）。**本番公開時は内容監修が必須**（未確認）。
- 実 DL / PV / 収益額は未測定（公開前のため推測しない）。
- AdSense の実ユニット ID は未設定（プレースホルダのみ）。
- 競合 ASO 調査は未実施（[[../../06_research/GooglePlay市場調査]] と接続余地）。

## 次の改善

1. 問題数を 30〜50 問へ拡充（分野バランス維持）
2. 「間違えた問題だけ復習」モード
3. 他資格テンプレ化（問題 JSON を差し替えるだけで別資格アプリ）
4. AdSense 実装 + 公開（Go/No-Go は人間判断）

## 関連

- [[../../05_monetization/収益化候補一覧]]
- [[../../06_research/GooglePlay市場調査]]
- [[../../02_apps/途中プロジェクト一覧]]
