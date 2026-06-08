---
date: 2026-06-08
task: Factory承認過多を解消（deployを承認不要の注意扱いに・判定を4概念へ整理）
runId: 20260608-143957
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260608-133645]
commitHashes: []
---

# 2026-06-08 Factory承認過多の解消（deploy=注意扱い・判定の4概念分離）

---

## 1. 作業目的

- なぜ: 本来 autonomous / factoryEligible:true の Epic（通常のアプリ改修・調査・UI追加・URL一覧追加・動作確認Todo追加）まで「承認待ち / 要承認 / 対象外」になり、承認が多すぎた。
- 背景: 直前の修正（[[2026-06-08_progress-factory-eligibility-fix]] / runId 20260608-133645）で riskFlags を**1つでも持てば一律「要承認」**にしていた。deploy も要承認扱いだった。
- 期待: 承認は本当に危険な作業だけに絞る。deploy は自動実行対象のまま「注意」表示にする。

---

## 2. 実施内容

- **riskFlags を 2 分割**:
  - `APPROVAL_RISK_FLAGS`（要承認）= `billing / production_db / auth_secret / migration / destructive / external_publish`
  - `CAUTION_RISK_FLAGS`（注意のみ・承認不要）= `deploy`
- **判定を 4 概念に分離**（`evaluateFactoryEligibility`）:
  - `factoryManaged`: 工場で処理できるか（excluded 以外）
  - `approvalRequired`: 人間承認が必要か（危険フラグ / approval_required / pending / blocked）
  - `riskLevel`: 注意度（`none` / `caution`(deploy) / `approval`(危険フラグ)）
  - `displayBadge`: `describeFactory` が主バッジ＋副バッジ＋注意チップ（⚠ デプロイ注意）を返す
- **deploy 単独 → auto（eligible=true）＋ ⚠ デプロイ注意**。承認なしで自動実行対象のまま。
- **危険6フラグ → approvalRequired=true（要承認）**。従来どおり自動実行はブロック。
- autonomous + factoryEligible:true + 危険フラグなし → auto。manual → 対象外。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `lib/types/operations.ts` | `FactoryEligibility` に approvalRequired / riskLevel / cautionFlags 追加。`RiskLevel` 型新設 |
| `lib/epic-contract.ts` | `APPROVAL_RISK_FLAGS`（deploy除外）/ `CAUTION_RISK_FLAGS`（deploy）定義。evaluate を 4 概念分離に再設計。`describeFactory` に cautionLabel 追加。`FACTORY_BLOCKING_RISK_FLAGS` は後方互換で APPROVAL_RISK_FLAGS を指す |
| `app/epic/page.tsx` | 一覧カードに ⚠ デプロイ注意チップ追加（主＋副＋注意の3チップ） |
| `app/epic/[epicId]/page.tsx` | 詳細に注意チップ＋「自動実行対象のまま・承認不要」注意文を追加 |
| `components/epic/EpicCreateForm.tsx` | riskFlags 警告を分岐（危険→要承認 / deploy→承認不要・注意 / manual→対象外）。一律「Approval必須」文言を撤廃 |
| `components/epic/EpicJsonImport.tsx` | インポートプレビューにデプロイ注意を併記 |

---

## 4. 検証結果

- typecheck: OK（tsc 0 error）
- build: OK（next build 成功 41/41）
- lint: OK（変更6ファイル eslint 0 error）
- 手動確認（dev 127.0.0.1:3019, HTTP 200）:
  - `/epic`: 🛡 要承認 **0 件**。対象2件（URL一覧・動作確認Todo）が **⚙ Factory対象 + ✅ 自律実行可 + ⚠ デプロイ注意**。🚫 Factory対象外は構造不完全な1 Epicのみ。
  - `/epic/epic-progress-url`: 主 ⚙ Factory対象・副 ✅ 自律実行可・⚠ デプロイ注意、要承認0 / 対象外0。
- 合成11ケース matrix:
  - billing / production_db / auth_secret / migration / destructive / external_publish → **要承認**（eligible=false）
  - deploy のみ → **auto**（eligible=true）+ ⚠ デプロイ注意
  - deploy+billing → 要承認 + 注意併記
  - 空 autonomous → auto / approval_required → 要承認 / manual → 対象外
- スマホ: チップは `flex flex-wrap gap` で折返し、3チップでも崩れなし。

---

## 5. 未対応 / 補正した既存Epic

- **補正した既存Epic: なし**。対象2件は元から `factoryEligible:true / autonomous` で正しく、誤判定はロジック由来だった。
- Epic「AI工場オペレーションセンター」は `doneCriteria` / `priority` 未設定で**構造的に不完全なため正しく Factory対象外**。誤判定ではないので**データ捏造による補正はしない**（ユーザーが契約を補完すべき）。
- **git commit/push 保留**（前タスクと同様、ny01 作業ツリーに無関係 WIP 多数）。本タスク6ファイル + 前タスク9ファイルをまとめて選択 commit 推奨。
- pm2 本番 progress（3010）は旧ビルドのまま。反映には rebuild + `pm2 restart progress` が必要。

---

## 6. 危険ポイント

- **deploy を自動実行対象に変更**: deploy 付き Epic は factory-dispatch/runner で自動 dispatch 候補になる。これは**ユーザーの明示要望**（「deploy だけの riskFlags は自動実行対象のまま」）。billing / production_db / auth_secret / migration / destructive / external_publish は**従来どおりブロック**（自動承認しない）。
- 実行ゲート `eligible` は `auto` のときだけ true。危険6フラグは eligible=false を維持。
- データ非破壊: epics.json / 進捗率 / 履歴は未変更。

---

## 7. 次にやるべきこと

- 前タスク+本タスクの計15ファイルを選択 commit/push（無関係 WIP・データ JSON 除外）。
- `pm2 restart progress` で本番反映 → iPhone/PC で確認。
- 「AI工場オペレーションセンター」Epic に doneCriteria / priority を補完して Factory対象化。

---

## 8. ChatGPT レビュー依頼文

```
対象アプリ: progress（/root/company/apps/ny01/progress, ポート3010）
runId: 20260608-143957（前段: 20260608-133645）/ commit: 未push（無関係WIP混在で保留）
変更: Factoryの承認過多を解消。riskFlagsを「要承認」と「注意のみ」に2分割。
  APPROVAL_RISK_FLAGS = billing/production_db/auth_secret/migration/destructive/external_publish（要承認）
  CAUTION_RISK_FLAGS  = deploy（承認不要・自動実行対象のまま・⚠デプロイ注意表示）
  判定を factoryManaged / approvalRequired / riskLevel(none|caution|approval) / displayBadge の4概念に分離。
  autonomous+factoryEligible:true+危険フラグなし → auto。manual → 対象外。

確認してほしい観点:
1. 安全性: deployを自動実行対象に格上げした影響。billing等6フラグが承認なしで
   自動実行される経路が本当に無いか(eligible=falseがfactory-dispatch/runner/auto-resumeで効くか)。
2. フラグ分類の妥当性: deployだけを注意扱いにし、他6つを承認必須にする線引きは妥当か。
   external_publishを承認側に入れた判断は正しいか。
3. riskLevel(none/caution/approval)の3段は運用上十分か。deploy+危険フラグ併存時は
   approval優先＋注意併記としているが、これで誤解は生じないか。
4. 未知のriskFlagが来たときに承認側に倒す(isApprovalFlag)フェイルセーフは妥当か。
5. commit運用: 無関係WIP混在のためcommit保留した判断は妥当か。15ファイル選択commitすべきか。
```
