---
date: 2026-06-13
task: 自動実行キューのpin反映不具合を修正＋運用説明追加（Codex実装→Fableレビュー）
runId: 20260613-160139
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260613-150148, 20260613-125025]
commitHashes: [3a4ff67]
---

# 2026-06-13 自動実行キュー pin反映不具合の修正＋運用説明

> Codex(前スレッド継続)が修正実装、Fableがレビュー・検証・データ復旧・コミット。

## 1. 作業目的
- ユーザー報告: /queue で「AI工場オペレーションセンター」に pin したのに、司令塔トップの「次」は「BirdLog」のままで反映されないように見える。
- 目的: pin/保留/対象外/上下の操作結果がトップに分かりやすく反映され、候補外なら理由を表示する。運用ページに使い方を追加。

## 2. 原因（切り分け）
- **pin保存自体は機能していた。** 原因は複合:
  1. pin済みでも status が `review_waiting`（最新runがレビュー待ち）のため候補外＝これは安全仕様として正しい。だがUIが「pin済みだが候補外」を説明していなかった。
  2. 司令塔トップに旧 `factoryOutlook` 由来の「次/その次」が残り、`buildAutoQueue()` と食い違って見える余地があった。
  3. トップ/queueがforce-dynamic/revalidate不足で古い表示になりうる。
- → 「仕様として候補外」＋「説明不足・旧表示残存というバグ」の複合。

## 3. 実施内容（修正）
- トップと /queue を同一の `getAutoQueueView()` 由来に統一し、旧 factoryOutlook の次/その次表示を撤去。
- `/api/auto-queue/control` と goals priority API で `revalidatePath('/')`・`('/queue')`、関連ページを `force-dynamic`。
- `AutoQueueView.pinnedExcluded` と 各itemの `candidateEligible` / `candidateBlockedReason` を追加。
- 司令塔トップに「最優先指定中だが候補外」枠を新設。/queue カードに 候補入否 / 候補外理由 / queueScore / pin済み候補外警告 を追加。
- 保留操作をレビュー状態より先に `ai_hold` 導出（ユーザー操作優先）。
- 「最優先」ボタンを「自動実行を最優先 / 復帰時に最優先」に分岐（pin は gating 非上書きを維持）。
- 運用ページ（operating-model / guide）に「自動実行キューの使い方」=派生ビュー説明・status意味・操作ボタン意味・安全注意を追加。

## 4. 変更ファイル
types/auto-queue.ts / lib/auto-queue.ts / lib/auto-queue-score.ts / app/api/auto-queue/{route,control/route}.ts / app/api/goals/[goalId]/priority/route.ts / app/page.tsx / app/queue/{page,QueueActionButton}.tsx / app/guide/page.tsx / docs/operations/current-operating-model.md

## 5. 検証結果（Fableが独立実行）
- tsc 0 / lint 0 / build 成功（49ページ） / `/` `/queue` `/guide` すべて 200。
- 実データ: GET /api/auto-queue の `pinnedExcluded` に「AI工場オペレーションセンター（review_waiting・candidateEligible=false・理由=レビュー待ち）」を確認。司令塔トップに「最優先指定中だが候補外」枠が描画されることを確認。
- 安全性: ユーザーがpinした項目は review_waiting のままで executable/next にならない＝**pinはgatingを上書きしない**を再確認。
- revalidatePath('/')・('/queue') と force-dynamic で操作後にトップ/queueが同じ派生結果で再表示される。

## 6. 危険ポイント / データ衛生
- **Codexのcontrol APIテストで実 epics.json に hold=true の副作用**が残っていた（BirdLog / progress-todo、updatedAt≈15:12 JST）。Fableが `unhold` で復旧し waiting_user へ戻した。ユーザー実pin（AI工場オペレーションセンター）は保持。
- epics.json は実データのため commit 対象外（コード/docのみ commit）。
- 変更禁止範囲（旧queue削除・自動実行の勝手な開始・認証/課金/外部公開）は不変更。/legacy/queue 維持。

## 7. 次にやるべきこと
- iPhone実機で「最優先指定中だが候補外」枠・候補外理由・375px崩れを確認。
- executable 実item発生時に pin→next 反映を実機確認。
- manualOrder stickiness 改善（前run 20260613-150148 の指摘）は継続課題。

## 8. ChatGPT レビュー依頼文
```
対象: progress 自動実行キュー pin反映不具合修正（runId 20260613-160139 / commit 3a4ff67）
新/変更: types/auto-queue.ts, lib/auto-queue*.ts, app/api/auto-queue/*, app/api/goals/[goalId]/priority/route.ts,
  app/page.tsx, app/queue/*, app/guide/page.tsx, docs/operations/current-operating-model.md
背景: /queueでpinしてもトップの次に反映されないように見えた。原因は「pin済み候補外の未説明＋旧factoryOutlook残存＋revalidate不足」。
観点:
1. pinはgatingを上書きしない仕様（pin済みでもwaiting_user/review_waiting/blockedは候補外）で、ユーザーの『最優先にしたのに動かない』不満を「最優先指定中だが候補外」表示で十分に解消できるか。
2. 保留をレビュー状態より先にai_hold導出する変更の副作用はないか。
3. force-dynamic+revalidatePathの範囲は適切か（過剰な再計算コスト）。
4. control APIテストが実データを汚した運用上の問題（テストは一時データ/ドライランで行うべきか）。
```
