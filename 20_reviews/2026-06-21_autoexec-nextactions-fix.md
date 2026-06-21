---
date: 2026-06-21
task: 自動実行RunのnextActionsを必ず埋める
runId: 20260621-091110
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260621-085829]
commitHashes: [ed28d6c]
---

# 2026-06-21 自動実行RunのnextActionsを実出力から抽出

## 1. 作業目的
- 集中作業モードで active ゴール[medium]「自動実行RunのnextActionsを必ず埋める」(goal-mqluko5g-rt044)に着手。実データ74runでnextActions=1件しか無い問題を解消。

## 2. 実施内容
- 原因: `lib/executors/{claude,codex}.ts` の返り値で `nextActions: []` がハードコード。factory-runner は result.nextActions をそのまま ExecutionRun へ記録するため常に空。
- `lib/executors/shell.ts`: `parseNextActions(stdout)` 追加。「次にやること/次のアクション/Next steps/やり残し/TODO」見出し以降の箇条書き(- * ・ → 数字.)を抽出、見出しが無くても next/todo を含む箇条書き行を拾う。重複排除・各200字・最大5件。
- claude.ts / codex.ts: `nextActions: []` → `parseNextActions(r.stdout)`。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| lib/executors/shell.ts | parseNextActions(stdout) 追加 |
| lib/executors/claude.ts / codex.ts | nextActions を parseNextActions で算出 |

## 4. 検証結果
- 単体テスト4ケース(見出し+箇条書き / 番号付き / 見出し無しTODO / 空)で期待通り
- typecheck OK / build OK / lint OK / pm2 restart後 api200

## 5. 未対応
- 実 factory 自動実行での実地反映(次回スケジュール起動)

## 6. 危険ポイント
- 文字列パースのみ。副作用なし。

## 7. 次にやるべきこと
- 次回自動実行で nextActions 記録を実地確認
- nextActions が埋まると ゴール生成モード(未消化nextActions→ゴール化)の素材も充実

## 8. ChatGPT レビュー依頼文
````text
対象: progress executor adapters
作業: 自動実行RunのnextActions抽出 / runId 20260621-091110 / commit ed28d6c

nextActions:[]固定を廃止し、executor stdoutの「次にやること/Next steps/TODO」
見出し以降の箇条書きを最大5件抽出。単体テスト4ケース通過・tsc/build/lint OK。

確認観点: 見出し/箇条書きの抽出パターンは十分か / 誤抽出(無関係な箇条書き)のリスク。
````
