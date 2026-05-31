---
date: 2026-05-31
task: Factory Dispatch準備フェーズ実装（選んだEpicをexecutorへ渡す準備）
runId: 20260531-165655
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260531-162903, 20260531-155036, 20260531-145153]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 Factory Dispatch準備フェーズ

> Factory 本体の前に、scan/pick で選ばれた Epic を Claude / Codex / manual executor へ「渡す準備」を整えた。
> 完全自動ループ・CLI 直叩き・cron/pm2/systemd は使わない。新しい正本は作らない（Dispatch Plan は生成ビュー、正本は ExecutionRun）。

---

## 1. 作業目的

- なぜ: 完全自動 Factory 疎通テストはまだ早いとユーザーが判断。まず dispatch に足りない部品（選んだ Epic を executor へ渡すプロンプト生成と結果戻しの結合）を揃える。
- 流れ: Factory Scan → Pick → Dispatch Plan 生成 → executor 選択 → executor 用プロンプト生成 → ユーザーがコピーして Claude/Codex へ → 結果を ExecutionRun へ戻す。
- 期待効果: Epic Contract を満たす Epic だけを安全に executor へ渡せる状態（半自動 dispatch）。

---

## 2. 成果物（1〜8）

- 1. 足りない部品の整理: Epic Contract / Factory対象判定 / Auto Resume / AutoFallback / Codex引き継ぎ / 結果戻し / ExecutionRun は実装済み。不足は「選ばれた Epic → executor 向けプロンプト生成 → 結果戻しの結合」。これを Dispatch Plan で埋めた。
- 2. Dispatch Plan 設計（生成ビュー・新正本なし）: dispatchPlanId / epicId / epicTitle / goal / doneCriteria / selectedReason / executorCandidate / preferred・fallbackExecutor / canRunOnCodex / requiresClaude / requiresApproval / approvalStatus / decisionStatus / riskFlags / nextActions / promptType / safetyStatus / blockedReason。既存正本から都度生成。
- 3. executor 選択ルール: requiresClaude=true→claude。canRunOnCodex（eligible ＆ (fallback/preferred=codex or classifyCodexEligibility OK)）＆ safety OK→codex 可。approval待ち/decision待ち/riskFlagsあり/manual→dispatch 不可。preferred 優先・なければ codex 可なら codex。executor 非依存。
- 4. Claude 用プロンプト: 安全判定/Goal/DoneCriteria/DecisionPolicy/RiskFlags/現在地/NextActions/禁止事項/ExecutionRun 記録指示（epicId・executorUsed・factoryDispatch・dispatchMode・dispatchPlanId・resultReturned）を単一プレーンテキストで生成。
- 5. Codex 用プロンプト: 既存 generateCodexPrompt を Dispatch Plan から呼ぶ。冒頭に安全判定ガード。canRunOnCodex 時のみ生成。
- 6. UI: Automation に Factory Dispatch（準備）。候補Epic一覧(picked)/Dispatch Plan詳細/Claude・Codexプロンプト生成/コピー/blocked理由/結果戻し(dispatchPlanId付き)。
- 7. Codex結果戻し接続: CodexReportForm に dispatchPlanId/executor を追加。付与時は factoryDispatch=true/dispatchMode=manual_copy/resultReturned=true/source=factory_dispatch で戻す。epicId/sourceRunId 結合可を確認。
- 8. ExecutionRun記録方針: 生成時=AutomationLog factory_dispatch、結果戻し時=ExecutionRun に factoryDispatch メタ（任意・保存仕様非破壊）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/factory-dispatch.ts | 新規。buildDispatchPlan/scanFactoryDispatch/generateClaudeFactoryPrompt/dispatchForExecutor |
| app/api/operations/factory-dispatch/route.ts | 新規。GET=scan/1件plan、POST=executor向けプロンプト生成+ログ |
| lib/types/operations.ts | ExecutorChoice/FactoryDispatchPlan/Scan/DispatchPromptResult・AutomationLog factory_dispatch |
| types/execution-run.ts | factoryDispatch等の任意メタ追加(後方互換) |
| app/api/execution-runs/route.ts | dispatchメタを受理・保存(非破壊) |
| components/automation/FactoryDispatchPanel.tsx | 新規。候補/Plan/プロンプト生成/コピー/結果戻し |
| components/codex/CodexReportForm.tsx | dispatchPlanId/executorプロップ・dispatchメタ送信 |
| app/automation/page.tsx | Factory Dispatch（準備）セクション |

---

## 4. 検証結果

- typecheck / build / lint: すべて OK（/api/operations/factory-dispatch 登録）
- 手動確認（curl・テストEpic作成→検証→復元）:
  - T1 scan → picked=テストEpic(claude) / candidates 1 / blocked 2（epic-91 doneCriteria無し・危険Epic deploy）
  - T2 POST claude → claude_factory プロンプト + dispatchPlanId
  - T3 POST codex → codex_handoff + 安全判定冒頭（canRunOnCodex via fallback codex）
  - T4 危険Epic codex → prompt null / blocked
  - T5 結果戻し dispatchPlanId付き → ExecutionRun に factoryDispatch/dispatchMode/dispatchPlanId/executorCandidate/resultReturned 永続化
  - T6 AutomationLog factory_dispatch × 3
  - 検証後復元: epics=epic-91のみ / factoryDispatch run 0 / automation-log 4行 / runs 163
  - 実データ live scan: picked=None（epic-91は契約不足でblocked）= 仕様どおり
- 機密チェック: OK

---

## 5. 未対応点

- 完全自動疎通テスト（今回対象外・ユーザー判断で次フェーズ）
- Epic詳細へのFactory Dispatchパネル展開（現状Automationのみ）
- pick順序の精緻化（priority+進捗+鮮度）
- progressアプリのコードは未commit（ユーザー判断）

---

## 6. 危険ポイント

- 既存への影響: 低。安全判定（evaluateAutoFallback/classifyCodexEligibility）・Codex引き継ぎ・Approval/Decision・ExecutionRun保存仕様は変更せず再利用/任意追加のみ。
- 完全自動実行なし・CLI直叩きなし・cron/pm2/systemdなし・無限ループなし。
- riskFlags/承認待ち/決定待ち/manual は dispatch 不可（多層ゲート）。
- 新正本なし（Dispatch Planは生成ビュー、dispatchPlanIdはUI経由で結果戻しへ引き継ぎ、正本はExecutionRun）。

---

## 7. 次にやるべきこと（次の一手）

- 小さいEpic（docs/epic-contract-test-epic.json）を投入し、Factory Dispatch で実際に Claude/Codex へ渡して1周回す**手動疎通テスト**。
- 以降 Factoryループ本体（scan→pick→dispatch→record→loop）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: Factory Dispatch準備フェーズ（選んだEpicをexecutorへ渡す準備）
runId: 20260531-165655
日付: 2026-05-31
GitHub commit: (progressアプリのコードは未commit / Vaultレビューのみ反映)

## 作業目的
- Factory本体の前に、scan/pickで選ばれたEpicをClaude/Codex/manualへ渡す準備（Dispatch Plan + プロンプト生成 + 結果戻し結合）を実装。完全自動ループは作らない。

## 実施内容
- Dispatch Plan（生成ビュー・新正本なし）: executorCandidate/canRunOnCodex/requiresClaude/approval・decisionStatus/riskFlags/safetyStatus/blockedReason 等。
- executor選択: requiresClaude→claude、canRunOnCodex&safety OK→codex、riskFlags/承認/決定/manual→dispatch不可。
- Claude用/Codex用プロンプト生成（既存generateCodexPrompt再利用・安全判定付き）。
- UI（Automation）: 候補Epic/Plan/プロンプト生成/コピー/blocked理由/結果戻し(dispatchPlanId)。
- 結果戻し: CodexReportFormにdispatchPlanId付与→ExecutionRunにfactoryDispatchメタ記録。

## 検証結果
- typecheck/build/lint OK。scan(picked/candidates/blocked)・claude/codexプロンプト・危険Epic→null・結果戻し永続化・AutomationLog記録 すべて確認。検証後データ復元。

## 確認したい観点
- executor選択ルール（requiresClaude/canRunOnCodex/blocked条件）は妥当か
- Dispatch Planを永続化せず生成ビューにし、正本をExecutionRunに寄せた設計は妥当か
- dispatchPlanIdをUI経由で結果戻しに引き継ぐ方式は十分か（永続結合が要るか）
- Claude用プロンプトの禁止事項・ExecutionRun記録指示は十分か
- riskFlags/承認/決定/manualのdispatch不可ガードは安全側に十分倒せているか
- executor非依存（将来executor追加可能）になっているか
- 次に手動疎通テスト（1周）へ進んでよいか
````

---

## 関連

- progress runId: 20260531-165655（正本）
- 関連 run: 20260531-162903（Epic契約形式）, 20260531-155036（Auto Resume/Approval即処理）
- Factory設計: [[../06_research/factory-orchestration-design]]
- テスト用Epic: [[../03_prompts/epic-contract-test-epic.json]]
- 関連アプリ: [[../02_apps/progress]]
