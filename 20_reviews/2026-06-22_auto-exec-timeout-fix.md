---
date: 2026-06-22
task: 自動実行が成果ゼロになる根本原因(実行タイムアウト300秒)の修正＋レポート明確化
runId: 20260622-212859
targetApp: progress
monetizationImpact: medium
theme: [workflow]
relatedRunIds: [20260622-204125]
commitHashes: [abb49df]
---

## 1. 作業目的
自動実行レポートで「この回でできたこと」がユーザーに全く分からない、という指摘を受けて調査。レポートの問題ではなく、**自動実行(factory-runner)が実際には成果を1つも生んでいない**ことが根本原因と判明したため、その真因を直す。

## 2. 実施内容
- factory_runner の実行履歴94件を調査 → 全件 changedFiles 0・summary「（出力なし）」・partial / max_runs_reached。
- claude / codex の CLI を実機ヘッドレステスト（`claude -p` / `codex exec`）→ 両方とも正常に "OK" 返却。CLI 自体は機能している。
- 真因特定: `lib/executors/claude.ts` / `codex.ts` の timeout が `?? 300_000`(5分)固定で、factory-runner が timeoutMs を渡していなかった。本格的な自走タスクが5分で打ち切られ、出力を出す前に kill されていた。
- 修正1: `lib/factory-runner.ts` に `executorTimeoutMs = Number(FACTORY_EXECUTOR_TIMEOUT_MS) || 1_500_000`(既定25分) を定義し、本実行の claude/codex run() 両方に付与。
- 修正2: `components/operations/AutoExecReport.tsx` に、成果が残らなかった回（partial/failed）へタイムアウト/エラーの理由バナーを表示。「やったこと」見出しに変更ファイル件数を併記。
- 実装は Codex に委譲、Claude が設計・検証（既定の分担運用）。

## 3. 変更ファイル
- `apps/ny01/progress/lib/factory-runner.ts` — executor timeout を env(既定25分)で設定可、本実行の claude/codex 呼び出しに timeoutMs 付与
- `apps/ny01/progress/components/operations/AutoExecReport.tsx` — 成果なし理由バナー＋変更ファイル件数表示

## 4. 検証結果
- `npx tsc --noEmit`: pass
- `npm run build`: pass
- `/guide` 200 / `/queue` 200、pm2 restart 済
- commit abb49df を origin main に push 済
- 機密スキャン: 該当なし
- 本実行テスト(auto, maxRuns 1, 25分タイムアウト): バックグラウンド実行中（別途結果確認）

## 5. 未対応
- 本実行テストの最終成果確認（成果ファイルが実際に残るか）はバックグラウンド完了待ち。
- maxRuns 3 でフルサイクル回すと最大75分かかる点の運用上の許容判断は未確定。

## 6. 危険ポイント
- timeout 延長により1回の自動実行が最大25分ブロックする。スケジュール実行やAPI呼び出しが長時間化する。
- 自動実行は executor が cwd(progress) 配下を自走編集する。25分の無監視自走中にコミットが走る可能性があるため、成果は事後レビュー前提。

## 7. 次にやるべきこと
- 本実行テスト結果を確認し、成果が残ればレポート表示(やったこと/変更ファイル件数)を実機確認。
- 成果が出ない場合は executor プロンプト or cwd の見直し。
- Codex 優先化ゴール（承認待ち）と合わせ、claude-first / codex-fallback の妥当性を再評価。

## 8. ChatGPT レビュー依頼文
```
progress アプリ(Next.js 16)の自動実行(AI工場 factory-runner)が長期間「成果ゼロ(（出力なし）)」だった件を修正しました。対象アプリ: progress / runId: 20260622-212859 / commit: abb49df。

真因は実行担当(claude/codex)の timeout が300秒固定で、本格的な自走タスクが5分で打ち切られていたこと。FACTORY_EXECUTOR_TIMEOUT_MS(既定25分)で延長可にし、レポートにも成果なし理由バナーを追加しました。

観点: (1)1回最大25分ブロックする運用上の妥当性(スケジュール/UIタイムアウト)、(2)maxRuns 3でフルサイクル最大75分の是非、(3)25分無監視自走でコミットが走る設計の安全性、(4)claude-first/codex-fallbackのままで良いか(Codex優先化すべきか)。
```
