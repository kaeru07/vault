---
date: 2026-06-20
task: 日々調査のゴール承認候補を一括追加（58件）＋一括追加エンドポイント新設
runId: 20260620-125523
targetApp: progress
monetizationImpact: medium
theme: [market-research, app-strategy, workflow]
relatedRunIds: [20260620-073504]
commitHashes: [1f2c612]
---

# 2026-06-20 日々調査のゴール承認候補を一括追加（58件）

## 1. 作業目的
- ユーザー要望「日々調査のゴール承認をできるだけ多く追加してほしい」。
- 既存の自動提案は承認待ち上限3・maxDays3・max3で絞られ、調査に★4以上のユニークツールが61件あっても3件しか入らない。

## 2. 実施内容
- `app/api/goals/propose-research/route.ts` 新設: `buildResearchGoalCandidates({maxDays,max})` + `proposeGoals(source=research)` を**承認待ち上限を無視**して呼ぶ手動トリガ。既定 maxDays=60 / max=100。既存ゴール（全status）同名は除外（承認/却下済みを蒸し返さない）。
- 実行: `POST /api/goals/propose-research {maxDays:60,max:100}` → createdCount=58 / skipped=0。proposed 3→61件。
- 候補はAIツールを「試す/調査する」系（例: Claude Codeを試す / OpenAI Codex SDKを試す / Figma MCP Serverを試す …）。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| progress/app/api/goals/propose-research/route.ts | 新規: 調査ゴール候補を上限無視で一括proposeする手動トリガAPI |

（データ操作: goals.json に proposed を58件追加。コミット対象外の live データ）

## 4. 検証結果
- typecheck: OK / build: OK / lint: OK
- 手動: /decide?tab=goalApproval 200で新候補が承認待ちに描画。goals.json proposed=61確認。
- 機密スキャン: OK / ob sync: n/a / git push: 1f2c612 pushed

## 5. 未対応
- 大量の承認候補をUIで一括承認/絞り込みする導線（現状1件ずつ）。

## 6. 危険ポイント
- proposed は承認するまで自動実行されない安全側。proposed=61で自動実行の調査/アイドル提案（上限3）は承認で減るまで止まる（設計通り）。

## 7. 次にやるべきこと
- 承認候補の一括承認/絞り込みUIの検討。
- 承認したゴールが自動実行で消化されるか観察。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: 日々調査のゴール承認候補を一括追加(58件)+一括追加API新設
runId: 20260620-125523 / commit: 1f2c612

## 目的
「日々調査のゴール承認をできるだけ多く追加」要望。上限3で3件しか入らなかったのを
全期間の調査★4以上から一括proposeできるようにした。

## 実施
/api/goals/propose-research 新設(上限無視・既存同名除外)。実行で58件追加(proposed3→61)。

## 検証
tsc/build/lint OK。/decide?tab=goalApproval 実描画200で新候補確認。

## 確認したい観点
1. proposedを61件まで積む運用は妥当か(承認の手間 vs 候補の豊富さ)。
2. 自動提案(上限3)は据え置き・手動トリガで大量追加、の切り分けで良いか。
3. 大量承認候補の一括承認UIを用意すべきか。
````
