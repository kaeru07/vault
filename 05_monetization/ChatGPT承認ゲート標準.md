---
title: ChatGPT 承認ゲート標準
type: monetization-design
issue: kaeru07/vault#18
created: 2026-05-19
updated: 2026-05-19
status: active
---

# ChatGPT 承認ゲート標準

> Issue #18。**大きな方向性の承認は ChatGPT（人間の意思決定チャネル）で行い、Claude は判断材料作成に集中**する。
> [[収益化シナリオ承認フロー]] を「誰が」「どこで」承認するかの観点で標準化。本書が承認運用の正本参照。

## 役割分担（厳守）

| 主体 | できること | できないこと |
|---|---|---|
| Claude Code | 推奨・比較・仮説・ブロッカー洗い出し・計画作成（判断材料） | approved 化 / 承認確認 / 承認待ちを人間に問う |
| ChatGPT | 大きな方向性の承認・却下・保留の意思決定 | （実装はしない・指示元） |
| 人間（owner/pm） | 最終承認の確定（status 書換）・公開/課金判断 | — |

- **Claude は approved 化しない・承認確認をしない・推奨/比較/仮説のみ**（/vloop 確認禁止ルールと一致）
- 大きな方向性（どの candidate を進めるか・撤退判断）は **ChatGPT で決定**

## 承認フロー図

```
[Claude] 調査・候補起票・比較・推奨・ブロッカー・計画（判断材料）
   │  status: candidate / pending_approval（Claudeはここまで）
   ▼
[chatgpt_pending] ChatGPT 承認待ち（判断材料が揃った状態・新設の論理状態）
   │  ChatGPT が大きな方向性を判断（approve / reject / hold）
   ▼
[人間] status を approved / rejected / hold に書換（decision-log 追記）
   │  approved のみ
   ▼
[progress] 投入 ToDo 化（progress連携基準）→ 実装（apps/ 各リポジトリ）
```

## ChatGPT 承認待ち状態（chatgpt_pending）の定義

- 位置づけ: `pending_approval` のうち**判断材料が揃い ChatGPT レビュー可能**なもの
- 表現: シナリオ frontmatter は `status: pending_approval` のまま、
  別途 `gate: chatgpt_pending` を任意付与（既存 status 体系を壊さない）
- 揃える判断材料（Claude が用意）: 比較表 / 推奨順位 / 公開ブロッカー / 7日プラン / 妥当性スコア
- 解除: ChatGPT 判断 → 人間が `status:` を approved/rejected/hold へ（[[収益化シナリオ承認フロー]]）

## approved 化条件（整理）

approved にできるのは **人間のみ**。かつ以下すべてを満たすとき:

1. 判断材料が揃っている（比較・推奨・ブロッカー・計画）= chatgpt_pending 到達
2. ChatGPT が方向性を承認している
3. 承認フロー最小確認項目（収益化インパクト / 調査根拠 / 対象明確 / 1作業分解可 / 完了条件）クリア
4. [[decision-log]] に判断理由が記録される（[[../90_templates/承認判断テンプレート]]）

> 1つでも欠けたら approved にしない（candidate / pending_approval / hold に留める）。

## vloop 運用への反映

- vloop は `candidate / pending_approval` を**実装対象にしない**（既存・[[../03_prompts/claude-commands/vloop]] 重要ルール）
- vloop が候補を進める = **chatgpt_pending までの判断材料を作る**ところまで（approved 化しない）
- approved 後のみ vloop は progress 投入 ToDo を実作業対象にできる
- vloop.md に本書への相互参照を1行追記（挙動変更なし・既存ルールの明文化）

## 現状適用（2026-05-19）

- candidate-001: 判断材料完備（[[../20_reviews/2026-05-19_承認候補選定]] 推奨 / [[scenarios/candidate-001_公開ブロッカー]] / [[scenarios/candidate-001_7日実行プラン]]）→ **chatgpt_pending 相当**。ChatGPT 承認待ち
- candidate-002 / 004: pending（材料一部）/ 003: 要再調査

## 未対応点 / 仮説
- `gate: chatgpt_pending` は frontmatter 任意フィールドで導入（既存 status enum を変更しない＝非破壊の仮説）
- ChatGPT 承認の記録場所は decision-log に集約（ChatGPT 側ログは GitHub Issue コメント/レビューを正とする仮説）

## 次の一手
1. ChatGPT が candidate-001 の方向性を承認するか判断
2. 承認時、人間が candidate-001 の `status:` を approved に書換 → decision-log 追記
3. [[ChatGPT承認待ちテンプレート]]（Issue #19）と本書をセット運用

## 関連
- [[収益化シナリオ承認フロー]] / [[decision-log]] / [[../20_reviews/2026-05-19_承認候補選定]]
- [[../03_prompts/claude-commands/vloop]]（vloop 運用反映先）
- Issue: kaeru07/vault#18（関連 #17 / #19 / #20）
