---
date: 2026-06-20
task: 試した方がいい系ゴールのトリアージ（★5に理由追記・★4以下を後でへ）＋triageエンドポイント新設
runId: 20260620-140003
targetApp: progress
monetizationImpact: medium
theme: [market-research, app-strategy, workflow]
relatedRunIds: [20260620-135942]
commitHashes: [3dbe800]
---

# 2026-06-20 試した方がいい系ゴールのトリアージ

## 1. 作業目的
- ユーザー要望: 試した方がいい系で、特に試した方がよさそうなものは理由を追記、あとででも良さそうなものは「後で」扱いに変更。

## 2. 実施内容
- `app/api/goals/triage-research/route.ts` 新設（手動トリガ）。proposed かつ research系（source=research / タイトル「〜を試す/調査する」/ notes「AIツール調査・導入価値評価」）を対象に:
  - ★5（priority high）=「特に試した方がいい」→ 承認待ち維持＋notesへ理由を追記（`⭐特に試した方がいい: 調査で最高評価(★5・cue)。…今のClaude/Codex/MCP中心の開発・自動実行に直結…`、重複追記なし）。
  - ★4以下=「あとででも良い」→ status=paused（後で・保留）へ変更。
  - あわせて剥がれていた proposalSource を research に修復。
- 実行結果: enriched=16 / movedLater=45 / repairedSource=61。
- 「後で(paused)」の意味: 承認待ち一覧と自動実行対象から外れるが削除しない。目標タブ（/goal-planner）で確認・再有効化できる。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| app/api/goals/triage-research/route.ts | 新規: 試した方がいい系を★5=理由追記/★4=後で(paused)へトリアージ＋source修復 |

（データ操作: research系proposed 16件にnotes追記・45件をpausedへ・61件のsource修復）

## 4. 検証結果
- typecheck/build/lint: OK
- 手動: /decide?tab=goalApproval 200。試した方がいい系=16件・各カードに理由文描画。paused=45。
- 機密スキャン: OK / ob sync: n/a / git push: 3dbe800 pushed

## 5. 未対応
- 「後で(paused)」を承認画面内の「あとで」サブ状態として可視化する案（現状は目標タブで確認）。
- ★5/★4の基準の妥当性はユーザー確認。

## 6. 危険ポイント
- paused は削除でなく保留。再有効化可。提案系の変更で自動実行への直接影響なし。

## 7. 次にやるべきこと
- 「後で」候補の見える化UI。
- トリアージ基準（★5/★4）の調整。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: 試した方がいい系ゴールのトリアージ(★5理由追記/★4以下を後でへ)
runId: 20260620-140003 / commit: 3dbe800

## 目的
試した方がいい系の承認候補を、特に試すべき(★5)は理由付きで残し、あとででも良い
(★4以下)は「後で(paused)」へ整理。

## 実施
/api/goals/triage-research 新設。★5=承認待ち維持+理由追記、★4以下=paused。
source剥がれも修復。実行でenriched16/movedLater45/repairedSource61。

## 検証
tsc/build/lint OK。/decide?tab=goalApproval 実描画200で理由描画確認。

## 確認したい観点
1. ★5/★4を「特に/あとで」の境界にするのは妥当か。
2. 「後で」をpaused(目標タブ送り)にする運用で良いか、承認画面に「あとで」タブが要るか。
3. 追記した理由文(テンプレ寄り)の質は十分か。
````
