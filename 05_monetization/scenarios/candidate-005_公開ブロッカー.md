---
type: monetization-blocker-list
title: candidate-005（token-speed-tool）公開ブロッカー一覧
date: 2026-05-24
issue: kaeru07/vault#60
scenario: candidate-005
status: 調査整理のみ（実装・公開はしない）
---

# candidate-005（token-speed-tool）公開ブロッカー一覧

> Issue #60。candidate-005 [[candidate-005]]（90_prototypes/token-speed-tool/）の**公開できない原因を特定**する。
> **読み取り調査のみ。実装・公開・approved 化・progress 投入はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> candidate-005 は現在 `candidate`（chatgpt_pending にも上げていない）。本作業は**ChatGPT 方向性レビュー前の事前整理**。

## 調査メタ（妥当性）

- どこを調べたか: `90_prototypes/token-speed-tool/`（index.html / sample-data.json / README.md）+ [[../token-speed-tool]] 仕様書 / 2026-05-24 / n=1 試作
- 情報源種別: 内部一次（試作リポジトリ実体）+ HN URL（情報源 trace）
- **未検証**:
  - iPhone Safari 実機での操作・表示は未確認
  - 公開先（Vercel / Netlify / GitHub Pages）の選定は未決定
  - 広告アカウント（AdMob / AdSense）の整備状況は未確認
  - note 販売プラットフォームの選定（note.com / Booth / Gumroad）は未決定

## ブロッカー一覧（種別 × 優先度 × 対応コスト）

| # | 種別 | 内容 | 状態 | 優先度 | 対応コスト |
|---|---|---|---|---|---|
| B1 | 技術 | iPhone Safari 実機で操作・表示確認していない（viewport 設定済だが実機未検証）| 未確認 | **高** | 低（実機で開く 1 回）|
| B2 | 公開先 | デプロイ先未決定（Vercel / Netlify / GitHub Pages / Cloudflare Pages のいずれか）| 未決定 | **高** | 低（静的構成のため数分で完了）|
| B3 | ドメイン | 独自ドメインの要否未決定（無料層 subdomain でも成立）| 未決定 | 中 | 中（要なら取得 + DNS）|
| B4 | コンテンツ | サンプルデータが 5 件のみ。**実 LLM 体験データの追加蓄積が必要**（自分の Claude Code / Codex / Gemini / ローカル LLM 実測）| 確認済（要整備）| 中 | 中（毎日 1〜2 件追加 / 自分の作業ついでに）|
| B5 | UX | ログ貼付解析の対応フォーマットが最小（Claude Code / Codex 風のみ）。Ollama / OpenAI Playground / llama.cpp 形式未対応 | 確認済（要拡張）| 中 | 中（パーサ追加 / 1 フォーマット 1〜2 時間）|
| B6 | 広告 | 広告枠の設置未実装（AdSense 申請は公開 + コンテンツ蓄積後）| 未着手 | 低 | 中（申請まで時間がかかる）|
| B7 | 動画 | Shorts 送客動画なし（速度比較動画のテンプレ未作成）| 未着手 | 低 | 中（nanikiru-shorts 基盤流用検討）|
| B8 | note 化 | 「2026 年 Q2 LLM ベンチ結果まとめ」note の素材なし（B4 と連動）| 未着手 | 低 | 中（B4 が進めば自動的に素材化）|
| B9 | 規約 | 公開ベンチデータの**取り込み方法と出典明示の規約**未整理（無断転載リスク）| 未確認 | 中 | 低（手動取り込み + 出典必須ルール明文化のみ）|
| B10 | 対応コスト | 上記の合計はおおむね軽量〜中。新規開発不要（試作完成済）| 推定 | — | 全体 中 |

> 種別の網羅: 技術(B1)/ 公開(B2,B3)/ コンテンツ(B4,B5)/ 広告動画 note(B6,B7,B8)/ 規約(B9)/ 対応コスト(B10)。candidate-001 と同レベルで網羅。

## 最初の解除対象（推奨順）

1. **B1 iPhone Safari 実機確認**（最優先・低コスト）— ユーザーが iPhone で開いてサンプル 5 件比較表示 + 入力フォーム + Export/Import がタップ可能か確認
2. **B2 デプロイ先決定** — Vercel 推奨（GitHub repo から自動デプロイ・無料層で十分・静的構成と相性最良）
3. **B9 規約整理** — 公開前に「手動取り込み + 出典必須」の運用ルールを明文化（B4 / B5 の前提）
4. B4 サンプルデータ拡充（自分の実測 10〜20 件まで）
5. B5 ログ解析拡張（Ollama / OpenAI Playground 対応）
6. B7 Shorts 動画 1 本（nanikiru-shorts 基盤流用検討）
7. B6 AdSense 申請（コンテンツ蓄積後・申請承認に時間）
8. B8 note 1 本目（B4 の実測が溜まったら）

> B1→B2→B9 が解ければ「公開できるか」が確定する。B4〜B8 は公開を磨く + 収益導線を整える工程。

## candidate-001 との並走時の優先度

- candidate-001 が **build 検証 → ストア公開**フローのため、Day1-2 で「公開できるか」が確定する
- candidate-005 は **既に MVP が動く + 静的構成**のため、**B1 + B2 だけで「公開できる」状態に到達**
- → candidate-001 と candidate-005 は**並走で進めて問題ない**（candidate-001 の build 検証中に candidate-005 を iPhone で確認するだけで前進）

## 未対応点 / 仮説

- B1/B2 は**未確認**。実機確認とデプロイは別作業のため、人間が iPhone で開いて確認後 / ChatGPT 承認後にデプロイ判断
- 「対応コスト 中」は実装規模を見ての推定（実数値未測定）
- candidate-005 は ChatGPT 方向性レビュー前段階。本ブロッカー解消の着手は **chatgpt_pending → approved 後**（AI が先行着手しない）
- B4（サンプル拡充）は AI が承認なしに進めることは可能（自分の Claude Code 実行ログを記録するだけ）。ただし**公開はしない**

## 次の一手

1. ChatGPT が candidate-005 を方向性レビュー（[[../../20_reviews/candidate-005_ChatGPT承認パック]] を読む）
2. ユーザーが iPhone Safari で `90_prototypes/token-speed-tool/index.html` を実機確認（B1 解除）
3. ChatGPT 方向性承認後、人間が chatgpt_pending → approved 判断
4. approved 後、B2（Vercel デプロイ）を最初の progress 投入 ToDo にする → [[candidate-005_7日実行プラン]] Day1 と一致

## 関連

- [[candidate-005]]（candidate 本体）
- [[candidate-005_7日実行プラン]]
- [[candidate-005_progress投入設計]]
- [[../../20_reviews/candidate-005_ChatGPT承認パック]]
- [[candidate-001_公開ブロッカー]]（並走候補・形式比較）
- [[../収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#60（前提 #61 / #62 / #63）
