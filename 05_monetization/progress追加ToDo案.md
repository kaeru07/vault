---
title: progress 追加 ToDo 案
type: monetization-design
issue: kaeru07/vault#1
created: 2026-05-18
updated: 2026-05-18
status: active
---

# progress 追加 ToDo 案

> Issue #1 の named 出力。**承認済み（approved）になった収益化シナリオのみ** progress へ投入する ToDo の素案。
> **AI は progress へ送らない。投入は人間承認が前提**（[[収益化シナリオ承認フロー]] / [[progress連携基準]]）。

## 投入ルール（厳守）

- 投入できるのは シナリオ `status: approved` かつ `approvedBy` 記入済みのものだけ
- candidate / pending_approval は **投入不可**（本ファイルに案として置くのみ）
- 投入時は1 ToDo=1作業に分解（最初の1作業のみ）。完了条件必須

## ToDo 案一覧（承認後に投入・現状すべて承認待ち）

| 優先 | ToDo 案 | 由来シナリオ | 投入条件 | 状態 |
|---|---|---|---|---|
| 1 | mahjong の公開ブロッカーを1件特定し文書化 | [[scenarios/candidate-001]] | candidate-001 が approved | ⏳ 承認待ち |
| 2 | nanikiru-shorts で送客CTA入りShorts構成1本ドラフト | [[scenarios/candidate-002]] | candidate-002 が approved | ⏳ 承認待ち（candidate） |
| 3 | 麻雀問題系3アプリの主力1本案を提示 | [[既存資産_収益転用候補]] | 人間が統合方針を承認 | ⏳ 承認待ち |
| 4 | progress `/monetization` 画面の実装（A案・読取専用） | [[progress_収益パイプライン設計]] §7 | 実装ToDo化を人間承認 | ⏳ 承認待ち |
| 5 | candidate-003 Obsidianテンプレの一次情報再調査 | [[scenarios/candidate-003]] | 次回定期調査枠 | ⏳ 未スケジュール |

## progress 投入時の最小フィールド（[[progress連携基準]] 準拠）

- `targetApp` / `targetTodoTitle`（由来シナリオID を含める）
- 最初の1作業 / 完了条件（検証可能）
- 由来: `scenarioId` と Vault パス
- **revenueImpact を必ず付す**（収益化インパクト最優先の判断軸）

## 未対応点
- 全 ToDo が承認待ちのため現時点で progress 投入はゼロ（正しい状態）
- ToDo#4 は実装作業のため、approved 後も architect 方針確認が必要（[[../03_prompts/Claude-Code標準運用]]）

## 次の一手
1. 人間が candidate-001 を approved → ToDo#1 を progress 投入（最優先）
2. ToDo#4（progress画面実装）の要否を人間が判断

## 関連
- [[収益化ステップ一覧]] / [[収益化シナリオ承認フロー]] / [[progress連携基準]] / [[progress_収益パイプライン設計]]
- Issue: kaeru07/vault#1
