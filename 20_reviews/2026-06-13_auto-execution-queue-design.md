---
date: 2026-06-13
task: Progress 自動実行キュー管理（Auto Execution Queue）の全体設計・データ設計・優先順位ロジック・Codex仕様書（設計のみ・実装なし）
runId: 20260613-125025
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy, prompt-template]
relatedRunIds: []
commitHashes: []
---

# 2026-06-13 Progress 自動実行キュー管理 設計

> 設計フェーズ。実装はしていない。成果物 = `apps/ny01/progress/docs/auto-execution-queue-design.md`。

## 1. 作業目的

- なぜ: Progress 司令塔トップの「次にやること」が、ユーザー意図と違う未整理/低関連案件（例: 野鳥観察系）を先頭に出していた。AI工場が勝手に変な優先順位で進む状態を止め、毎日トップを見るだけで判断できる状態にしたい。
- 背景: 司令塔トップが旧 `work-queue.json`（project-tasks 由来）を表示元にしており、新しい Epic Contract（P0/P1/P2・factoryEligible・riskFlags）を無視。二重正本が原因で並びが崩れていた。
- 期待効果: 次回予定／なぜ次か／判断待ち件数／Goal進捗をトップで把握。低優先レビュー・AI保留で工場全体が止まらない。

## 2. 実施内容

- 既存実装を調査（types/goal.ts, lib/types/operations.ts の Epic Contract, types/execution-run.ts, lib/queue-split.ts, work-queue.json, queue API, regenerateAutoOrder, factory-orchestration-design.md）。
- 根本原因を「work-queue.json と Epic Contract の二重正本」と特定。
- 全体設計を策定: Epic=実行正本 / Goal=優先度の親（配下へ boost 伝播）/ Project=表示軸 / ExecutionRun=履歴正本。自動実行キューは新正本を作らず `buildAutoQueue()` で都度生成する派生ビュー。ユーザー手動操作のみ `Epic.queueControl` / `Goal.priorityBoost` に書き戻す。
- 15 成果物を設計書に出力（現状課題/全体設計/画面構成/トップ改善/キュー画面/Goal-Project-Todo-Epic関係/データ構造/優先順位計算式/操作フロー/Factory接続/MVP/後回し/危険設計/Codex仕様書/Codexプロンプト）。

### 主要な設計判断
- 正本: Epic（実行正本）。Goal は分類かつ優先度の親。キューは派生ビュー（新正本を作らない原則を遵守）。
- Goal優先度 vs item優先度の矛盾: 上書きではなく加点(floor boost)。明示pin > Goal pin > priority+GoalBoost > item priority > manualOrder。
- gating: 全体停止型を避け item単位。review_waiting は P2・危険なしなら工場を止めない。
- Inbox: 未整理はキューに入れない（triage後Epic化）。野鳥問題の経路を断つ。
- waiting_user（人間が動かないと永久停止）と ai_hold（AIが自分で再開可）を明確分離。

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/ny01/progress/docs/auto-execution-queue-design.md | 新規: 自動実行キュー管理の設計書（設計のみ・実装なし） |

## 4. 検証結果

- typecheck: n/a（設計のみ・コード変更なし）
- build: n/a
- lint: n/a
- 手動確認: 既存型/ロジックと整合する設計か実コードを読んで確認（Epic Contract / FactoryEligibility / queue-split / regenerateAutoOrder）
- 機密パターン事前チェック: OK（実値なし・設計文書のみ）
- ob sync: 実行（最終応答参照）
- git push: 設計書を progress repo へ commit / vault は mirror→push（最終応答参照）

## 5. 未対応

- 実装は未着手（今回は設計のみの依頼）。
- review_waiting の importance 自動判定の精緻化は MVP 後。
- Project単位 boost は後回し（Goal boost で代替）。

## 6. 危険ポイント

- 司令塔トップの表示元差し替え（work-queue.json → buildAutoQueue）は表示挙動が大きく変わるため、移行時に旧キューを完全に消さず後方互換読み取りで残す。
- 既存スキーマ（epics.json / goals.json）は追加フィールドのみ。破壊変更禁止。
- 承認/本番反映/課金/認証/破壊的操作の自動化は設計上も禁止（承認必須6点を維持）。

## 7. 次にやるべきこと

- Codex に §14 仕様書 + §15 プロンプトで MVP 実装を依頼。
- MVP = buildAutoQueue 実装 / トップ表示元差し替え / WorkItemStatus 導出 / queue 画面4操作 / reason 生成 / 判断待ちフィルタ / Inbox 除外。
- 実装後 Fable で受け入れテスト §14.5 の 1〜9 を検証。

## 8. ChatGPT レビュー依頼文

```
対象: Progress アプリ（apps/ny01/progress）の「自動実行キュー管理」設計書レビュー
ファイル: apps/ny01/progress/docs/auto-execution-queue-design.md
runId: 20260613-125025

背景: 司令塔トップの次回候補が未整理/低関連案件（野鳥観察系）を先頭に出す問題があり、
原因は work-queue.json と Epic Contract の二重正本でした。
Epic=実行正本 / Goal=優先度の親 / キュー=派生ビュー という方針で全体設計しています。

レビューしてほしい観点:
1. 「正本は Epic、キューは派生ビュー」という判断は妥当か。work-queue.json を実行正本から降格する移行リスクは許容範囲か。
2. Goal優先度とitem優先度の矛盾を「加点(floor boost)方式」で解く設計に穴はないか（ユーザーpinが常に最強で良いか）。
3. item単位gating（低優先レビュー/AI保留で全体を止めない）の設計で、逆に「永久に着手されないitem」が生まれないか。
4. waiting_user と ai_hold の分離は運用上ワークするか（ai_holdの自動再開条件が曖昧では）。
5. MVP範囲(§11)の切り方は妥当か。Codex仕様書(§14)・プロンプト(§15)は迷わず実装できる粒度か。
6. スマホ前提のトップ最小表示（判断待ち件数中心）で毎日の判断が回るか。
```
