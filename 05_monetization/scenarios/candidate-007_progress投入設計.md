---
type: monetization-progress-handoff
title: candidate-007 承認後 progress 投入設計
issue: kaeru07/vault#61
scenario: candidate-007
date: 2026-05-25
status: 設計のみ（approved 前は投入しない）
---

# candidate-007 承認後 progress 投入設計

> Issue #61 / #80。**ChatGPT 方向性承認後 → 人間 approved + 統合方針決定 → 止まらず実行へ移れる**よう、progress 投入の形式と最初の 3 ToDo を設計。
> **実装・実投入はしない**（candidate-007 は `candidate`。投入は approved 後 + 人間が実施）。
> 起点: [[candidate-007_公開ブロッカー]]（B1-B9）/ [[candidate-007_7日実行プラン]] / [[../progress連携基準]]

> [!note] 用語注（Issue #69）
> - progress 投入 = progress（作業履歴アプリ・localhost:3010）への ToDo 追加
> - ExecutionRun = progress 内の「作業 1 件分の実行記録」（runId 付き）
> - approved / 承認済み = ChatGPT + あなたの判断で「実装着手 OK」になった状態
> - candidate / 有力候補 = 収益化する価値があるかもしれない案
> - scenarioId / vaultRef = progress と Vault を相互に結ぶための識別子
> - revenueImpact = 収益インパクト（high / medium / low / none の 4 段階）
> - /vloop = Claude が ToDo を Epic 単位でまとめて進める実行コマンド
> - /focus = Claude を「1 作業に集中して進めるモード」で動かす実行コマンド
> - B1〜B9 = 公開ブロッカー（公開を阻む問題）の種別番号
> - Vault Search Cheatsheet = iPhone Obsidian + GitHub Vault の検索パターン早見表 HTML
> - 統合方針 A/B/C = 既存「Vault の見方ガイド」と本候補をどう統合するかの 3 案

## 投入の前提（ゲート）

- candidate-007 の `status: approved` かつ `approvedBy` 記入済（[[../ChatGPT承認ゲート標準]]）
- **統合方針 A/B/C が決定済**（ChatGPT 方向性レビュー時に決定）
- [[../decision-log]] に承認判断 + 統合方針決定が 1 行ずつ記録済
- 上記未達なら**投入しない**（[[../progress追加ToDo案]] の投入ルール準拠）

## progress 投入形式（ExecutionRun / ToDo 準拠・実 POST はしない）

各 ToDo は progress の作業単位として以下フィールドで投入（[[../progress連携基準]]）:

```json
{
  "targetApp": "vault-search-cheatsheet",
  "targetTodoTitle": "[candidate-007] <作業名>",
  "scenarioId": "candidate-007",
  "vaultRef": "05_monetization/scenarios/candidate-007.md",
  "revenueImpact": "low",
  "firstAction": "<1アクション>",
  "doneCondition": "<検証可能な完了条件>",
  "runStatus": "running→completed/partial/failed（実行時）"
}
```

- 1 ToDo = 1 作業（複数をまとめない）
- 由来 `scenarioId` / `vaultRef` を必ず含める（progress↔Vault 相互追跡）
- candidate-007 は **revenueImpact: low**（candidate-005 medium より優先度低）

## 最初の 3 ToDo（承認後すぐ投入・Claude 集中作業向け分割）

| 順 | ToDo | firstAction | done 条件 | 依存 | 集中作業分割（1 セッション粒度） |
|---|---|---|---|---|---|
| 1 | B1 + B9 iPhone 実機確認 & 規約明文化 | iPhone で `index.html` を開き、公開可能範囲 1 行記録 | iPhone で検索ボックス動作確認済 + 規約 1 行完成 | 承認 + 統合方針決定 | 単独 1 セッション |
| 2 | B2 既存ガイド重複整理（統合方針 A/B/C 実施） | 決定した方針に沿って既存ガイド + 本候補の整理を実施 | 重複箇所の整理完了 / 統合方針通りの状態に到達 | ToDo1 | 単独 1 セッション（方針による複雑度差あり）|
| 3 | B3 Vercel デプロイ | Vercel CLI で `vercel --prod` を実行（GitHub 連携 or CLI） | 本番 URL 確定 / iPhone でも本番 URL で動く | ToDo2 | 単独 1 セッション |

> 4 件目以降（B5 UX 拡充 / B4 蓄積運用 / B7 テンプレ / B8 note / B6 AdSense）は 3 件完了後に [[candidate-007_7日実行プラン]] Day4 以降から投入。

## done 条件の定義（共通）

- 「コードが書けた」ではなく「**検証が通った/事実が記録された**」で done（CLAUDE.local.md 原則 3）
- iPhone 実機確認は**スクショ証跡**を done 条件にする
- 統合方針実施は**重複箇所が物理的に消えている**ことを done 条件にする（残存重複ゼロ）
- 公開・課金・広告を伴う作業は done 条件に含めない（人間操作・AI 停止条件）

## Claude 集中作業向け分割の方針

- 各 ToDo は **1 セッション（/focus か承認後 /vloop）で完結**する粒度に割る
- ブロッカー連鎖（B1→B2→B3）は依存順を厳守
- B2 の重複整理が大きい場合は分割（仮: B2-a 重複洗い出し / B2-b 統合方針実施 / B2-c リンク張り替え）

## 未対応点 / 仮説

- progress の ToDo 実データ形式は実投入時に現物確認が必要（candidate-005/006 投入設計と同方針）
- candidate-007 未承認のため本設計は待機。approved + 統合方針決定後に**人間が**最初の 3 ToDo を投入
- 「集中作業 1 セッション粒度」は標準軽作業前提の仮置き（B2 統合方針の重さ次第でリスケ）
- 統合方針 B（既存ガイド索引化）が選ばれた場合、既存ガイド利用者の習慣変更を伴うため**周知が必要**（人間判断）

## 次の一手

1. ChatGPT / 人間が candidate-007 を承認 + 統合方針決定（[[../../20_reviews/candidate-007_ChatGPT承認パック]]）
2. 承認後、人間が ToDo1（B1 + B9）を progress 投入 → Claude が /focus か /vloop で実行
3. ToDo1 done → ToDo2 → ToDo3 と依存順に投入

## 関連

- [[candidate-007]] / [[candidate-007_公開ブロッカー]] / [[candidate-007_7日実行プラン]] / [[../../20_reviews/candidate-007_ChatGPT承認パック]]
- [[../progress追加ToDo案]] / [[../progress連携基準]] / [[../ChatGPT承認ゲート標準]]
- [[../../00_inbox/Vaultの見方_どこを見れば何がわかるか]]（既存資産・重複整理対象）
- [[candidate-005_progress投入設計]] / [[candidate-006_progress投入設計]]（形式参考）
- Issue: kaeru07/vault#61（前提 #55 / #58 / #63 / #80）
