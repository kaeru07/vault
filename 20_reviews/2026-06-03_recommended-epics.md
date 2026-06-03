---
date: 2026-06-03
task: Progress に「おすすめ追加Epic」機能を追加（/recommended-epics・/recommended-epics/[id]）
runId: 20260603-210707
targetApp: progress
monetizationImpact: high
theme: [app-strategy, workflow, monetization]
relatedRunIds: [20260603-144109]
commitHashes: []
---

# おすすめ追加Epic 実装（2026-06-03）

## 1. 作業目的
AI工場が毎日（11:00/23:00/起動時）Vault・Progress・調査・既存Epic・実行履歴を見て「追加すべき Epic 候補」を抽出し、ユーザー承認した候補だけを正式 Epic として epics.json に追加する。自動Epic追加は禁止（承認は人間のみ）。Monetization Hub（[[2026-06-03_monetization-hub]]）の上位レイヤとして、候補→Epic化の承認ゲートを担う。

## 2. 実施内容
- 型 `types/recommended-epic.ts`、ストア＋抽出 `lib/recommended-epics-store.ts`、UI色分け `lib/recommended-epics-ui.ts`。
- API: `/api/recommended-epics`（GET一覧/POST抽出生成）、`/api/recommended-epics/[id]`（GET/PATCH状態遷移）、`/api/recommended-epics/[id]/approve`（承認→Epic追加）。
- ページ: `/recommended-epics`（一覧・状態フィルタ・カード）、`/recommended-epics/[id]`（全項目詳細）。
- コンポーネント: RecommendationList / RecActions（承認/保留/却下/再調査）/ GenerateButton（抽出実行）。
- ナビ（Top/Bottom）に導線追加。

## 3. 抽出ロジック
- 収益化候補（未Epic化）→ 新規Epic候補（収益化インパクト最優先、riskFlags=external_publish）。
- active かつ未完の既存Epic → 既存EpicへのNext Action候補（新規Epicにせず remainingWork 追記対象）。
- 直近の失敗Run → 既存Epic継続（失敗解消）候補。
- 重複生成は dedupeKey(`kind:sourceRef`) で防止。新規Epicは slug で 実装済みアプリ/既存Epic と重複チェック。

## 4. 承認フロー / Epic追加 / 安全ゲート
- 承認は人間が「✅承認してEpic追加」を押した時のみ。
- new_epic: 重複チェック → Epic Contract生成 → createEpic（追記）→ status epic_created → ExecutionRun(source=recommended_epics)。
- existing_epic_next_action: updateEpic で remainingWork 追記（新規Epicを作らない）→ ExecutionRun。
- 二重登録防止（epic_created 済みは 409）。
- **安全ゲート不変**: deploy/billing/migration/auth_secret/production_db/destructive は FACTORY_BLOCKING_RISK_FLAGS により自動対象外のまま。external_publish も Approval 必須。承認しても Factory の安全条件は緩めない（eligibility に反映するだけ）。
- 自動Epic追加・自動Factory投入はしない（抽出は status=suggested のみ）。

## 5. 検証結果
- typecheck OK / lint OK / build OK / pm2 deploy / GET /recommended-epics 200・詳細 200。
- API実機: 抽出生成8件 / 再生成dedupe(skip8) / 詳細 / 承認(新規Epic化 epic-birdlog-mvp...) / 二重登録=409 / 既存EpicへNext Action追記(epic-91) / hold / reject / Factory対象判定(external_publish→Approval必須・既存Epic継続→自動可) を確認。
- テスト生成物（作成Epic・ExecutionRun）を除去し、recommended-epics.json は再生成で8件 suggested の整合状態に戻した。

## 6. 未対応
- 11:00/23:00/起動時の定例からの自動抽出配線は統合点 `POST /api/recommended-epics` を用意したのみ（factory-schedule への配線は未実施・別Epic）。
- existing_epic_next_action の doneCriteria が既存 remainingWork を echo するため、承認時の追記が0件になる場合がある（冪等・無害だが、新規提案文言にする改善余地）。
- genRunId が秒粒度のため、同秒の連続承認で runId 衝突の可能性（将来ユニーク化）。

## 7. 危険ポイント
- approve は epics.json を追記する。重複チェックを通すが slug 判定は英数ベース。
- automation 経路から approve を呼ばない設計を厳守（自動Epic追加禁止）。

## 8. ChatGPTレビュー依頼文
```
対象: progress / おすすめ追加Epic 実装 / runId 20260603-210707
AI工場が Vault/Progress/調査/既存Epic/実行履歴から追加Epic候補を抽出し、人間承認した候補だけ epics.json に追加する機能を実装（/recommended-epics）。抽出は提案のみ（自動Epic追加なし）、承認は人間のみ。新規Epic化と既存EpicへのNext Action追記の2種別。
確認したい観点:
1. 「抽出は自動・承認/Epic追加は人間のみ」のガードが構造的に守られているか（automationからapproveを呼べないか）。
2. 安全ゲート不変（deploy/billing/migration/auth_secret/production_db/destructive を自動実行しない）が担保されているか。
3. 既存Epicに含めるべきものを新規EpicにせずNext Action候補にする判定の妥当性。
4. 重複・二重登録防止の十分性。
5. iPhone操作性（カード/状態フィルタ/承認ボタン）。
```
