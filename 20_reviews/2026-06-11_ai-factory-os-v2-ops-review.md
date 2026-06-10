---
date: 2026-06-11
task: AI工場OS v2 運用レビュー（ボトルネック/自走率95%/収益化/v3設計の10項目）+ progress API全404の復旧
runId: 20260611-013300
targetApp: progress
monetizationImpact: high
theme: [workflow, app-strategy, monetization, obsidian]
relatedRunIds: [20260611-001032]
commitHashes: []
---

# 2026-06-11 AI工場OS v2 運用レビュー（10項目）

> 前回戦略レビュー（[[2026-06-11_ai-factory-os-v2-strategy]] / runId 20260611-001032）の指摘事項（Goal層・Epic.goalId・意思決定キュー・Knowledge→Next Epic 等）が実装された後の、運用フェーズレビュー。

---

## 1. 作業目的

- なぜ: v2 実装完了後の「ループは構造的に完成したが、実際に回っているか」を実データで検証し、自走率95%・収益化への残課題を特定するため
- 背景: Goal層/North Star/Epic.goalId/orphan検出/意思決定キュー/Resume Packet/Review Queue/Knowledge/Next Epic生成 まで実装済み。人間は毎日5〜15分の承認・却下・Goal調整・優先順位変更のみの想定
- 期待効果: 次の30〜90日で投資すべき箇所（レビュー自動化・収益貫通・計測レイヤ）の確定

---

## 2. 実施内容

- progress 実データの棚卸し: goals.json（4 Goal / North Star = goal-ai-factory-os）、epics.json（11件: done 8 / active 3 / orphan 1）、recommended-epics.json（21件: suggested 20 / epic_created 1）、knowledge-records.json（1件）、execution-runs.json（67件: not_reviewed 65）、approvals.json（0件）、operational-decisions.ndjson（0行）、automation-config（factoryEnabled=true / autoResume=false / executorMode=both）、automation-log（factory_schedule が毎日 max_runs_reached=3 まで稼働）
- 10項目の運用レビューを作成（本文は rawReport / 最終応答参照。要旨は §7）
- **障害発見と復旧**: 調査中に progress の API が全404（GET/POST とも）でFactoryループが停止状態であることを発見。原因は 2026-06-11 01:17 の不完全ビルド（routes-manifest に静的APIルート欠落・dev/prod成果物混在）。`npm run build` 再実行 + `pm2 restart progress` で復旧し、`GET /api/execution-runs → 200` を確認

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/ny01/progress/.next | 再ビルド（API全404の復旧。ソースコード変更なし） |
| obsidian-sync-vault/20_reviews/2026-06-11_ai-factory-os-v2-ops-review.md | 新規作成（本ファイル） |
| obsidian-sync-vault/20_reviews/_review_queue.md | 未レビュー先頭に1件追加 |

---

## 4. 検証結果

- typecheck: n/a（ソース変更なし）
- build: OK（npm run build 成功・42ルート生成）
- lint: n/a
- 手動確認: GET /api/execution-runs → 200、POST → {"success":true,"runId":"20260611-013300"}
- 機密パターン事前チェック: OK（実値なし）
- ob sync: 実施（最終応答参照）
- git push: 実施（最終応答参照）

---

## 5. 未対応

- automation-log の欠落確認（01:17〜01:30 の API 停止中に factory_schedule / 他セッションの POST が失敗していた可能性。ログと runId 連番の欠落チェックは未実施）
- 不完全ビルドの根本原因（何が 01:17 のビルドを中断させたか）は未特定。ビルド成功検証をデプロイ手順に組み込む対策は提案のみ
- レビュー本文で提案した施策（AI一次レビュー / WIP制限 / 人間タスクレーン分離 / metric自動計測）は提案のみで未実装

---

## 6. 危険ポイント

- pm2 restart progress を実施（可逆・サービス復旧目的）。データ破壊なし
- 本番データ（data/real）への書き込みは ExecutionRun POST 1件のみ（正規API経由）
- 今後 Factory の MAX_RUNS を上げる場合、レビュー滞留（現在65件）が加速するリスクあり → WIP制限の先行導入を推奨

---

## 7. 次にやるべきこと（レビュー要旨）

1. **最大ボトルネック = Review工程**: 67 run 中 65 件 not_reviewed、Knowledge 1件。Review→Knowledge→Next Epic の目玉ループは実質1回しか発火していない。AI一次レビュー（reviewer agent が一次判定し、人間は needs_human のみ見る）の導入が最優先
2. **WIP制限**: 未レビュー滞留が閾値を超えたら Factory を自動減速（生産速度 ≤ レビュー消化速度）
3. **収益化の最後の1マイル**: ストア申請・AdMob 等の「ユーザー作業」候補が suggested に滞留 = 収益ゼロの直接原因。人間タスクレーンを AI キューから分離し、BirdLog 1本を「MVP→公開→収益1円」まで貫通させる
4. **Goal metric の実測**: daily_decision_minutes / closed_loop_rate は定義のみで計測値なし。自動算出してホームに表示
5. **意思決定ログ 0行**: 人間の毎日5〜15分が記録に残っていない。承認UI経由の判断を必ず ndjson に落とす
6. North Star の再考: 工場改修 Epic が過半 = 手段の目的化。North Star を価値検証/収益側へ移す検討

---

## 8. ChatGPT レビュー依頼文

```
AI工場OS v2（個人開発の自走型開発管理システム）の運用レビューをお願いします。

対象: progress アプリ（runId 20260611-013300 / 前回戦略レビュー runId 20260611-001032 の実装後フォロー）
本体: 20_reviews/2026-06-11_ai-factory-os-v2-ops-review.md

システム概要:
Goal → Epic → Execution → Review → Knowledge → Next Epic候補 のループを持つ。
人間は毎日5〜15分（承認/却下/Goal調整/優先順位変更）のみ介在する想定。
factoryEnabled=true で毎日 MAX_RUNS=3 の自動実行が稼働中。

実データ所見:
- ExecutionRun 67件中 65件が not_reviewed、Knowledge記録は1件のみ
- Next Epic候補 21件中 20件が suggested のまま滞留（テンプレ生成中心）
- 意思決定ログ 0行（人間判断が記録されていない）
- Goal 4件の metric は定義のみで計測値なし
- 収益データの流入経路なし（ストア申請・課金設定が人間作業として滞留）

Claude側の提案:
(1) AI一次レビュー導入（人間は needs_human のみ）
(2) 未レビュー滞留数による Factory の WIP制限（バックプレッシャー）
(3) 人間タスクレーンの分離 + BirdLog 1本の収益貫通テスト最優先化
(4) Goal metric の自動計測とホーム表示
(5) North Star を「工場自走化」から「価値検証/収益」へ移す再考
(6) v3 は機能追加でなく計測レイヤ（metric自動化/アプリ別P/L/reviewer agent正式化）

確認したい観点:
- AI一次レビューを信頼してよい条件設計（人間サンプリング率・誤判定検知）は何が妥当か
- WIP制限の閾値設計（未レビュー何件で減速・停止か）
- 「収益貫通テスト1本優先」と「候補の幅出し継続」のバランスは妥当か
- North Star を収益側へ移すことの副作用（工場品質の劣化リスク）
- 90日後の理想状態として「2アプリ公開・closed_loop_rate 80%・人間10分/日」は現実的か
```
