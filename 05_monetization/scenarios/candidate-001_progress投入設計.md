---
type: monetization-progress-handoff
title: candidate-001 承認後 progress 投入設計
issue: kaeru07/vault#23
scenario: candidate-001
date: 2026-05-19
status: 設計のみ（approved 前は投入しない）
---

# candidate-001 承認後 progress 投入設計

> Issue #23。**方向性承認後に止まらず実行へ移れる**よう、progress 投入の形式と最初の3ToDoを設計。
> **実装・実投入はしない**（candidate-001 は `pending_approval`。投入は approved 後＋人間が実施）。
> 起点: [[candidate-001_公開ブロッカー]]（B1〜B5）/ [[candidate-001_7日実行プラン]] / [[../progress連携基準]]

> [!note] 用語注（Issue #69）
> - progress 投入 = progress（作業履歴アプリ・localhost:3010）への ToDo 追加
> - ExecutionRun = progress 内の「作業 1 件分の実行記録」（runId 付き）
> - approved / 承認済み = 実装着手 OK の状態（ChatGPT + あなたの判断）
> - pending_approval / 承認待ち = まだ承認判断が出ていない状態
> - scenarioId / vaultRef = progress と Vault を相互に結ぶための識別子（どの有力候補・どのページ由来か）
> - /vloop = Claude が ToDo を Epic 単位でまとめて進める実行コマンド
> - /focus = Claude を「1 作業に集中して進めるモード」で動かす実行コマンド
> - B1〜B5 = 公開ブロッカー（公開を阻む問題）の種別番号
> - revenueImpact = 収益インパクト（high / medium / low / none の 4 段階）

## 投入の前提（ゲート）

- candidate-001 の `status: approved` かつ `approvedBy` 記入済（[[../ChatGPT承認ゲート標準]]）
- [[decision-log]] に承認判断が1行記録済
- 上記未達なら**投入しない**（[[progress追加ToDo案]] の投入ルール準拠）

## progress 投入形式（ExecutionRun/ToDo 準拠・実 POST はしない）

各 ToDo は progress の作業単位として以下フィールドで投入（[[../progress連携基準]]）:

```
{
  "targetApp": "mahjong",
  "targetTodoTitle": "[candidate-001] <作業名>",
  "scenarioId": "candidate-001",
  "vaultRef": "05_monetization/scenarios/candidate-001.md",
  "revenueImpact": "high",
  "firstAction": "<1アクション>",
  "doneCondition": "<検証可能な完了条件>",
  "runStatus": "running→completed/partial/failed（実行時）"
}
```

- 1 ToDo = 1 作業（複数をまとめない）。`revenueImpact: high` を必ず付す
- 由来 `scenarioId` / `vaultRef` を必ず含める（progress↔Vault 相互追跡）

## 最初の3ToDo（承認後すぐ投入・Claude 集中作業向け分割）

| 順 | ToDo | firstAction | done条件 | 依存 | 集中作業分割（1セッション粒度） |
|---|---|---|---|---|---|
| 1 | B1 build 検証 | apps/mahjong で `npm run build` 実行 | build 成功、または失敗ログ・原因が記録 | 承認 | 単独1セッションで完結（実行＋結果記録） |
| 2 | B2 デプロイ状態確認 | Vercel 本番URL有無を確認（vercel.json あり） | 本番URL確定 or 未デプロイ＋手順案 | ToDo1 build OK | 単独1セッション（確認＋手順整理） |
| 3 | B3 ストア/紹介文ドラフト | README→アプリ説明・ASO 文面ドラフト | 文面ドラフト完成（公開はしない） | ToDo2 | 単独1セッション（ドラフトのみ） |

> 4件目以降（B4 素材 / B5 動画）は3件完了後に [[candidate-001_7日実行プラン]] Day4以降から投入。

## done 条件の定義（共通）

- 「コードが書けた」ではなく「**検証が通った/事実が記録された**」で done（CLAUDE.local.md 原則3）
- build 系は成否ログ必須。未検証は done にしない・「未確認」と書く
- 公開・課金・広告を伴う作業は done 条件に含めない（人間操作・AI 停止条件）

## Claude 集中作業向け分割の方針

- 各 ToDo は **1 セッション（/focus か承認後 /vloop）で完結**する粒度に割る
- ブロッカー連鎖（B1→B2→B3）は依存順を厳守し、前段 done まで次段に進まない
- build 失敗時は ToDo2 以降を保留し「B1 原因対応」を差し込む（7日プランと同方針）

## 未対応点 / 仮説
- progress の ToDo 実データ形式は実投入時に現物確認が必要（ExecutionRun 型は既知だが ToDo 構造は実装時確認）。仮説で確定しない
- candidate-001 未承認のため本設計は待機。approved 後に**人間が**最初の3ToDoを投入（AI は送らない＝[[progress連携基準]]）
- 「集中作業1セッション粒度」は標準軽作業前提の仮置き（B1 失敗時はリスケ）

## 次の一手
1. ChatGPT/人間が candidate-001 を承認（[[candidate-001_ChatGPT承認パック]]）
2. 承認後、人間が ToDo1（B1 build検証）を progress 投入 → Claude が /focus か /vloop で実行
3. ToDo1 done → ToDo2 → ToDo3 と依存順に投入

## 関連
- [[candidate-001]] / [[candidate-001_公開ブロッカー]] / [[candidate-001_7日実行プラン]] / [[candidate-001_ChatGPT承認パック]]
- [[progress追加ToDo案]] / [[../progress連携基準]] / [[../ChatGPT承認ゲート標準]]
- Issue: kaeru07/vault#23（前提 #14/#15/#16/#22）
