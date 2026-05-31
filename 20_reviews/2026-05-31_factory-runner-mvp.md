---
date: 2026-05-31
task: Factory実行自動化MVP（executor adapter + factory-runner dry_run/manual/auto・caps・Claude→Codex fallback）
runId: 20260531-190446
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260531-180730, 20260531-165655, 20260531-155036]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 Factory 実行自動化 MVP（factory-runner + executor adapter）

> Dispatch Plan を「作るだけ」から「実際に executor へ渡して実行→ExecutionRun 記録→Epic 内ループ」へ前進。
> 安全第一: 既定 dry-run・caps（1 起動最大 3 Run / 同一 Epic 最大 3 Run）・auto は confirm 必須。スケジュールは未有効化の設計案のみ。

---

## 1. 現状調査結果（Phase A）

- claude: `/root/.local/bin/claude` v2.1.158（Claude Code）。`-p/--print` で非対話可。**認証済**（`claude -p "reply PONG"` → PONG）。
- codex: `/root/.local/bin/codex` v0.135.0。`codex exec`（alias e）で非対話可。プロンプトは引数 or stdin。
- node v22 / npx 有。pm2 は progress のみ稼働（vloop 停止中）。systemd ユニット無・cron 無。

## 2. Claude/Codex 実起動可否（確認 8 問への回答）

1. Claude を VPS から非対話起動できるか → **可能**（claude -p、認証済）。
2. Codex を VPS から非対話起動できるか → **可能**（codex exec）。
3. 現在の実行方法 → CLI 利用可。これまでの運用は主に手動貼り付け。
4. 非対話不可時の代替 → 該当せず（両方可）。ただし安全のため既定は dry-run/manual。
5. Claude 上限エラーを runner 側で検知できるか → **可能**（stdout/stderr の文言を looksRateLimited で判定）。
6. Codex へ安全に切替えるのに必要な情報 → Epic Contract（riskFlags/decisionPolicy）+ Dispatch Plan の canRunOnCodex + classifyCodexEligibility（プロンプト本文の deny 判定）。
7. ExecutionRun へ自動記録できるか → **可能**（runner が addExecutionRun。factoryRun/source=factory_runner/runnerMode）。
8. doneCriteria 達成判定の自動化 → 部分的。現状は nextActions 空で Epic ループ停止する暫定。実 done マークは人/Approval 判断（誤判定回避）。

## 3. factory-runner 設計

scan→pick→Dispatch Plan→executor 選択→（adapter で）Run→ExecutionRun 記録→Next Actions/doneCriteria 確認→Epic 内ループ。
- Factory OFF なら何もしない（scanFactoryDispatch がゲート）。
- caps: maxRuns 3 / maxPerEpic 3（最大 3 にクランプ＝無限ループ防止）。
- 基本 Claude。result.rateLimited で AutoFallback 評価 → ON かつ canRunOnCodex なら Codex、不可なら停止記録。
- 停止条件: factory_off / no_candidate / blocked / approval_required / run_failed / rate_limited_no_codex / doneCriteria要確認 / max_runs。

## 4. executor adapter 設計

共通 IF `ExecutorAdapter.run(input) → ExecutorResult{status, stdout, stderr, resultSummary, changedFiles, errorType, rateLimited, needsApproval, nextActions}`。
- manual: 実起動せず needs_manual。
- claude（基本）: dryRun 擬似 / 実起動は `claude -p`、git 前後差分で changedFiles、上限検知で rateLimited=true。
- codex（fallback）: 実行前に classifyCodexEligibility で安全判定（deny→needsApproval・起動しない）、OK なら `codex exec`。
- getAdapter レジストリで executor 非依存（将来追加可）。

## 5. Epic 内ループ設計

同一 Epic で Dispatch→Run→Record→NextActions/doneCriteria 確認→未完了なら次 Dispatch。初期制限: 1 起動最大 3 Run / 同一 Epic 最大 3 Run / Approval 発生で停止 / blocked 停止 / rate limit→Codex fallback / Codex 不可で停止。

## 6. AutoFallback 実行設計

Claude→rateLimited→AutomationLog(auto_fallback)→AutoFallback ON & Codex 安全 → Codex 実行 → ExecutionRun。Codex 不可なら blocked / approval_required / requires_claude として記録。

## 7. スケジュール起動設計（未有効化）

`docs/factory-schedule/`: factory-runner.sh（既定 dry_run の curl）/ factory-runner.service（oneshot）/ factory-runner.timer（毎朝 11:00）/ factory-runner-boot.service（VPS 起動時）/ crontab.disabled / README。**いずれも未インストール。enable はユーザーが承認後に手動実行。**

## 8. MVP 実装範囲

Phase A（調査）〜 D（manual runner 記録自動）+ E/F（claude/codex adapter 実装）+ G（caps 付きループ骨格）。完全無人スケジュール有効化（H）と doneCriteria 完全自動判定は未。

## 9. 今回実装した内容（変更ファイル）

| ファイル | 内容 |
|---|---|
| lib/executors/types.ts, shell.ts, manual.ts, claude.ts, codex.ts, index.ts | adapter 一式（新規） |
| lib/factory-runner.ts | runner 本体（新規） |
| app/api/operations/factory-run/route.ts | runner 起動 API（新規） |
| types/execution-run.ts | factoryRun/runnerMode（任意・後方互換） |
| components/automation/FactoryRunnerPanel.tsx | 開発者モード用 runner UI（新規） |
| app/automation/page.tsx | Factory Runner セクション |
| docs/factory-schedule/* | 未有効化のスケジュール案 |

## 10. 検証結果

- typecheck / build / lint: OK
- Phase A: claude -p → PONG（実起動）/ codex exec 確認
- T1 OFF → factory_off / 0 step
- T2 ON + dispatchable → dry_run 1 step（claude / needs_manual）/ ExecutionRun 追加なし
- T3 manual → ExecutionRun 記録（factoryRun=true / source=factory_runner / runnerMode=manual / running）/ manual_execution_pending 停止
- T4 auto（confirm 無）→ auto_requires_confirm / 実起動なし
- 検証後データ全復元（epics=epic-91 のみ / factoryRun run 0 / OFF）

## 11. 危険箇所

- auto 実起動はファイル変更を伴うため**実 Epic では検証していない**（claude/codex 実起動自体は PONG/smoke で確認）。
- runner は API 同期実行のため auto 長時間はリクエストをブロックしうる（将来 background 化）。
- 安全装置: Factory OFF 即停止 / caps 最大 3 / auto confirm 必須 / Codex 安全判定二重 / Approval・blocked・rate-limit 停止 / 新正本なし。

## 12. 未対応点

- 安全な小 Epic での auto 1 Run 疎通（ユーザー承認後）。
- doneCriteria 自動判定（checks/build 結果との突合）。
- 複数 Epic 横断ループ（P2）。systemd/cron 有効化（ユーザー承認後）。

## 13. 次の一手

- ユーザー承認のうえ、安全な小 Epic（docs/epic-contract-test-epic.json）で **auto・最大 1 Run・confirm 付き**の疎通を 1 回だけ実施し、実起動→記録→停止までを観測する。

## 14. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: Factory実行自動化MVP（executor adapter + factory-runner dry_run/manual/auto・caps・Claude→Codex fallback）
runId: 20260531-190446
日付: 2026-05-31
GitHub commit: (progressアプリのコードは未commit / Vaultレビューのみ反映)

## 実施内容
- Phase A 調査: claude(-p)/codex(exec) 非対話起動が可能と実機確認（claude -p→PONG）。
- executor adapter(claude/codex/manual)共通IF + factory-runner(scan→pick→Dispatch→Run→ExecutionRun記録→Epic内ループ、caps=最大3Run)。
- 既定dry_run・manualは記録まで・autoはconfirm必須でClaude基本→上限時Codex fallback。
- スケジュール(systemd/cron)は未有効化の設計案のみ。

## 検証
- build/tsc/lint OK。OFF→factory_off、dry_run 1step(記録なし)、manual→ExecutionRun記録、auto(confirm無)→停止。実epic auto未実行。

## 確認したい観点
- runner の停止条件・caps（最大3）は無限ループ防止として十分か
- auto を API 同期実行にしている点（長時間ブロック）の是非と background 化の必要性
- Claude→Codex fallback の安全判定二重化は十分か
- doneCriteria 自動判定を入れずに nextActions 空で停止する暫定は妥当か
- スケジュールを未有効化の設計案に留めた判断は妥当か
- 次に auto 1Run 疎通へ進んでよいか（安全な小Epic・confirm付き）
````

---

## 関連

- progress runId: 20260531-190446（正本）
- 関連 run: 20260531-180730（Factory ON/OFF）, 20260531-165655（Factory Dispatch準備）
- Factory設計: [[../06_research/factory-orchestration-design]]
- テスト用Epic: [[../03_prompts/epic-contract-test-epic.json]]
- 関連アプリ: [[../02_apps/progress]]
