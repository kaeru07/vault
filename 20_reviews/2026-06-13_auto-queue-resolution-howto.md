---
date: 2026-06-13
task: 自動実行キューの候補外に「こうすれば動きます」解消手順＋ボタンを追加
runId: 20260613-174719
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260613-160139, 20260613-150148]
commitHashes: [f265460]
---

# 2026-06-13 自動実行キュー 候補外の解消手順表示

## 1. 作業目的
- ユーザー指摘:「（候補外と出るが）これどうすれば解消するかがわかりにくすぎる。改善して」「AI工場オペレーションセンター レビュー待ちのため、次回自動実行候補には入りません。」
- 目的: 候補外の作業に「どうすれば自動実行候補に入るか」の具体手順＋ボタンを表示し、次アクションを明確にする。

## 2. 実施内容
- `AutoQueueItem.resolution`（how / actionLabel / actionHref）を追加。
- `deriveResolution()` で status 別に解消手順を生成:
  - review_waiting / waiting_user → 「Inboxでレビュー/承認する」→ /decide
  - blocked → 「詳細を見る」→ /epic/:id（ブロック要因を表示）
  - ai_hold → 「保留解除」、manual(対象外) → 「対象に戻す」
- control API に `include`（factoryEligible=true へ復帰）アクションを追加、/queue に「対象に戻す」ボタン追加。
- 司令塔トップ「最優先指定中だが候補外」枠と /queue カードの両方に「👉 こうすれば動きます」手順＋遷移ボタンを表示。
- 運用ページ（operating-model 操作ボタン表・重要注意・変更履歴・frontmatter、guide FAQ）を更新。

## 3. 変更ファイル
types/auto-queue.ts / lib/auto-queue-score.ts / lib/auto-queue.ts / app/api/auto-queue/control/route.ts / app/page.tsx / app/queue/page.tsx / app/guide/page.tsx / docs/operations/current-operating-model.md

## 4. 検証結果
- tsc 0 / lint 0 / build 成功（49ページ）/ `/` `/queue` `/guide` 200。
- 実データ: pin済み「AI工場オペレーションセンター」(review_waiting) の resolution = how「Inboxのレビュータブで…『問題なし』にすると候補に入ります」/ ボタン「Inboxでレビューする → /decide」を確認。トップに「こうすれば動きます」描画を確認。
- 安全性: pin は gating 非上書きを維持（候補外のまま、ただし解消導線を提示）。

## 5. 未対応
- iPhone実機での解消ボタンのタップ導線・375px崩れ確認。
- executable 実item発生時の pin→next 反映の実機確認。

## 6. 危険ポイント
- include アクションは factoryEligible を true に戻すのみ（Epic化・自動実行開始はしない）。承認必須6点に非該当。
- 変更禁止範囲（旧queue/データ削除/自動実行の勝手な開始）不変更。

## 7. 次にやるべきこと
- iPhone実機確認。manualOrder stickiness（既知課題）は継続。

## 8. ChatGPT レビュー依頼文
```
対象: progress 自動実行キュー 候補外の解消手順表示（runId 20260613-174719 / commit f265460）
変更: types/auto-queue.ts, lib/auto-queue*.ts, app/api/auto-queue/control/route.ts, app/page.tsx, app/queue/page.tsx, app/guide/page.tsx, docs/operations/current-operating-model.md
背景: 「最優先にしたのにレビュー待ちで候補外、どう解消するか分からない」改善。候補外itemにstatus別の解消手順＋ボタンを表示。
観点: 1.status別の解消文言・遷移先(/decide,/epic,対象に戻す)は適切か 2.includeアクションでfactoryEligible=true復帰は安全か(Epic化しない) 3.手順表示でユーザーが迷わず解消に到達できるか 4.文言過多でカードが重くないか。
```
