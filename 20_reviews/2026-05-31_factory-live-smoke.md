---
date: 2026-05-31
task: Factory実運転疎通テスト（安全な小Epicで実Claude起動→ExecutionRun記録→停止）
runId: 20260531-194218
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260531-190446, 20260531-180730]
commitHashes: []
# reviewFileCommit:
---

# Factory実運転結果

> 実データで Factory ON → Epic Pick → Claude実行 → ExecutionRun記録 → doneCriteria評価 → 停止 を確認。
> 実 `claude -p` を起動（146s）。テスト後に Claude の編集と全データを復元し net 変更ゼロ。新機能追加なし。

## Epic
- title: Progress文言の軽微修正テスト
- epicId: epic-progress
- Contract判定: eligible=true（reasons 空）

## 実行結果
- Pick: ✓（scan→pick で epic-progress を選択）
- Dispatch: ✓（buildDispatchPlan / dispatchPlanId=dp-epic-progress-…）
- Claude起動: ✓（claude -p 実起動・146s・completed）
- ExecutionRun: ✓（runId 20260531-192701 / source=factory_runner / runnerMode=auto / factoryRun=true / executorUsed=claude / runStatus=completed）
- changedFiles: ✓（progress/app/page.tsx を 1 行修正＝Claude が実編集、typecheck 成功と報告）
- nextActions: ✓（取得＝空配列）
- doneCriteria: △（自動評価は未実装。nextActions 空で停止する暫定。実 done マークは人/Approval）

## 停止判定
- stopReason: max_runs_reached（cap=1）
- expected: max_runs_reached（maxRuns=1 のため）/ もしくは nextActions 空による doneCriteria要確認
- actual: max_runs_reached（cap が先に発火。nextActions 空の停止条件も成立）
- 補足: blocked / approval_required / fail の停止は前ターン runner 検証（T 系）で確認済み

## AutoFallback
- tested: ✓（POST /api/operations/claude-limit force=true epicId=epic-progress / Codex 実行はしない）
- result: auto_fallback 記録（automation-log ×5）/ safetyGuard=true / canRunOnCodex=true（dispatch plan）/ fallback.status=blocked（requires_approval）
  - → Claude→AutoFallback→Codex判定 まで到達。progress スコープの pending_approval タスクにより**要承認で安全に blocked**（codex_ready には至らず＝安全ゲートが正しく機能）。codex_ready 生成自体はクリーンスコープ Epic で別途確認済（runId 20260531-155036）。

## UI
- Running: ✓（Factory ON + dispatchable Epic）
- Paused: ✓（Factory OFF）
- Blocked: ✓（前ターンで ON + 不完全 Epic にて観測。状態機械ロジック実装済み）
- CodexReady: △（状態機械ロジック実装済み。上限検知 + Codex 可の同時成立が条件のため本テストでは未発火）

## 問題点
- runner は API 同期実行のため auto 1Run で 146s リクエストがブロック（spawn は非同期のためサーバ全体は停止せず）。
- 実 `claude -p` が cwd=progress リポジトリで**実ファイルを編集**する（本番運用では cwd 分離 / サンドボックスが必要）。
- doneCriteria 達成判定が未自動化（暫定で nextActions 空停止）。

## 完全自動化までの残課題
- doneCriteria 自動評価（build / typecheck / checks 結果との突合）。
- runner の background 化（長時間 auto のブロック回避）。
- 実行 cwd の分離 / サンドボックス（実リポジトリ汚染の防止）。
- 複数 Epic 横断ループ（P2）。
- スケジュール（systemd timer / boot）の段階有効化（ユーザー承認後）。
- 通知（push）と結果戻しワンタップ。

---

## 安全対応（テスト衛生）

- 実行前に progress ソース + データの tar スナップショットを取得。
- Claude の編集は app/page.tsx 1 ファイルのみ（変更前 clean を確認）→ `git checkout -- app/page.tsx` で復元。
- データ 5 ファイル（epics / execution-runs / automation-config / automation-log / approvals）をバックアップから復元。
- 検証後: epics=epic-91 のみ / factoryEnabled OFF / factory_runner run 0 / app/page.tsx clean / git status=元の 48 件（未コミット作業 intact）/ tsc OK。**net 変更ゼロ**。

## ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: Factory実運転疎通テスト（安全な小Epicで実Claude起動→ExecutionRun記録→停止）
runId: 20260531-194218
日付: 2026-05-31

## 結果
- 実データで Factory ON→Pick→実claude -p(146s)→completed→ExecutionRun記録(source=factory_runner/runnerMode=auto/changedFiles=app/page.tsx)→max_runs停止。
- AutoFallbackシミュ: auto_fallback記録・canRunOnCodex=true・要承認で安全にblocked。
- UI: Running/Paused確認。Blocked/CodexReadyはロジック実装済。
- テスト後に編集と全データを復元しnet変更ゼロ。

## 問題点
- runner同期実行で146sブロック / 実claude -pがprogress repoを編集（cwd分離が必要）/ doneCriteria自動判定未。

## 確認したい観点
- 実運転の安全境界（cwd分離/サンドボックス）をどう設計すべきか
- doneCriteria自動評価の最小実装方針
- runner background化の必要性と方式
- 次に複数Epic横断やスケジュール有効化へ進む条件
````

---

## 関連

- progress runId: 20260531-194218（正本）／ 実行記録 runId: 20260531-192701（factory_runner）
- 関連 run: 20260531-190446（runner MVP）, 20260531-180730（Factory ON/OFF）
- テスト用Epic: [[../03_prompts/epic-contract-test-epic.json]]
- 関連アプリ: [[../02_apps/progress]]
