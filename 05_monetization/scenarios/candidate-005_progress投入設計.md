---
type: monetization-progress-handoff
title: candidate-005 承認後 progress 投入設計
issue: kaeru07/vault#60
scenario: candidate-005
date: 2026-05-24
status: 設計のみ（approved 前は投入しない）
---

# candidate-005 承認後 progress 投入設計

> Issue #60。**ChatGPT 方向性承認後 → 人間 approved → 止まらず実行へ移れる**よう、progress 投入の形式と最初の 3 ToDo を設計。
> **実装・実投入はしない**（candidate-005 は `candidate`。投入は approved 後 + 人間が実施）。
> 起点: [[candidate-005_公開ブロッカー]]（B1-B9）/ [[candidate-005_7日実行プラン]] / [[../progress連携基準]]

## 投入の前提（ゲート）

- candidate-005 の `status: approved` かつ `approvedBy` 記入済（[[../ChatGPT承認ゲート標準]]）
- [[../decision-log]] に承認判断が 1 行記録済
- 上記未達なら**投入しない**（[[../progress追加ToDo案]] の投入ルール準拠）

## progress 投入形式（ExecutionRun / ToDo 準拠・実 POST はしない）

各 ToDo は progress の作業単位として以下フィールドで投入（[[../progress連携基準]]）:

```json
{
  "targetApp": "token-speed-tool",
  "targetTodoTitle": "[candidate-005] <作業名>",
  "scenarioId": "candidate-005",
  "vaultRef": "05_monetization/scenarios/candidate-005.md",
  "revenueImpact": "medium",
  "firstAction": "<1 アクション>",
  "doneCondition": "<検証可能な完了条件>",
  "runStatus": "running→completed/partial/failed（実行時）"
}
```

- 1 ToDo = 1 作業（複数をまとめない）。`revenueImpact: medium` を必ず付す
- 由来 `scenarioId` / `vaultRef` を必ず含める（progress ↔ Vault 相互追跡）
- `targetApp` は新規 `token-speed-tool`（apps/ 配下 or 90_prototypes 配下 / 公開先決定後に確定）

## 最初の 3 ToDo（承認後すぐ投入・Claude 集中作業向け分割）

| 順 | ToDo | firstAction | done 条件 | 依存 | 集中作業分割（1 セッション粒度）|
|---|---|---|---|---|---|
| 1 | B1 iPhone 実機確認 + B9 規約明文化 | iPhone Safari で `90_prototypes/token-speed-tool/index.html` 開く（または Vercel deploy 後の本番 URL）| サンプル 5 件比較 + フォーム + Export/Import 動作確認 / 「手動取り込み + 出典必須」ルール 1 行記録 | 承認 | 単独 1 セッション（実機確認 + 記録）|
| 2 | B2 Vercel デプロイ | GitHub 連携 + `vercel --prod` | 本番 URL 確定 / iPhone 実機で動く | ToDo1 OK | 単独 1 セッション（デプロイ + URL 共有）|
| 3 | B4-1 自分の実測 5-6 件追加 | Claude Code / Codex の実測ログを sample-data.json に追記 | 10 件以上 / source: own-log の比率増 | ToDo2 | 単独 1 セッション（実測 + 追記）|

> 4 件目以降（B5 ログ解析 / B4-2 4 LLM カバー / B7 動画 / B6 AdSense / B8 note）は 3 件完了後に [[candidate-005_7日実行プラン]] Day4 以降から投入。

## done 条件の定義（共通）

- 「コードが書けた」ではなく「**検証が通った / 事実が記録された**」で done（CLAUDE.local.md 原則 3）
- iPhone 実機系は**スクショ必須**（実機タップで動作確認の証跡）
- デプロイ系は**本番 URL 必須**（iPhone Safari で開ける URL）
- 公開・課金・広告を伴う作業は done 条件に含めない（人間操作・AI 停止条件）

## Claude 集中作業向け分割の方針

- 各 ToDo は **1 セッション（/focus か承認後 /vloop）で完結**する粒度に割る
- iPhone 実機確認はユーザー操作なので Claude セッション外（progress には「ユーザー確認待ち」として記録）
- Vercel デプロイは GitHub 連携設定後は CLI 1 コマンドで完結
- 実測追加は「自分の Claude Code 利用時に取れる」ため AI セッションのついでに記録可能

## candidate-001 との progress 投入並走

- candidate-001 の `targetApp: mahjong` と candidate-005 の `targetApp: token-speed-tool` は別 progress ToDo として並走
- 両者とも `scenarioId` で trace 可能なため、進捗ダッシュボード（[[../progress_収益パイプライン設計]]）で同時表示
- `revenueImpact` は candidate-001 = high / candidate-005 = medium のため表示順は candidate-001 が上

## 投入時のチェックリスト

- [ ] candidate-005.md の `status` が approved
- [ ] [[../decision-log]] に承認 1 行
- [ ] ToDo に scenarioId と vaultRef
- [ ] `revenueImpact: medium`
- [ ] firstAction / doneCondition が 1 行で検証可能
- [ ] iPhone 系はスクショ証跡を残す
- [ ] 公開・課金・広告系の人間操作部分は done 条件から除外

## 未対応点 / 仮説

- progress アプリ（port 3010）への実 POST は本サイクルではしない（候補-005 が未承認のため）
- targetApp の正式名称（"token-speed-tool" or "ts-tool" or "llm-speed-bench"）は公開時に決定
- B4 実測データは AI セッションごとに自動記録するスクリプトを将来作る可能性あり（次サイクル検討）

## 次の一手

1. ChatGPT が candidate-005 方向性承認（[[../../20_reviews/candidate-005_ChatGPT承認パック]] 経由）
2. 人間が approved 判断 → [[../decision-log]] 追記
3. 最初の 3 ToDo を progress に投入（人間が実施）
4. Day1 から [[candidate-005_7日実行プラン]] で実行

## 関連

- [[candidate-005]]
- [[candidate-005_公開ブロッカー]]
- [[candidate-005_7日実行プラン]]
- [[../../20_reviews/candidate-005_ChatGPT承認パック]]
- [[candidate-001_progress投入設計]]（並走候補・形式比較）
- [[../progress連携基準]]
- [[../progress追加ToDo案]]
- Issue: kaeru07/vault#60（前提 #61 / #62 / #63）
