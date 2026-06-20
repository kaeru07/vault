---
date: 2026-06-20
task: 自動実行レポートの中身を充実（何をしたかが分かるよう結果/対象/やったことを常時表示）
runId: 20260620-125504
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: []
commitHashes: [1f2c612]
---

# 2026-06-20 自動実行レポートの中身を充実

## 1. 作業目的
- ユーザー指摘「自動実行レポートが内容書いてなさすぎる。できたことに『ファイルの変更履歴なし』とかあるが、じゃあ何をしたか分かるようにして」。
- 各記事が changedFiles 依存で、空だと何をしたか伝わらない問題を解消する。

## 2. 実施内容
- 実データ調査: 74自動実行runのうち changedFiles=10件のみ。一方 summary=74全件 / stopReason=72 / selection(選定理由)あり。→ これらを活用していなかった。
- `describeOutcome(r)`: stopReason（blocked/no_candidate/epic_done/continue/approval_required/max_runs_reached/rate_limited/run_failed 等・接尾辞付き）を「この回が何をして、なぜ終わったか」の人間語に変換。無し時は runStatus でフォールバック。
- `didRealWork(r)`: 変更を伴う実作業をしたかの判定。
- RunArticle 刷新: ①結果ブロック（outcome+summary常時表示）②対象と選定理由（selection）③やったこと（changedFiles列挙、無ければ summary/outcome で説明・コード変更なしの回は理由付き明示）④できなかったこと ⑤検証 ⑥次にやること ⑦詳細レポート。
- `cleanRawReport`: `[factory-runner ...]`/`[factory-schedule ...]`/`executor=` の機械プレフィックス行も除去。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| progress/components/operations/AutoExecReport.tsx | 結果(stopReason人間語訳)/対象/やったことを常時表示に刷新 |

## 4. 検証結果
- typecheck: OK / build: OK / lint: OK
- 手動: pm2 restart後 /guide?tab=report 200。「やったこと」が25記事相当・「結果」ブロック・「この回はコードを変更する作業はしていません」・「起動条件を満たさず」描画を確認。
- 機密スキャン: OK / ob sync: n/a / git push: 1f2c612 pushed

## 5. 未対応
- factory-runner 側が changedFiles を十分記録しない根本問題（レポートは現状データで最大限説明する方針）。
- iPhone実機での読みやすさ確認。

## 6. 危険ポイント
- 表示専用の改修のみ。ロジック・データ書き込みなし。revert で旧表示に戻る。

## 7. 次にやるべきこと
- factory-runner の changedFiles 記録改善の検討。
- 記事に「対象Goal/Epicへのリンク」を付けるか検討。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: 自動実行レポートの中身を充実（結果/対象/やったことを常時表示）
runId: 20260620-125504 / commit: 1f2c612

## 目的
自動実行レポートの各記事がchangedFiles依存で、空だと「変更履歴なし」だけで
何をしたか分からない問題を解消。

## 実施
describeOutcome()でstopReasonを人間語(何をしてなぜ終わったか)に変換。summary
(全件存在)+selection(対象/選定理由)を常時表示。コード変更なしの回も理由付きで明示。

## 検証
tsc/build/lint OK。/guide?tab=report 実描画200で新セクション確認。

## 確認したい観点
1. 各記事が「何をしたか」を掴めるようになったか(過不足)。
2. stopReason→人間語の訳し分けに不自然・誤解は無いか。
3. changedFilesが薄い根本(factory-runner)を直すべきか、レポート側説明で十分か。
````
