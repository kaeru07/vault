---
title: LLM トークン速度・体感ベンチ可視化ツール（APIなし MVP）
type: monetization-scenario
status: candidate
revenueImpact: medium
手段: アプリリリース (台帳A1) + 広告収入 (台帳A2) + note 販売 (台帳B)
対象アプリ: 90_prototypes/token-speed-tool（新規・MVP モック完成）
市場: 日本 + 英語圏（AI 開発者層）
created: 2026-05-24
updated: 2026-05-24
approvedBy:
progressLinkedAt:
issue: kaeru07/vault#60
relatedIssues: [kaeru07/vault#61, kaeru07/vault#62, kaeru07/vault#63]
---

## 収益化シナリオ（1 文）

外部 LLM API を呼ばずに **tokens/sec / TTFT / 体感スコア**を比較できるブラウザツールを公開し、AI 開発者層への広告・note 販売・Shorts 送客で収益化する。

## 対象アプリまたは資産

- **新規アプリ**: token-speed-tool（[[../token-speed-tool|仕様 + trace]]）
- **MVP モック**: [[../../90_prototypes/token-speed-tool/README|静的 HTML+JSON 試作]]（外部 CDN ゼロ・サンプル 5 件動作確認済み）
- 関連: [[candidate-001]]（mahjong 本命）と並走候補・競合しない

## 想定収益源

- アプリ広告（無料層 / Google AdSense or 自家広告）
- note 販売（「2026 年 Q2 LLM ベンチ結果まとめ」「ローカル LLM 速度実測」等の月次レポート）
- YouTube Shorts 送客（速度比較動画 1 分 → アプリ送客）
- 将来オプション: プレミアム機能（履歴無制限 / 一括 CSV 取り込み / Bring your own key の自動ベンチ）

## 既存資産流用ポイント

- **nanikiru-shorts 動画パイプライン**（速度比較 Shorts 量産に流用可）
- **Claude Code / Codex 実体験データ**（自前ベンチの一次データになる）
- **Vault の見方ガイド / progress テンプレ知見**（note 化に流用可）
- **既存 Web スタック想定**: 静的 HTML + JSON 構成のため Vercel 無料層でデプロイ可

## 最初の 1 作業（progress 送り用・1 つに分解）

- **作業**: token-speed-tool MVP モックの iPhone 実機表示確認 + 体感スコア計算式の暫定値 vs 実測値レビュー（実装はしない・観察と式の調整方針メモのみ）
- **完了条件**:
  - iPhone Safari で `index.html` を開きサンプル 5 件比較表示できることをスクショで確認
  - 入力フォーム / Export / Import / グラフが指でタップ可能なサイズかチェック
  - 体感スコア計算式の重み（0.4 / 0.4 / 0.2）が直感に合うか・要調整かを 1 行メモ

## 完了条件（候補-005 自体）

- MVP モックが動作する（✅ 達成）
- APIなし範囲が確定している（✅ 達成 → [[../token-speed-tool]] §2-1）
- 着想経路 trace が文書化されている（✅ 達成 → 同 §1）
- iPhone 実機表示が確認されている（⏳ 未達）
- ChatGPT 方向性レビューを通過している（⏳ 未達）
- 7 日実行プランがある（⏳ 次サイクル）
- 公開ブロッカーが整理されている（⏳ 次サイクル）

## 調査根拠（妥当性評価メタ）

- **どこを調べたか**:
  - HN「How fast is N tokens/s really」（ideaId 20260521-003 / [[../idea_pool/2026-05-21.ndjson]]）
  - iTunes Search JP `term=何切る`（参考：ニッチ市場の空白確認）
  - Claude Code / Codex 自体験（社内一次データ）
  - 上位 5 案収益化スコアリング（[[../../06_research/2026-05-22_上位5案追加調査]] §9）
- **情報源種別**: HN（外部一次）+ 内部一次（実体験）+ 推測値（一部）
- **競合根拠**: 既存ベンチサイト（OpenRouter / Artificial Analysis 等）は数値羅列で UX 悪い。**専業 iOS / Web アプリは事実上不在**（iTunes Search `benchmark llm` で確認 / サンプル少なく信頼度 low）
- **鮮度**: 2026-05-24 起票・2026-05-21 idea_pool 由来
- **再現性**: 高（HN URL / Claude Code 実体験 / iTunes Search 全て再取得可）
- **スコア**: 収益 3 速度 4 流用 3 広告 4 動画 4 継続 3 競合回避 4 根拠 3 = **28/40**

## 妥当性評価スコア / status

- scoreTotal: **28/40**（candidate-004 25/40 より高い・candidate-001 補強済を除けば 4 件中 1 位）
- 収益化 6 軸スコア（[[../../06_research/2026-05-22_上位5案追加調査]] §9 と同基準で再計算）: Shorts 化 4 / MVP 速度 5 / 広告向き 4 / note 化 4 / 継続性 3 / 実装易しさ 5 = **25/30**（candidate-001 24/30 と並ぶ高さ）
- status: **candidate**（収益期待 medium・MVP モック動作確認済・APIなし範囲確定。**approved にはしない**・人間 + ChatGPT 承認待ち）

## AI / 人間の分担

- **AI（Claude Code）**:
  - MVP モック実装（✅ 完了）
  - 仕様書 + trace 文書化（✅ 完了）
  - 7 日実行プランドラフト（次サイクル可）
  - 公開ブロッカー洗い出しドラフト（次サイクル可）
  - ChatGPT 承認パック化ドラフト（次サイクル可）
- **人間承認が必要**:
  - approved 昇格判断
  - Vercel / Netlify 等への**外部公開判断**
  - 広告アカウント設定（AdMob / AdSense）
  - note 販売の開始判断
  - 体感スコア計算式の最終承認

## ステータス履歴

- 2026-05-21 idea（idea_pool 登録 / 粗 score 10 / 20260521-003）
- 2026-05-22 hold（[[../../06_research/2026-05-22_上位5案追加調査]] §2「AI 分野継続性難 + 競合確立想定」で深掘り対象外）
- 2026-05-22 スコア再評価 17/30（中位・hold 継続）
- 2026-05-24 「APIなし前提で再評価」指示で Issue #60 起票 → MVP モック試作完了 → candidate 化判定 OK
- 2026-05-24 **candidate-005 として正規化**（本ファイル新規）

## 候補-001 との並走判断

- **競合しない**: 主市場が異なる（candidate-001 = 麻雀学習者 / candidate-005 = AI 開発者）
- **相互送客可能**: 両方で Shorts 経由送客可能
- **MVP 速度**: candidate-005 の方が早い（静的 HTML 1 ファイルで動く）
- **収益化直接性**: candidate-001 の方が高い（既存資産流用度大）
- **本命と並走**の位置付け → candidate-001 完了を待たず並行進行可

## 関連

- [[../token-speed-tool]] — 仕様 + trace（本候補の正本）
- [[../../90_prototypes/token-speed-tool/README]] — MVP モック
- [[../idea_trace]] §2 — 全案ハブ上での位置付け
- [[../試作ループ検証]] — 試作ループ Phase1-5 検証
- [[../../06_research/2026-05-22_上位5案追加調査]] §9 — 収益化スコアリング
- [[candidate-001]] — 並走候補
- [[../既存資産_収益転用候補]] / [[../ranking-rule]] / [[../収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#60 / #61 / #62 / #63
