---
date: 2026-06-21
task: ゴール達成率ダッシュボードを作る
runId: 20260621-094533
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: []
commitHashes: [4c5b09b]
---

# 2026-06-21 ゴール達成率ダッシュボード

## 1. 作業目的
- 集中作業モードで active[low]ゴール「ゴール達成率ダッシュボードを作る」(goal-mqluko5k-q6n7p)に着手。16 active＋多数 proposed の全体像が無い問題を解消。

## 2. 実施内容
- `app/goal-dashboard/page.tsx` 新設(server component): 状態内訳カード6種(実行中/承認待ち/後で/完了/取りやめ/保管・人間語)、実行中の平均達成率、実行中ゴールの達成率バー(`goalAchievement`・**進んでいない順**・%で色分け)、承認待ちのカテゴリ内訳(試す/アプリ)。`goalAchievement` / `proposalCategoryOf` 再利用。iPhone向け grid。
- `lib/nav-menu.ts`「計画・候補」に `/goal-dashboard` 追加(☰・/legacy 到達)。
- goal-planner(per-goal管理・proposed除外)とは非重複の集計ビュー。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| app/goal-dashboard/page.tsx | 新規: 状態内訳＋達成率の俯瞰 |
| lib/nav-menu.ts | /goal-dashboard 追加 |

## 4. 検証結果
- typecheck/build/lint OK / pm2 restart後 /goal-dashboard 200・全セクション描画・/legacy にnav掲載
- 機密スキャン: n/a(表示のみ) / git push: 4c5b09b

## 5. 未対応
- 達成率が伸びない実行中ゴールへの着手は別途

## 6. 危険ポイント
- 読み取り専用ページ。データ書き込みなし。

## 7. 次にやるべきこと
- ダッシュボードで「進んでいない目標」を見て次の着手を決める

## 8. ChatGPT レビュー依頼文
````text
対象: progress /goal-dashboard
作業: ゴール達成率ダッシュボード新設 / runId 20260621-094533 / commit 4c5b09b
状態内訳＋実行中ゴールの達成率(進んでいない順)を俯瞰。goal-plannerと非重複。
tsc/build/lint OK・実描画200。確認観点: 指標(current/target)の見せ方は十分か。
````
