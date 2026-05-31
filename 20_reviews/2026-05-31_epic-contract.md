---
date: 2026-05-31
task: Epic作成を明示的な契約形式に整備（フォーム/JSONインポート/テンプレコピー + Factory対象判定）
runId: 20260531-162903
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260531-155036, 20260531-152019, 20260531-145153]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 Epic作成を明示的な契約形式に整備

> AI工場の自動化テスト開始前提。Factory/Auto Resume が曖昧な Epic を勝手に解釈しないよう、
> Epic 作成時に Goal / DoneCriteria / DecisionPolicy / Priority / riskFlags を明示する契約運用にした。

---

## 1. 作業目的

- なぜ: Factory/Auto Resume を自動で回す前に、対象 Epic の「目標・完了条件・判断方針・優先度・危険性」を人が明示し、未定義の Epic を自動実行しないようにする。
- 背景: Auto Resume / AutoFallback / Claude上限検知まで実装済み。次は自動化テストの入口（契約付き Epic）を整える。
- 期待効果: 完了条件と安全条件を満たす Epic だけが自動実行対象になり、最初の自動化テストを安全に開始できる。

---

## 2. 実施内容（成果物 1〜8）

- 1. 現状調査: Epic 作成画面なし / epics API は GET 専用 / Epic.priority は既存未使用（安全に追加可）/ decisionPolicy は `!== autonomous` ゲートのみ（`manual` 追加で整合）/ createEpic 不在。
- 2. Epic Contract 設計: epics.json（唯一の正本）の Epic に契約フィールドを**全て任意**で追加（既存 Epic 互換）。必須=title/goal/doneCriteria(1件以上)/decisionPolicy(autonomous/approval_required/manual)/priority(P0/P1/P2)/riskFlags。任意=notes/targetApp/relatedRepo/preferred・fallbackExecutor/factoryEligible。
- 3. 画面入力: `/epic/new` のフォーム（doneCriteria 動的行追加・riskFlags チェックボックス・policy/priority セレクト・notes・targetApp）。モバイル優先。`/epic` に作成ボタン＋対象バッジ。
- 4. JSONインポート: textarea → ① プレビュー（dryRun=検証のみ・Draft/Factory対象表示）→ ② confirm（作成）。
- 5. テンプレコピー: `epicTemplateText()` を素テキスト（入れ子コードブロックなし）でコピー。
- 6. バリデーション: title/goal 空→error、doneCriteria 空→error、policy/priority 不正→error、riskFlags 未知値→error、riskFlags 未指定→warning。
- 7. Factory対象判定 `evaluateFactoryEligibility`: goal 非空 & doneCriteria≥1 & policy/priority 設定 & policy≠manual & 危険 riskFlags（billing/production_db/auth_secret/deploy/migration/destructive）なし & 他 riskFlag（external_publish）も Approval 必須で除外 & Approval 待ち 0 & status≠blocked & factoryEligible≠false。1 つでも欠ければ対象外＋理由。factoryEligible=true でも安全条件が優先。
- 8. 自動化テスト用 Epic JSON: `docs/epic-contract-test-epic.json`（Vault: `03_prompts/epic-contract-test-epic.json`）。

Auto Resume 連携: epic-scope 時に `getFactoryEligibility` を追加ゲート化（安全ゲートの後段）。不完全 Epic は自動再開しない。既存ゲートは不変。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/epic-contract.ts | 新規。validate/eligibility/template/定数 |
| lib/types/operations.ts | DecisionPolicyに'manual'追加・Epic契約フィールド(任意)・各型・EpicDetail.factoryEligibility |
| lib/operations-store.ts | createEpic/getFactoryEligibility・getEpicDetailにeligibility付与 |
| app/api/operations/epics/route.ts | POST(dryRun=検証/作成)。GET維持 |
| components/epic/EpicCreateForm.tsx | 新規。フォーム入力 |
| components/epic/EpicJsonImport.tsx | 新規。preview→confirm |
| components/epic/EpicTemplateCopyButton.tsx | 新規。テンプレコピー |
| app/epic/new/page.tsx | 新規。3方式タブ |
| app/epic/page.tsx | 作成ボタン+対象バッジ |
| app/epic/[epicId]/page.tsx | Epic Contractセクション |
| lib/auto-resume.ts | epic-scope eligibilityゲート追加 |
| docs/epic-contract-test-epic.json | 新規。自動化テスト用Epic |

---

## 4. 検証結果

- typecheck / build / lint: すべて OK（/epic/new 登録）
- 手動確認（curl・data/real）:
  - T1 /epic/new（フォーム/インポート/テンプレ）描画
  - T2 不正JSON → goal空/doneCriteria空/policy不正/priority不正/riskFlags未知値 の5エラー
  - T3 テストEpic dryRun → ok / eligible / doneCriteria 4
  - T4 riskFlags:deploy → Factory対象外（riskFlags: deploy）
  - T5 decisionPolicy:manual → Factory対象外（manual）
  - T6 実作成 → epic-progress / eligible
  - T7 詳細に Epic Contract + ⚙Factory対象
  - T8 既存 epic-91（doneCriteria無し）→ 🚫Factory対象外で描画（壊れない）
  - T9 Auto Resume epic-scope → blocked
  - 検証後 epics.json 復元（epic-91 のみ）/ autoResume=false
- 機密チェック: OK

---

## 5. 未対応

- Epic 編集画面（既存 Epic への契約後付け）— 現状は作成のみ。既存 epic-91 は契約未設定のため Factory対象外のまま。
- DecisionPolicy 旧2値（budget/destructive_sensitive）→ 3値移行UI。
- Factory 本体（指示どおり未実装）。

---

## 6. 危険ポイント

- 既存機能への影響: 低。契約フィールドは全て任意・追加。既存 Epic 破壊なし・ExecutionRun 削除なし。安全ゲート（evaluateAutoFallback）・decideApproval は不変。
- epics.json への書き込みは createEpic（追記のみ）。新正本は作らない。
- 観察: 既存 epic-91 は契約未設定で「Factory対象外」表示になる（仕様どおり。誤解を招かないか観察）。

---

## 7. 次にやるべきこと（次の一手）

- **小さい Epic（docs/epic-contract-test-epic.json）を JSON 投入して最初の自動化テストを開始**。
- 今後の細かい改善は手動指示せず、Next Actions として回収し Factory 対象 Epic から順に処理。
- Epic 編集画面の追加（既存 Epic に契約を後付け）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: Epic作成を明示的な契約形式に整備（フォーム/JSONインポート/テンプレコピー + Factory対象判定）
runId: 20260531-162903
日付: 2026-05-31
GitHub commit: (progressアプリのコードは未commit / Vaultレビューのみ反映)

## 作業目的
- Factory/Auto Resume が曖昧な Epic を解釈しないよう、Epic 作成時に goal/doneCriteria/decisionPolicy/priority/riskFlags を必須化。

## 実施内容
- Epic Contract: epics.json の Epic に契約フィールドを全て任意で追加（既存互換・新正本なし）。
- 3方式: 画面フォーム / JSONインポート(preview→confirm) / JSONテンプレコピー。
- バリデーション + Factory対象判定（不完全・危険riskFlags・manual は対象外）。
- Auto Resume に epic-scope eligibility ゲートを追加（安全ゲートは不変）。
- 自動化テスト用 Epic JSON 同梱（03_prompts/epic-contract-test-epic.json）。

## 検証結果
- typecheck / build / lint: OK。
- 不正JSON→5エラー / テストEpic→eligible / deploy・manual→対象外 / 実作成→epic-progress / 既存epic-91→対象外で壊れない。検証後復元。

## 確認したい観点
- Epic Contract の必須/任意の切り分けは妥当か
- Factory対象判定ルール（riskFlags/manual/doneCriteria/Approval待ち）は安全側に十分倒せているか
- external_publish を「Approval必須で対象外」に倒した判断は妥当か
- 既存Epic（契約未設定）を一律 Factory対象外にする扱いは妥当か
- 新正本を増やさず epics.json 拡張で済ませた設計は妥当か
- executor非依存（preferred/fallback両対応）になっているか
- この後 docs/epic-contract-test-epic.json で自動化テストを始めてよいか
````

---

## 関連

- progress runId: 20260531-162903（正本）
- 関連 run: 20260531-155036（Auto Resume/Approval即処理）, 20260531-145153（Claude上限検知）
- テスト用Epic: [[../03_prompts/epic-contract-test-epic.json]]
- Factory設計: [[../06_research/factory-orchestration-design]]
- 関連アプリ: [[../02_apps/progress]]
