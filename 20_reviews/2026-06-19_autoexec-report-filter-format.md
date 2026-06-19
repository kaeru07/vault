---
date: 2026-06-19
task: 自動実行レポートに期間/状態フィルタ＋詳細レポートの整形を追加
runId: 20260619-232121
targetApp: ny01/progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260619-225715]
commitHashes: [196035d]
---

# 2026-06-19 自動実行レポート：フィルタ＋詳細整形

> ユーザー指示「フィルタと整形やって」。

---

## 1. 作業目的

- 自動実行レポートに期間・状態のフィルタを付ける。
- 詳細レポート全文の機械的なメタ情報を整形して読みやすくする。

---

## 2. 実施内容

- **フィルタ**: `AutoExecReport` に期間（7日 / 30日=既定 / 全期間）＋状態（すべて / 完了 / 一部完了 / 失敗）のチップ（Link）を追加。`/guide` から `?range=` `?status=` を渡して URL 同期。該当件数を表示。0件時はガイド文。
- **整形**: `cleanRawReport()` を新設し、rawReport から「progressレビュー用」ブロック以降と `monetizationImpact / theme / obsidianSummary / obsidianSaveTarget / reviewStatus / runId / changedFiles ...` 等の機械メタ行を除去。連続改行で段落分割して `<p>` 表示（旧 `<pre>` ベタ貼りを廃止）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/components/operations/AutoExecReport.tsx | 期間/状態フィルタ(chip+URL同期)・cleanRawReportで詳細整形 |
| progress/app/guide/page.tsx | AutoExecReportにrange/statusをsearchParamsから渡す |
| progress/docs/operations/current-operating-model.md | frontmatter/変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0） / build: OK（next build 0）
- 手動: `/guide?tab=report` にフィルタ描画（期間7/30/全・状態すべて/完了/一部/失敗・該当件数）。`?range=7`・`?status=failed` 200。詳細レポートに `progressレビュー用`/`monetizationImpact`/`obsidianSaveTarget` が残らないこと（0件）を確認。
- ob sync: n/a / git push: 196035d

---

## 5. 未対応

- フィルタは期間×状態のみ（Goal/Epic別フィルタは未）。
- cleanRawReport は固定キーの除去（網羅的でない可能性）。
- 実機での操作・読みやすさ最終確認は未。

---

## 6. 危険ポイント

- 影響: 低。表示専用 server コンポーネント＋guide のprops受け渡しのみ。
- 既定が30日になったため、30日より前の実行はフィルタを「全期間」にしないと出ない（仕様）。
- ロールバック: commit 196035d の revert。

---

## 7. 次にやるべきこと

- 必要なら Goal/Epic 別フィルタ、cleanRawReport の除去パターン拡充。
- 実機確認。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: 自動実行レポートに期間/状態フィルタ＋詳細レポートの整形
runId: 20260619-232121
日付: 2026-06-19
GitHub commit: 196035d

## 実施内容
- AutoExecReportに期間(7/30既定/全)＋状態(すべて/完了/一部/失敗)フィルタ(chip+?range=?status=URL同期・件数表示)。
- cleanRawReportでrawReportのprogressレビュー用ブロック以降と機械メタ行(monetizationImpact等)を除去し段落表示に整形。

## 検証
- tsc0/build0/フィルタ描画+range7/status=failed 200/機械メタ除去確認。

## 未対応
- Goal/Epic別フィルタ未・cleanRawReportは固定キー除去・実機確認未。

## 確認したい観点
- 既定30日の妥当性
- cleanRawReportの除去対象の網羅性(消しすぎ/残りすぎ)
- フィルタの軸(期間/状態)で十分か
````

---

## 関連

- progress runId: 20260619-232121（正本）
- 関連: components/operations/AutoExecReport.tsx / [[2026-06-19_autoexec-report-per-run]]
- 関連アプリ: [[../02_apps/progress]]
