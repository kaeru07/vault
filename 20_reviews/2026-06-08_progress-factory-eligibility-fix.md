---
date: 2026-06-08
task: progress EpicカードのFactory対象外誤判定を修正（riskFlags=deployで対象外化される設計バグ）
runId: 20260608-133645
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: []
# reviewFileCommit:   # 任意。ny01側はWIP混在のためcommit保留（本文参照）
---

# 2026-06-08 progress EpicカードのFactory対象外誤判定を修正

---

## 1. 作業目的

- なぜ: 「全アプリURL一覧を追加する」「動作確認Todoページを追加する」Epic が、`factoryEligible: true` の想定にもかかわらず画面で「🚫 Factory対象外」と表示されていた。本来 Factory 自動実行対象にしたい Epic が対象外になる原因を特定し修正する。
- 背景: 両 Epic は `decisionPolicy: autonomous` / `riskFlags: ["deploy"]`。`deploy` があるだけで対象外になっていた。
- 期待効果: riskFlags（deploy 等）だけで「対象外」にせず、「要承認」など別表示に分け、autonomous は Factory 対象候補として扱う。

---

## 2. 実施内容

- **原因特定**: `lib/epic-contract.ts` の `evaluateFactoryEligibility` が、riskFlags / approval_required / pendingApproval / blocked / 構造不備 / factoryEligible=false を**すべて `eligible=false`（=「Factory対象外」）に畳み込んでいた**。`deploy` は `FACTORY_BLOCKING_RISK_FLAGS`（ハード除外）に含まれる。
- **切り分け**: 実データ `data/real/epics.json` の該当2件は `factoryEligible:true` / `autonomous` / `riskFlags:[deploy]` で**正しい**。localStorage 上書き・デフォルト false 化は無し（未指定は `undefined`）。正本は `epics.json`。→ **UI 判定ロジックのみが誤り**。
- **修正**: 判定を 3 区分に再設計。
  - `excluded`（Factory対象外）: factoryEligible=false / decisionPolicy=manual / 契約の構造不備
  - `approval`（要承認）: riskFlags あり / approval_required / Approval待ち / blocked
  - `auto`（自律実行可）: 上記いずれにも該当せず
  - `eligible`（実行ゲート）は `classification==='auto'` のときだけ true → **factory-dispatch / factory-runner / auto-resume の自動実行ガードは従来どおり厳格**（deploy は自動実行されない＝安全性不変）。
- **表示ヘルパー** `describeFactory` を追加し、全カードの文言を統一（主バッジ=Factory対象/対象外、副バッジ=要承認/自律実行可）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `lib/types/operations.ts` | `FactoryEligibility` に classification / factoryManaged / approvalReasons / excludedReasons 追加。`FactoryClassification` 型新設 |
| `lib/epic-contract.ts` | `evaluateFactoryEligibility` を 3 区分判定に再設計。`describeFactory` ヘルパー追加 |
| `app/epic/page.tsx` | 一覧カードを主バッジ＋副バッジの 2 段表示に |
| `app/epic/[epicId]/page.tsx` | 詳細ページを同様の 2 バッジ表示に。対象外理由/要承認理由を分離 |
| `components/recommended/RecommendationList.tsx` | おすすめ一覧を 3 状態表示（後方互換 fallback 付き） |
| `components/epic/EpicJsonImport.tsx` | インポートプレビューの Factory 表示を 3 状態化 |
| `app/recommended-epics/[id]/page.tsx` | おすすめ詳細の Factory 判定文言を 3 状態化 |
| `lib/recommended-epics-store.ts` | `factoryEligiblePreview` に classification / factoryManaged を格納 |
| `types/recommended-epic.ts` | `FactoryEligiblePreview` に classification / factoryManaged（任意）追加 |

---

## 4. 検証結果

- typecheck: OK（`tsc --noEmit` 0 error）
- build: OK（`next build` 成功）
- lint: OK（変更9ファイル `eslint` 0 error）
- 手動確認: dev（127.0.0.1:3019）起動 → `GET /epic` HTTP 200。対象2件が **⚙ Factory対象 + 🛡 要承認**、構造不備の1 Epicのみ 🚫 Factory対象外。`GET /epic/epic-progress-url` HTTP 200 で主 ⚙ Factory対象・副 🛡 要承認・対象外表示0・要承認理由行ありを確認。
- 実データ検証: `tsx` で全7 Epic の classification を確認（autonomous+deploy → appro/managed、構造不備 → excluded、autonomous+no risk → auto）。
- 機密パターン事前チェック: OK（実値なし）
- 既存データ補正: **不要**（`epics.json` は既に factoryEligible:true）。データは一切変更していない。
- git push: **保留**（理由は §6）

---

## 5. 未対応

- **git commit/push 保留**: ny01 作業ツリーに本件無関係の大量未コミット変更（news-app / ai-trend-sns / anglerlog / birdlog / `data/real/*.json` 実行時データ / Factory・recommended 機能の WIP ファイル群）があり、巻き込みを避けるため自動 commit を見送り。ユーザー判断で本件9ファイルのみ選択 commit を推奨。
- **本番反映**: pm2 の progress（ポート3010）は旧ビルドのまま。画面反映には rebuild + `pm2 restart progress` が必要（ユーザー判断）。
- factoryEligible:false の Epic での「対象外」表示は実データに該当 Epic が無いためロジック検証のみ（構造不備 Epic で対象外表示は確認済み）。

---

## 6. 危険ポイント

- **実行ゲートの安全性は不変**: `eligible` は `auto` のときだけ true。deploy 等 riskFlags 付き Epic は依然 `eligible=false` で `buildDispatchPlan` が `safetyStatus='blocked'` にする＝**承認なしの自動実行はされない**。表示だけを「対象外→対象+要承認」に変更。
- **データ非破壊**: `epics.json` / 進捗率 / ステータスは未変更。Epic 削除・リセットなし。
- **二重管理なし**: 判定は `evaluateFactoryEligibility` 単一ソース。表示は `describeFactory` 単一ソースに集約。
- commit を保留したため、Claude Code 再起動で作業ツリーの編集が失われるリスク（ファイルは保存済みだが未コミット）。

---

## 7. 次にやるべきこと

- ユーザー: 本件9ファイルのみ選択的に commit/push（無関係 WIP・データ JSON は除外）。コマンド例は ChatGPT 依頼文の下に記載。
- `pm2 restart progress` で本番（3010）へ反映し、iPhone/PC で対象2件が「Factory対象」表示になることを確認。
- 将来: `FACTORY_BLOCKING_RISK_FLAGS` は現状 evaluate では未使用（export は残置）。完全に役割を終えたら削除を検討。

---

## 8. ChatGPT レビュー依頼文

```
対象アプリ: progress（/root/company/apps/ny01/progress, ポート3010）
runId: 20260608-133645 / commit: 未push（作業ツリーに無関係WIPが多く保留）
変更: EpicカードのFactory判定を3区分に再設計。
  原因 = evaluateFactoryEligibility が riskFlags(deploy)/approval_required/pending を全部
         eligible=false=「Factory対象外」に畳み込んでいた表示ロジックのバグ。
         実データ(epics.jsonのfactoryEligible:true)は正しかった。
  修正 = excluded(factoryEligible=false/manual/構造不備) / approval(riskFlags/approval_required/pending/blocked)
         / auto の3区分。eligible(実行ゲート)はautoのみtrueで自動実行の厳格さは維持。
         表示は主バッジ(Factory対象/対象外)+副バッジ(要承認/自律実行可)。

確認してほしい観点:
1. 安全性: 表示を「対象→要承認」に緩めても、deploy等のriskFlags付きEpicが
   承認なしで自動実行される経路が本当に塞がっているか(factory-dispatch/runner/auto-resumeのeligible依存)。
2. 区分定義の妥当性: manual を excluded、approval_required を approval に振り分ける設計は意図通りか。
   構造不備(goal/doneCriteria欠落)を excluded にするのは正しいか。
3. 後方互換: recommendedのfactoryEligiblePreviewにclassification未設定の旧データが残った場合の
   fallback推定(eligible→auto/approval)は妥当か。
4. commit運用: ny01作業ツリーに無関係WIPが多くcommitを保留した判断は妥当か。
   本件9ファイルだけ選択commitすべきか、別の整理が必要か。

選択commit用コマンド例:
cd /root/company/apps/ny01/progress
git add lib/types/operations.ts lib/epic-contract.ts app/epic/page.tsx \
  "app/epic/[epicId]/page.tsx" components/recommended/RecommendationList.tsx \
  components/epic/EpicJsonImport.tsx "app/recommended-epics/[id]/page.tsx" \
  lib/recommended-epics-store.ts types/recommended-epic.ts
git commit -m "fix(epic): riskFlags(deploy)だけでFactory対象外にしない3区分判定に修正"
git push origin main
```
