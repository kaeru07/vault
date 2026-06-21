---
date: 2026-06-21
task: 自動実行RunにchangedFilesを確実に記録する
runId: 20260621-085829
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260621-091110]
commitHashes: [834a9c9]
---

# 2026-06-21 自動実行RunのchangedFiles記録を確実化

## 1. 作業目的
- 集中作業モードで active ゴール[high]「自動実行RunにchangedFilesを確実に記録する」(goal-mqluko5f-wbefy)に着手。自動実行レポートが薄い主因(実データ74runでchangedFiles=10件)を根治。

## 2. 実施内容
- 原因: `lib/executors/{claude,codex}.ts` は実行前後で `git status --porcelain` を取り差分していたが、executor が変更をコミットすると porcelain が clean になり changedFiles が空に。
- `lib/executors/shell.ts`: `gitHead(cwd)` と `changedFilesSince(cwd, beforeHead, beforeDirty)` を追加。後者は `git diff --name-only <beforeHead>..HEAD`(コミット済み) ＋ 実行後 porcelain(実行前 dirty 除外) を集約。
- `claude.ts` / `codex.ts`: 実行前に beforeHead/beforeDirty を取得、実行後 changedFilesSince で算出。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| lib/executors/shell.ts | gitHead/changedFilesSince 追加 |
| lib/executors/claude.ts / codex.ts | changedFilesSince で changedFiles 算出 |

## 4. 検証結果
- typecheck OK(Set spread→Array.from 修正) / build OK / lint OK / pm2 restart後 api200
- 既存挙動: dry_run の changedFiles:[] は不変、changedFilesIn は温存(後方互換)

## 5. 未対応
- 実 factory 自動実行での実地反映(次回スケジュール起動 11/14/16/23時で確認)

## 6. 危険ポイント
- executor 内の git 参照のみ。データ書き込み・破壊操作なし。

## 7. 次にやるべきこと
- 次回自動実行で changedFiles 記録を実地確認
- 関連: nextActions も埋める(別ゴール・対応済み)

## 8. ChatGPT レビュー依頼文
````text
対象: progress executor adapters
作業: 自動実行Runのchangedfiles記録確実化 / runId 20260621-085829 / commit 834a9c9

executorが変更をコミットするとgit status porcelainがcleanになりchangedFilesが
空になる問題を、実行前後のHEAD差分(コミット済み)＋未コミット集約で修正。
claude/codex両アダプタ適用。tsc/build/lint OK。

確認観点: HEAD差分＋porcelain集約の方式は妥当か / 実行前dirty除外の副作用は無いか。
````
