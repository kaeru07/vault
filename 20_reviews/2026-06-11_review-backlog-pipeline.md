---
date: 2026-06-11
task: AI工場OS v2 Review滞留解消 — AI一次レビュー/一括処理/Factoryバックプレッシャー/Metrics/Decision Log/Human・AI Queue分離
runId: 20260611-022341
targetApp: progress
monetizationImpact: medium
theme: [workflow]
relatedRunIds: [20260611-013300]
commitHashes: [562623f]
---

# 2026-06-11 Review滞留解消パイプライン実装

> 運用レビュー（[[2026-06-11_ai-factory-os-v2-ops-review]]）で特定した最大ボトルネック「Review工程の詰まり（65件滞留・Knowledge 1件）」への対処実装。

---

## 1. 作業目的

- なぜ: Executionまで自走するがReviewで在庫が積み上がり、Knowledge化とNext Epic生成が回っていなかった（closed_loop_rate 1.5%）
- 背景: v3新機能ではなく「Review詰まり解消」と「Factoryを安全に動かす最低限の計測・制御」に限定するというユーザー指示
- 期待効果: レビュー消化の自動化（人間はneeds_humanのみ）、Factoryの暴走防止、運用状態の可視化

## 2. 実施内容

1. **AI一次レビュー（ルールベース・LLM不使用）**: not_reviewed Run を 8ルール（run_failed / run_partial / stale_running / approval_required / errors_recorded / check_failed / risk_keyword / clean_completed）で reviewed / needs_human / partial / failed に4分類。判定理由を reviewMemo + run.aiReview に保存
2. **振り分け**: reviewed→既存Knowledge生成ループ / needs_human→Approval（意思決定キュー）/ partial・failed→needs_followup（修復・再試行候補）
3. **一括処理**: POST /api/operations/ai-review（直近10件）+ ホームの AiReviewPanel（未レビュー件数・最古日齢・処理結果表示）
4. **Factoryバックプレッシャー**: auto実行前に not_reviewed を計測。>20 で起動スキップ、>10 で maxRuns=1 に減速。factoryEnabled は変更しない。automation-log に factory_backpressure を記録
5. **Metrics**: closed_loop_rate / not_reviewed_count / oldest_not_reviewed_age / suggested_epic_count / stale_suggested_count / daily_decision_count / factory_last_result / factory_last_error を API + ホームに表示
6. **Decision Log**: approve / reject / assignGoal / changePriority / markReviewed / pause / drop / factory_pause・resume / goal_adjust / 候補承認・却下 を operational-decisions.ndjson に記録（action / runId / source 付き）
7. **Human/AI Queue分離**: Human（公開・課金・認証・本番DB・Goal判断・上位候補承認・needs_human Run）/ AI（run一次レビュー・candidate scoring・orphan修復提案・failed/partial原因分析・next epic draft）をホームに並列表示
8. needs_human の Approval を人間が決定（mark_reviewed / needs_followup）すると Run の reviewStatus に反映しループを閉じる

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/ai-review.ts | 新規: 一次レビューエンジン |
| progress/lib/factory-metrics.ts | 新規: Metrics計算+backpressure判定 |
| progress/lib/queue-split.ts | 新規: Human/AI Queue分離 |
| progress/lib/factory-runner.ts | バックプレッシャー組込 |
| progress/lib/operations-store.ts | recordOperationalDecision+各操作ログ |
| progress/app/api/operations/ai-review/route.ts | 新規: 一括レビューAPI |
| progress/app/api/operations/metrics/route.ts | 新規: metrics API |
| progress/app/api/operations/approvals/route.ts | needs_human決定のRun反映 |
| progress/app/api/execution-runs/[runId]/route.ts | needs_human許可+Decision Log |
| progress/app/api/goals/route.ts ほか recommended-epics 2件 | Decision Log |
| progress/components/dashboard/AiReviewPanel.tsx | 新規: 実行UI |
| progress/app/page.tsx | Metrics/警告/Queue分離セクション |
| progress/types/execution-run.ts, lib/types/operations.ts | 型拡張 |

## 4. 検証結果

- typecheck: OK（tsc --noEmit 0 errors）
- build: OK / lint: OK（No ESLint warnings or errors）
- 手動確認: 実データで2バッチ20件処理 → reviewed 15（Knowledge 15件生成）/ needs_human 1 / partial 4。closed_loop_rate 1.5%→23.5%。needs_human（Vercel本番反映 = risk_keyword「公開」）が Approval 化され Human Queue 先頭に表示。Factory auto 実行が backpressure_paused で正しくスキップ（automation-log 記録）。Decision Log 追記確認。ホーム 200・新セクション描画確認
- 既存データ保持: 68 run 全件保持・削除なし。factoryEnabled=true 不変
- 機密パターン事前チェック: OK / git push: 562623f（kaeru07/ny01 main）

## 5. 未対応

- 残り not_reviewed 48件（10件ずつボタン実行 or API で消化可能）
- candidate scoring 未実装（AI Queue に項目として表示のみ）
- orphan修復提案・next epic draft も同様に表示のみ（実行ロジックは次フェーズ）
- バックプレッシャーは auto モードのみ対象（dry_run / manual は対象外）

## 6. 危険ポイント

- Knowledge生成ループが reviewed run ごとに Next Epic 候補を自動生成するため、一括レビューで suggested が 20→34件に増加。candidate scoring（絞り込み）を入れないと候補滞留が悪化する
- ルールベース判定の clean_completed は「記録上エラーなし」を見ているだけで、実コードの品質は見ていない。誤 reviewed のサンプリング確認を推奨
- 既存データに runId 重複（20260610-140527）があり、重複 run の片方しか更新されないケースがある

## 7. 次にやるべきこと

- 残り48件の一括処理（ホームのボタンで5回 = 人間操作1分）
- needs_human 1件（Vercel本番反映）の Approval 判断
- suggested 34件の整理（上位3件は Human Queue に表示済み。残りは reject か hold）
- ルーチン run（Factory schedule / 収益化候補取り込み）の Knowledge 生成抑制の検討

## 8. ChatGPT レビュー依頼文

```
progress アプリ（AI工場OS v2）の「Review滞留解消」実装の運用レビューをお願いします。
runId: 20260611-022341 / commit: 562623f / 本体: 20_reviews/2026-06-11_review-backlog-pipeline.md

実装概要:
- not_reviewed Run をルールベース8ルールで reviewed / needs_human / partial / failed に4分類するAI一次レビュー
- reviewed→Knowledge自動生成 / needs_human→人間の意思決定キュー / partial・failed→修復候補
- Factoryバックプレッシャー（not_reviewed >10 で減速・>20 で起動スキップ、factoryEnabled は不変）
- Metrics 8項目（closed_loop_rate 等）+ Human Queue / AI Queue 分離表示

実測効果: closed_loop_rate 1.5%→23.5%、Knowledge 1→15件（2バッチ20件処理）

確認したい観点:
1. ルールベース判定の安全性 — clean_completed（エラー記録なし=問題なし）の誤判定リスクと、人間サンプリング確認の適切な頻度
2. バックプレッシャー閾値（10/20）の妥当性
3. reviewed run ごとに Next Epic 候補を自動生成する設計の是非（suggested が20→34件に増加。生成を絞るべきか、scoring で絞るべきか）
4. needs_human の危険キーワード5系統（課金/公開/認証/本番DB/破壊的）に抜けがないか
5. Decision Log の粒度（9操作）は Activity Mining に十分か
```
