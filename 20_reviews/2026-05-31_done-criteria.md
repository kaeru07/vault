---
date: 2026-05-31
task: doneCriteria自動判定エンジン実装＋実運転DONE確認
runId: 20260531-200904
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260531-194218, 20260531-190446]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 doneCriteria 自動判定エンジン

> Factory が Epic→実行→ExecutionRun→**doneCriteria判定→done/continue** を自動判断できるようにした。
> 新正本なし（ExecutionRun / Epic Contract / checks を利用）。executor 非依存（Claude/Codex 共通）。
> 実運転で 実claude(174s)→ExecutionRun→checks(tsc/lint OK)→**doneCriteria 4/4→epic_done** を確認。

---

## 実装内容

- `lib/done-criteria.ts`: エンジン（純粋関数 evaluateDoneCriteria + 入口 getDoneCriteriaForEpic）。
- `lib/checks-runner.ts`: tsc --noEmit / next lint / npm run build を実行し OK/NG を返す → ExecutionRun.checks へ構造化保存（L1）。
- `lib/factory-runner.ts`: auto 時に executor 実行後 runChecks → checks 付きで ExecutionRun 記録 → getDoneCriteriaForEpic で done 判定。done=epic_done 停止 / continue=次 Run。従来の「nextActions 空停止」を置換。
- `app/api/operations/done-criteria/route.ts`: GET ?epicId=。
- UI: Automation FactoryProgressCard と Epic 詳細に doneCriteria 達成状況（各 criterion ✓/✗ + level + evidence、達成率 N/M、DONE/CONTINUE）。

## 判定ロジック（5レベル）

- **L1 機械判定**: criterion が build/typecheck/lint に言及 → checks の該当値が OK/pass/成功 か。「または」を含むため言及中 1 つでも OK なら met。
- **L2 ファイル変更**: changedFiles にファイル/語が一致するか（bigram files 一致率≥0.25）。
- **L3 曖昧一致**: summary + rawReport と criterion の bigram 重なり（text≥0.18 / 汎用≥0.3）。
- **meta**: ExecutionRun 記録（run 有 + executorUsed）/ 未承認作業（pendingApproval=0）。
- **L4**: 全 met → done / **L5**: 一部未達 → continue。

## 判定できる doneCriteria 種類

- build / typecheck / lint 成功（L1・高精度）
- ファイル変更・文言修正・画面追加など変更系（L2/L3）
- ExecutionRun 記録・executorUsed・未承認作業なし（meta）

## 判定できない / 弱い種類

- 主観的品質（「使いやすい」等）
- 外部条件（本番反映確認・実機確認）
- 否定条件の厳密判定
- 日本語同義語（L3 bigram の限界）

## 誤判定リスク

- L3 曖昧一致: summary に語が含まれるだけで met 化する**偽陽性**。
- checks 未記録時に build/typecheck criterion が**偽陰性**。
- 「1 箇所だけ」等の件数条件は changedFiles 長依存。集約 run が複数だと過大に見える。
- → 緩和: L1/meta を優先し、L3 は閾値を保守的に設定。最終的な Epic done マークは人/Approval（自動で status=done にはしない）。

## Factoryループへの組込み方法

scan→pick→Dispatch→Run→**runChecks**→ExecutionRun（checks 付き）→**getDoneCriteriaForEpic**→
done? → epic_done 停止 / continue → 次 Run（caps 内）。
停止条件: done=Epic完了 / continue=次Run / blocked / approval_required / fail / max_runs。

## build / lint / typecheck

- build: OK（next build 成功）
- typescript: OK（tsc --noEmit クリーン）
- lint: OK（next lint 警告0）
- runner 内 runChecks でも typecheck=OK / lint=OK を記録（実運転）。

## 実運転テスト結果

- エンジン合成検証: DONE 4/4（全 criterion met）/ CONTINUE 2/4（file✗ typecheck✗ / ExecutionRun✓ 未承認✓）。
- 実運転: テスト Epic（epic-progress）投入 eligible → Factory ON → auto maxRuns=1 confirm → 実 claude -p（174s）。
  - runner ExecutionRun 20260531-200602（checks typescript=OK / lint=OK）
  - claude 自己 POST 20260531-200459（changedFiles=app/epic/page.tsx / typecheck pass）
  - doneCriteria 集約 = **4/4**（L3 文言✓ / L1 typecheck✓ / meta ExecutionRun✓ / meta 未承認✓）
  - stoppedReason = **epic_done（doneCriteria 4/4）** ← done 駆動で停止
- 安全対応: 実行前 tar スナップショット + データ 5 ファイル backup。claude の編集は app/epic/page.tsx（blocked ラベル 停止中→ブロック中）のみ。harness が当該変更を保持判断したため、データファイルのみ復元（epic-91 のみ / OFF / epic-progress runs 0）。app/epic/page.tsx は自分の作業 + claude 微修正を保持し tsc/lint clean。

## 残課題

- L3 の精度向上（同義語 / 否定の扱い）。
- Epic 完了時の status=done 自動マーク是非（現状は停止のみ・人/Approval 判断）。
- runner cwd のサンドボックス化（実 repo 汚染防止）。
- build 判定が必要な Epic では runChecks の build を有効化。

## ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: doneCriteria自動判定エンジン実装＋実運転DONE確認
runId: 20260531-200904
日付: 2026-05-31

## 実装
- doneCriteriaエンジン(L1機械build/typecheck/lint・L2 changedFiles・L3 bigram曖昧一致・meta)。done/continue。executor非依存。
- checks-runnerでtsc/lint/buildをExecutionRun.checksへ構造化。
- factory-runnerに組込み(done=epic_done停止)。Automation/Epic詳細にUI。

## 実運転
- 実claude(174s)→ExecutionRun(checks tsc/lint OK)→doneCriteria 4/4→epic_done。合成検証でCONTINUE 2/4も確認。

## 確認したい観点
- 5レベル判定の優先順と閾値(L3 bigram)は妥当か
- 誤判定(L3偽陽性/checks未記録偽陰性)の緩和は十分か
- Epic done自動マークを入れずに停止に留める判断は妥当か
- runner cwdサンドボックス化の優先度
````

---

## 関連

- progress runId: 20260531-200904（正本）／ 実行記録 20260531-200602（factory_runner）
- 関連 run: 20260531-194218（実運転疎通）, 20260531-190446（runner MVP）
- テスト用Epic: [[../03_prompts/epic-contract-test-epic.json]]
- 関連アプリ: [[../02_apps/progress]]
