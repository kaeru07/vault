---
date: 2026-06-20
task: ゴール承認のカテゴリサブタブ分割＋progress改善ゴール27件追加＋proposalSource保持バグ修正
runId: 20260620-135942
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260620-140003, 20260620-125523]
commitHashes: [3dbe800]
---

# 2026-06-20 ゴール承認のカテゴリサブタブ分割＋アプリ系改善ゴール追加

## 1. 作業目的
- ユーザー要望: progressアプリの運用・機能・ユーザー操作フローを深く見直し、改善ゴールをできるだけ多く承認候補に追加。ゴール承認欄を「試した方がいい系」「アプリ系」のカテゴリでサブタブ分割。

## 2. 実施内容
- カテゴリ分割: `lib/command-center.ts` に `proposalCategoryOf(source)`（research=試した方がいい系 / それ以外=アプリ系）を追加し、`InboxCard.proposalCategory` を付与。`components/newux/InboxTabs.tsx` の goalApproval に サブタブ（すべて / 試した方がいい系N / アプリ系N）を追加、選択カテゴリで絞り込み（client state `goalCat`）。
- アプリ系改善ゴール27件追加: コード精査で見えた実在の改善点を `/api/goals/propose`（source=app_improvement）で一括投入。例: 自動実行Runの changedFiles(10/74)/selection(5/74)/nextActions(1/74) 記録不足の解消、ゼロダウンタイムデプロイ（build中の_error.jsクラッシュ事故）、ゴール承認の一括承認/検索UI、stale自動修復、doneCriteria精度点検、iPhone表示点検、成功率/Executor統計可視化、危険語ゲート点検 など。
- バグ修正: `lib/goal-reader.ts` の `normalizeGoal` が `proposalSource`/`proposedAt`/`approvedAt`/`proposalEnables`/`proposalPros`/`proposalCons` を返しておらず、`readGoals→writeGoals` のたびに全ゴールから提案元が脱落していた（27件追加時の read で既存61件の research source が消失して発覚）。保持するよう修正。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| components/newux/InboxTabs.tsx | goalApprovalにカテゴリサブタブ追加・proposalCategoryで絞り込み |
| lib/command-center.ts | proposalCategoryOf追加・InboxCardにproposalCategory付与 |
| lib/goal-reader.ts | normalizeGoalがproposal系フィールドを保持（脱落バグ修正） |

## 4. 検証結果
- typecheck/build/lint: OK
- 手動: pm2 restart後 /decide?tab=goalApproval 200。サブタブ（試した方がいい系/アプリ系/すべて）・アプリ系ゴール描画。proposalSource分布 app_improvement=27 / research=61(修復後)。
- 機密スキャン: OK / ob sync: n/a / git push: 3dbe800 pushed

## 5. 未対応
- 大量承認候補の一括承認UI（改善ゴールとして登録済み）。iPhone実機確認。

## 6. 危険ポイント
- normalizeGoal修正は全ゴール読み込みに影響。既存フィールドは保持のみ追加で破壊なし。proposed追加は承認まで自動実行されない安全側。

## 7. 次にやるべきこと
- 一括承認/絞り込みUIの実装（承認ボトルネック解消）。
- normalize系の他フィールド脱落が無いか点検。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: ゴール承認のカテゴリサブタブ分割＋アプリ系改善27件追加＋proposalSource保持バグ修正
runId: 20260620-135942 / commit: 3dbe800

## 目的
ゴール承認を「試した方がいい系/アプリ系」で分け、progress改善ゴールを多数追加。

## 実施
proposalCategoryOf(research=try/他=app)でカード分類しサブタブ化。改善ゴール27件を
source=app_improvementで投入。normalizeGoalがproposal系を落とすバグ(read→writeで
source消失)を修正。

## 検証
tsc/build/lint OK。/decide?tab=goalApproval 実描画200でサブタブ・アプリ系ゴール確認。

## 確認したい観点
1. try/appの2分類で十分か(さらに細分類が要るか)。
2. 追加した27のアプリ改善ゴールの粒度・優先度は妥当か。
3. normalizeGoalのフィールド保持漏れが他に無いか。
````
