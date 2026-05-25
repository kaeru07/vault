---
type: monetization-blocker-list
title: candidate-006（llm-chooser）公開ブロッカー一覧
date: 2026-05-25
issue: kaeru07/vault#61
scenario: candidate-006
status: 調査整理のみ（実装・公開はしない）
---

# candidate-006（llm-chooser）公開ブロッカー一覧

> Issue #61 / #80。candidate-006 [[candidate-006]]（90_prototypes/llm-chooser/）の**公開できない原因を特定**する。
> **読み取り調査のみ。実装・公開・approved 化・progress 投入はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> candidate-006 は現在 `candidate`（chatgpt_pending 前段階）。本作業は**ChatGPT 方向性レビュー前の事前整理**。

> [!note] 用語注（Issue #69）
> - 公開ブロッカー = 公開を阻む問題（B1〜B8 で番号管理）
> - candidate / 有力候補 = 収益化する価値があるかもしれない案
> - chatgpt_pending = ChatGPT 方向性レビュー待ちの状態
> - approved / 承認済み = ChatGPT + あなたの判断で「実装着手 OK」になった状態
> - viewport 設定 = スマホで表示が崩れないようにする HTML の設定
> - LLM Chooser = 質問 4 種に答えて Claude / Codex / Gemini / ローカル LLM を提案するクイズ形式の静的 HTML
> - 判定基準客観化 = 「どの LLM が合うか」の判定基準を個人観察ではなく根拠データで裏付けすること（N-03 既知制約）
> - Vercel / Netlify = 静的サイトを無料層で公開できる代表サービス
> - AdSense = Google の Web 広告 SDK
> - Shorts 送客 = 短尺動画からアプリ / サイトへ誘導すること

## 調査メタ（妥当性）

- どこを調べたか: `90_prototypes/llm-chooser/`（index.html / README.md）+ [[candidate-006]] 仕様 / 2026-05-24 / n=1 試作
- 情報源種別: 内部一次（試作リポジトリ実体）+ 個人観察（Claude Code / Codex / Gemini / ローカル LLM 利用経験）
- **未検証**:
  - iPhone Safari 実機での操作・表示は未確認
  - 公開先の選定は未決定
  - 判定基準の客観化方針が未定（N-03 既知制約）
  - 広告アカウントの整備状況は未確認

## ブロッカー一覧（種別 × 優先度 × 対応コスト）

| #  | 種別      | 内容                                                                              | 状態       | 優先度   | 対応コスト                        |
| -- | ------- | ------------------------------------------------------------------------------- | -------- | ----- | ---------------------------- |
| B1 | 技術      | iPhone Safari 実機で操作・表示確認していない（クイズ 4 種のタップ可否未検証）                                | 未確認      | **高** | 低（実機で開く 1 回）                 |
| B2 | 判定基準    | 判定基準が**個人観察ベース**（Claude Code / Codex / Gemini / ローカル LLM の使い分け根拠を客観化するルールが未確定） | 確認済（未整備） | **高** | 中（観察可能な指標案 1 ページ / N-03 既知）  |
| B3 | 公開先     | デプロイ先未決定（Vercel / Netlify / GitHub Pages / Cloudflare Pages のいずれか）              | 未決定      | 中     | 低（静的構成のため数分で完了）              |
| B4 | コンテンツ   | 質問 4 種・LLM カード 4 種・比較表 9×4 のみ。**実体験ログによる根拠の上書きが必要**（B2 と連動）                    | 確認済（要整備） | 中     | 中（自分の利用ログから 1 週間分蓄積）         |
| B5 | UX      | クイズの分岐ロジックが固定（質問の重み付けが調整不可）                                                     | 確認済（要拡張） | 中     | 中（重み調整 UI 追加 / 1〜2 時間）       |
| B6 | 広告      | AdSense 申請未着手（公開 + コンテンツ蓄積後でないと申請通らない）                                          | 未着手      | 低     | 中（申請まで時間がかかる）                |
| B7 | 動画      | Shorts 送客動画なし（「あなたに合う LLM は？」1 分動画の構成未作成）                                       | 未着手      | 低     | 中（nanikiru-shorts 基盤流用検討）    |
| B8 | テンプレ販売  | 「LLM 使い分けチートシート Markdown」テンプレが未作成                                               | 未着手      | 低     | 低（既存資産 + 1〜2 時間で雛形可能）        |

> 種別の網羅: 技術(B1) / 判定基準(B2) / 公開(B3) / コンテンツ(B4,B5) / 広告動画 テンプレ(B6,B7,B8)。candidate-005 と同レベルで網羅。

## 最初の解除対象（推奨順）

1. **B1 iPhone Safari 実機確認**（最優先・低コスト）— ユーザーが iPhone でクイズ動作確認 + フォーム / カードがタップ可能か確認
2. **B2 判定基準客観化方針**（高優先）— 「個人観察を観察可能な指標に置き換える方針」1 ページ作成（N-03 既知制約解消の最初の 1 歩）
3. **B3 デプロイ先決定** — Vercel 推奨（candidate-005 と同じ理由）
4. B4 実体験ログによる根拠補強（自分の Claude Code / Codex / Gemini 利用ログから 1 週間分）
5. B5 重み調整 UI
6. B8 テンプレ販売素材
7. B7 Shorts 動画 1 本
8. B6 AdSense 申請

> B1→B2→B3 が解ければ「公開できるか + 判定の客観性」が確定する。B4〜B8 は磨き工程。

## candidate-005 / 007 との並走時の優先度

- candidate-005 は **MVP 完成度が最高**で `medium` impact のため最優先
- candidate-006 は **`low` impact + 判定基準の客観化制約**があるため candidate-005 公開後に並走着手
- candidate-007（vault-search-cheatsheet）とは別市場（AI 開発者層 vs Vault ユーザー層）のため**干渉なし**
- → candidate-005 → 006 → 007 の順で承認判断推奨（並走の選択は人間判断）

## 未対応点 / 仮説

- B1/B3 は**未確認**。実機確認とデプロイは ChatGPT 方向性承認後 + 人間判断後に実施
- B2 の判定基準客観化は **N-03 既知制約**（[[../../20_reviews/案件別ToDo一覧]] A-3 にも残）。個人観察を完全に消すのは難しく、「観察可能な指標で裏付ける」アプローチが現実的
- 「対応コスト 中」は実装規模を見ての推定（実数値未測定）
- candidate-006 は ChatGPT 方向性レビュー前段階。本ブロッカー解消の着手は **chatgpt_pending → approved 後**（AI が先行着手しない）

## 次の一手

1. ChatGPT が candidate-006 を方向性レビュー（[[../../20_reviews/candidate-006_ChatGPT承認パック]] を読む）
2. ユーザーが iPhone Safari で `90_prototypes/llm-chooser/index.html` を実機確認（B1 解除）
3. ChatGPT 方向性承認後、人間が chatgpt_pending → approved 判断
4. approved 後、B2（判定基準客観化方針）を最初の progress 投入 ToDo にする → [[candidate-006_7日実行プラン]] Day1 と一致

## 関連

- [[candidate-006]]（candidate 本体）
- [[candidate-006_7日実行プラン]]
- [[candidate-006_progress投入設計]]
- [[../../20_reviews/candidate-006_ChatGPT承認パック]]
- [[candidate-005_公開ブロッカー]]（並走候補・形式比較）
- [[candidate-007]]（相互送客候補・別市場）
- [[../収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#61（前提 #63 / #80）
