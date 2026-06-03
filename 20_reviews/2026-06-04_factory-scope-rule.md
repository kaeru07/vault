---
date: 2026-06-04
task: AI工場 作業範囲ルール修正（MVP=autonomous / 公開・課金は別Epic分離）
runId: 20260604-013554
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy, monetization]
relatedRunIds: [20260603-210707, 20260603-144109]
commitHashes: []
---

# AI工場 作業範囲ルール修正（2026-06-04）

## 1. 作業目的
AI工場の作業範囲を明確化し、MVP（ローカル実装・検証）は自走、公開・課金・申請（ユーザー作業）は分離する。MVP Epic が Factory 自動実行対象になるようにする。

## 2. 原因
旧ルールでは収益化候補/おすすめ候補から作る MVP Epic に `external_publish` を付け `approval_required` にしていた。そのためローカル実装だけの MVP まで自動実行対象外（Approval必須）になり、AI工場が自走できなかった。公開・課金と MVP が同一 Epic に混在していた。

## 3. 作業範囲ルール（確定）
- **AI工場がやってよい**: MVP設計 / 画面実装 / ローカル保存 / JSONデータ / UI作成 / build・lint・typecheck / 公開前チェックリスト / ストア文言下書き / スクショ案 / アイコン案 / ASOキーワード案。
- **ユーザー作業（AI工場禁止）**: Google Play / App Store 公開・審査申請、課金実装・サブスク本番・AdMob本番、支払い情報・クレカ登録、本番deploy / 本番DB / 認証情報 / secret・env 変更、外部公開。

## 4. 修正内容（Epic生成ロジック）
- `lib/monetization-store.ts` `buildEpicContract`: MVP Epic を `decisionPolicy: autonomous` / `riskFlags: []` / `factoryEligible: true`。goal から「公開直前まで」を削除しローカル検証までに限定。notes に作業範囲を明記。
- `lib/recommended-epics-store.ts` Source A: 1収益化候補につき 3候補に分離生成。
  - **MVP**（`monetization_candidate`）: autonomous / `[]` → Factory自動対象。
  - **公開申請**（`publish_task`）: `manual` / `external_publish` / targetApp=`<slug>-publish` → ユーザー作業。
  - **課金**（`billing_task`、monetizationあり時）: `approval_required` / `billing` / targetApp=`<slug>-billing` → ユーザー作業。
- `external_publish` は公開Epicだけ、`billing` は課金Epicだけに付与。
- **安全ゲート不変**: `FACTORY_BLOCKING_RISK_FLAGS`（billing/production_db/auth_secret/deploy/migration/destructive）は無変更。MVP が autonomous になるのは公開/課金/deploy/secret を含まない正しい分類であり、ゲート緩和ではない。

## 5. AnglerLog MVP の判定（検証）
- AnglerLog MVP 候補を承認 → `epic-anglerlog-mvp-local-firs` を作成 → `decisionPolicy=autonomous` / `riskFlags=[]` / `factoryEligible=true`。承認Runの eligibility warnings=`[]`（factoryEligible=true）＝**Factory自動実行対象**。
- 検証後にテスト用 epic は削除し epics.json は正規3件に復元。

## 6. 公開・課金系の扱い（検証）
- 再生成20件中: MVP6件すべて autonomous/[]/eligible、publish6件 manual/external_publish/非eligible、billing6件 approval_required/billing/非eligible。公開・課金候補は Factory自動対象外（ユーザー作業）として分離。

## 7. 検証結果
- typecheck OK / lint OK / build OK / pm2 deploy。
- AnglerLog MVP=自動実行対象、Google Play公開・課金がMVPに含まれず別候補に分離、を確認。テスト生成物除去。

## 8. ChatGPTレビュー依頼文
```
対象: progress / AI工場 作業範囲ルール修正 / runId 20260604-013554
収益化候補/おすすめ候補から作るMVP Epicを autonomous/riskFlags:[]/factoryEligible:true にし(ローカル実装・検証のみ)、公開(external_publish/manual)・課金(billing/approval_required)を別Epic候補に分離しました。安全ゲート(FACTORY_BLOCKING_RISK_FLAGS)は不変です。
確認したい観点:
1. MVPをautonomous化することが安全ゲート緩和になっていないか（公開/課金/deploy/secretを含まない分類になっているか）。
2. 公開・課金を別Epicに分離する粒度（slug-publish/slug-billing）の妥当性。
3. external_publishは公開Epicだけ・billingは課金Epicだけ、という付与ルールに漏れがないか。
4. AnglerLog MVPが自動実行対象になる判定は妥当か。
```
