---
date: 2026-05-31
task: Codex adapter修正（stdin hang根本原因）＋Claude→Codex実運転成功
runId: 20260531-210900
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260531-200904, 20260531-194218]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 Codex adapter 修正（stdin hang 根本原因）

> 最優先タスク: Codex adapter の「Reading additional input from stdin」待機を調査・修正。
> 真因は stdin そのものではなく、shell helper が空文字 input を falsy 判定し stdin を閉じていなかったこと。
> 修正後、実データで **simulateRateLimit → AutoFallback → Codex実行(completed) → ExecutionRun → doneCriteria** まで成功。

---

## 調査結果（確認項目）

- **spawn時のstdio設定**: 既定は pipe（stdin/out/err 全て）。stdin が pipe のままだと codex は EOF を待ち続ける。
- **stdinを閉じる処理（真因）**: 旧 `if (opts.input) { write; end }` は `opts.input === ''`（空文字）が **falsy** のため write/end を呼ばず、**stdin pipe が開きっぱなし** → codex が EOF 待ち → 300s timeout（status=partial）。
- **/dev/null相当**: spawn `stdio[0]='ignore'` で stdin を /dev/null 化すれば codex は即座に stdin なしで進行。
- **codex exec推奨起動方法**: `codex exec -C <dir> --skip-git-repo-check -s workspace-write <prompt>`（stdin なし / workspace-write で cwd 配下のみ書込可）。
- **Claude adapterとの差分**: claude も同じく `input:''` を渡しており同じ穴。両方修正。

再現検証（node spawn）: 簡単なプロンプトで A) pipe+write('')+end と B) stdin ignore の両方が即完了（9s/6s・ファイル生成）。→ stdin を正しく閉じる/ignore すれば hang しない。旧 adapter は空文字 falsy で end 未呼出のため hang していた。

---

## 修正内容

1. `lib/executors/shell.ts`: `const wantStdin = typeof opts.input === 'string'`; spawn `stdio=[wantStdin?'pipe':'ignore','pipe','pipe']`; wantStdin 時のみ write+end。→ input 未指定なら /dev/null 相当。
2. `lib/executors/codex.ts` / `claude.ts`: `input:''` 除去（stdin ignore 経路へ）。
3. （併せて）`lib/factory-runner.ts`: 安全判定の意図テキストを **Epic 固有の goal + doneCriteria** に限定。旧コードは `plan.nextActions`（他 Epic 由来の global 候補）を使い「削除」等を誤検知して Codex を block していた。

---

## 実運転結果（完了条件・実データ）

testEpic 投入（eligible）→ Factory ON + autoFallback ON → auto maxRuns=1 **simulateRateLimit** confirm。

- [claude 擬似] rate_limited
- [AutoFallback] auto_fallback 記録（claude_rate_limited）/ canRunOnCodex=true
- [Codex] `codex exec -C progress -s workspace-write` 起動 → **174s completed**（hang せず）
- app/automation/page.tsx の文言を 1 箇所修正（codex 自身が既存未コミット変更に配慮）
- [checks] runChecks: **typescript=OK / lint=OK**
- [record] ExecutionRun 20260531-210606（**executorUsed=codex / source=factory_runner / runnerMode=auto / factoryRun=true / runStatus=completed**）
- [doneCriteria] **3/4 continue** → max_runs_reached 停止

→ simulateRateLimit → AutoFallback → Codex実行 → ExecutionRun → doneCriteria まで**実データで成功**。完了条件達成。

### Claude→Codex 切替の時系列

```
t0  Factory auto 起動 → scan → pick(epic-progress)
t1  Claude(擬似) → rateLimited=true
t2  AutoFallback → automation-log: auto_fallback(claude_rate_limited) / canRunOnCodex=true
t3  Codex 起動 codex exec -C progress -s workspace-write
t4  Codex completed (174s) → 文言1箇所修正
t5  runChecks → typescript OK / lint OK
t6  ExecutionRun 記録 executorUsed=codex / source=factory_runner
t7  doneCriteria 3/4 → continue → max_runs_reached 停止
```

---

## 問題点 / 補足

- runner の changedFiles は、executor が**既に dirty なファイル**を編集すると前後 git diff で空になる（本番の clean repo では発生しない）。doneCriteria criterion1（1箇所変更）が未達で 3/4 はこれが原因。
- 実運転検証は progress repo で実施し、codex が app/automation/page.tsx を文言修正 → 検証後 tar スナップショットで**完全復元（net 変更ゼロ・git status 51件=自作業 intact）**。実運用では executor cwd のサンドボックス化が残課題。

---

## 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/executors/shell.ts | stdin: input 未指定時 'ignore'(/dev/null相当)・指定時のみ pipe+write+end |
| lib/executors/codex.ts | input:'' 除去・安全判定 safetyText 優先・workspace-write 起動 |
| lib/executors/claude.ts | input:'' 除去 |
| lib/executors/types.ts | ExecutorRunInput.safetyText 追加 |
| lib/factory-runner.ts | 意図テキスト=goal+doneCriteria・simulateRateLimit/cwd・done時 next Epic advance |
| app/api/operations/factory-run/route.ts | simulateRateLimit/cwd 受理 |

## 検証
- build OK / tsc クリーン / lint OK。
- spawn 再現テストで真因確定。実運転で Codex completed・ExecutionRun・doneCriteria 評価まで成功。検証後 tar 全復元。

## 次の一手
- P2 複数Run / P3 スケジュールOFFガード / P4 複数Epic（本タスク対象外・次回）。
- executor 実行 cwd のサンドボックス化（実 repo 汚染防止）。

## ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象: progress（AI工場 / Next.js / 3010）
作業: Codex adapter修正（stdin hang根本原因）＋Claude→Codex実運転成功
runId: 20260531-210900

## 真因
shell helper の if(opts.input) が空文字''をfalsy判定 → stdin.end()未呼出 → stdin pipe開きっぱなし → codexがEOF待ちで300s timeout。

## 修正
input未指定時はspawn stdin='ignore'(/dev/null相当)、指定時のみpipe+write+end。codex/claudeのinput:''除去。併せてCodex安全判定の意図テキストをgoal+doneCriteriaに限定(global nextActionsの削除誤検知回避)。

## 実運転
simulateRateLimit→AutoFallback→Codex実行(completed 174s)→ExecutionRun(executorUsed=codex/source=factory_runner/checks tsc・lint OK)→doneCriteria 3/4 評価。検証後tar全復元。

## 確認したい観点
- stdin ignore(/dev/null相当)の方針は妥当か
- 安全判定テキストをgoal+doneCriteriaに限定した判断は妥当か
- changedFiles空判定(既存dirty)への対処方針(clean repo前提 or cwdサンドボックス)
- P2-P4へ進んでよいか
````

---

## 関連
- progress runId: 20260531-210900（正本）／ 実行記録 20260531-210606（codex）
- 関連 run: 20260531-200904（doneCriteria）, 20260531-194218（実運転疎通）
- 関連アプリ: [[../02_apps/progress]]
