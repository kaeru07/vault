---
date: 2026-06-29
task: 自動実行レポートを人間語の記録文に(機械トークン除去)
runId: 20260629-232302
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: []
commitHashes: [e85bd94]
---

# 自動実行レポートの人間語化

## 1. 作業目的
ユーザー指摘（繰り返し要望）:「自動実行レポートに block0 等の機械語が出て分からない。やったことを人間語で記事/記録のように書いて」。

## 2. 実施内容
- 原因: 定時実行系の run は `summary` 自体が機械文（例「Factory 起動: Review Fix 実行0・予約0・skip0・block1 / Epic 0 Run / Prompt Queue 実行0・予約0・skip0・block0 / 停止理由=no_candidate」）で、それが「やったこと」に表示されていた。
- `humanizeAutoSummary(run)` を追加し機械サマリーを人間語の記録文へ変換:
  - 何も実行なし/no_candidate → 「今回は自動実行できる作業がなかったため、何も実行せずに待機しました。」
  - review-fix → 「修正候補をN件確認し…（実行0かつblockなら見送り）」
  - monetization-sync/Vault走査 → 「収益化候補をVaultから点検しました（N件確認・新規X件/更新Y件）。」
  - 実行あり → 「自動でN件の作業を進めました。」
- `stripMachineTokens(s)` を表示用テキスト（やったこと・outcome）に適用し、残留トークン（block\d / executed= / blocked= / stopped= / no_candidate / key=value / [prefix]）を最終除去。
- 「詳細レポート全文を読む」の生ログは情報保全のため原文維持（既定では二重折りたたみで非表示）。

## 3. 変更ファイル
- `progress/components/operations/AutoExecReport.tsx`（humanizeAutoSummary + stripMachineTokens, commit e85bd94）

## 4. 検証結果
- tsc0 / build0。
- /report 200・人間向け本文から block0/block1/runs=0/tagged= が消滅（0回）・「修正候補を…」等の人間語記録文を確認・error0。
- 既定非表示の生ログ全文には review-fix 由来の executed=/blocked= 等が残る（情報保全用・最深部）。
- 表示変換のみ。ExecutionRun 生成側・保存データは不変。data/real 手編集なし。

## 5. 未対応
- iPhone実機で記録文として読みやすいか確認。
- （任意）生ログ全文も人間語化したい場合は別途（現状は debug 用に原文維持）。

## 6. 危険ポイント
- 表示変換のみで生成・保存ロジックは不変。情報損失なし（全文は維持）。

## 7. 次にやるべきこと
- iPhone実機確認。残トークンが気になる場合は生ログ全文にも stripMachineTokens を適用する判断。

## 8. ChatGPTレビュー依頼文
```
対象アプリ: progress / runId: 20260629-232302 / commit: e85bd94
GitHub: kaeru07/ny01

自動実行レポート(/report)で定時実行の機械文(block0/executed=0/no_candidate等)を人間語の記録文に変換しました(humanizeAutoSummary+stripMachineTokens)。生ログ全文は情報保全で維持。

観点:
1. 記録文が「何をして何ができたか」を人間語で十分伝えているか
2. 変換ルールの抜け(機械トークンが本文に残る経路)がないか
3. 生ログ全文を残す設計(debug用)は妥当か、それとも全部人間語にすべきか
```
