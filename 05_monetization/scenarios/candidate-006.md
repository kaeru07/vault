---
title: LLM Chooser（Claude / Codex / Gemini / ローカル 使い分けチャート）
type: monetization-scenario
status: candidate
revenueImpact: low
手段: 静的サイト公開 + 広告 (台帳A2) + テンプレ販売 (台帳B)
対象アプリ: 90_prototypes/llm-chooser（新規・MVP モック完成）
市場: AI 利用者 / AI 開発者（日本 + 英語圏）
created: 2026-05-24
updated: 2026-05-24
approvedBy:
progressLinkedAt:
issue: kaeru07/vault#61
relatedIssues: [kaeru07/vault#63]
---

## 収益化シナリオ（1 文）

質問 4 種に答えると Claude / Codex / Gemini / ローカル LLM のどれが合うかを提案する**静的 HTML 1 枚物のチャート**を公開し、SNS 拡散 + 広告 + テンプレ販売で収益化する。

## 対象アプリまたは資産

- 新規: token-chooser（[[../../90_prototypes/llm-chooser/README|MVP モック]]）
- 既存資産: nanikiru-shorts 動画パイプライン（Shorts 量産流用可）

## 想定収益源

- 静的サイト広告（Google AdSense）
- テンプレ販売（「LLM 使い分けチートシート Markdown」）
- Shorts 送客（「あなたに合う LLM は？」1 分動画 → サイト送客）
- candidate-005（token-speed-tool）との**相互送客**

## 既存資産流用ポイント

- 試作完成済（クイズ 4 種 + 全 LLM カード + 機能比較表 9×4）
- 外部 CDN ゼロ・file:// 動作・iPhone 対応 viewport 済
- candidate-005 と同じ AI 開発者層がメイン市場（束で運用可能）

## 最初の 1 作業（progress 送り用・1 つに分解）

- 作業: iPhone Safari 実機表示確認 + 判定基準の客観化方針メモ（実装はしない）
- 完了条件: iPhone でクイズ動作確認 + 「個人観察ベース判定の客観化方針」1 行メモ

## 完了条件

- MVP モックが動作する（✅ 達成）
- API なし範囲が確定（✅ 全機能 API なし）
- iPhone 実機表示確認（⏳ 未達 / B1）
- ChatGPT 方向性レビュー（⏳ 未達）
- 判定基準客観化方針（⏳ 未達 / N-03 既知制約）
- 補助 4 ファイル（公開ブロッカー / 7 日プラン / progress 投入設計 / 承認パック）（⏳ 次サイクル）
- approved（⏳ approved 化禁止ルール / 人間判断）

## 調査根拠（妥当性評価メタ）

- どこを調べたか: vloop2 試作ループ検証 Phase2 で 14 案中 N-03 として 2 位選定 / 個人観察（Claude Code / Codex / Gemini / ローカル LLM 利用体験）
- 情報源種別: 内部一次（実体験）+ 個人観察ベース判定
- 競合根拠: LLM 使い分けの判定フローを 1 ページ HTML 化した競合は確認できず（未検証 / 低信頼度）
- 鮮度: 2026-05-24 / 再現性: 個人観察は再現性低
- スコア: 収益 2 速度 5 流用 4 広告 3 動画 4 継続 2 競合回避 4 根拠 2 = **26/40**

## 妥当性評価スコア / status

- scoreTotal: **26/40**（candidate-004 25/40 と同程度・candidate-005 28/40 より低い）
- 収益化 6 軸スコア: Shorts 化 4 / MVP 速度 5 / 広告向き 3 / note 化 4 / 継続性 2 / 実装易しさ 5 = **23/30**
- status: **candidate**（試作完成済 + API なし成立だが、**判定基準が個人観察ベースで継続性難**。継続性 2/5 / 根拠 2/5 / 収益 2/5 のため revenueImpact は low）
- **approved 化しない**（人間 + ChatGPT 承認待ち）

## AI / 人間の分担

- AI（Claude Code）:
  - MVP 試作（✅ 完了）
  - 判定基準の客観化検討ドラフト（次サイクル可）
  - 補助 4 ファイル化ドラフト（次サイクル可）
- 人間承認が必要:
  - approved 昇格判断
  - 外部公開判断（Vercel デプロイ等）
  - 判定基準の妥当性最終承認
  - 広告アカウント設定

## ステータス履歴

- 2026-05-24 vloop2 N-03 として 14 案に追加 → 試作候補 3 位選定 → 静的 HTML 試作完成
- 2026-05-24 vloop6 **candidate-006 として scenarios 正規化**（本ファイル）

## candidate-005 との連動

- 両者とも AI 開発者層がメイン市場 → 相互送客可能
- candidate-005（高度な機能・体感スコア）+ candidate-006（入門的判定フロー）で**カバー範囲が異なる**
- candidate-006 → candidate-005 への送客導線が成立する仮説

## 関連

- [[../../90_prototypes/llm-chooser/README]] — MVP モック
- [[../idea_trace]] §8 — 案の追跡表での位置付け
- [[../試作ループ検証]] — 試作ループの結果
- [[candidate-005]] — 相互送客候補
- [[../既存資産_収益転用候補]] / [[../ranking-rule]] / [[../収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#61（前提）
