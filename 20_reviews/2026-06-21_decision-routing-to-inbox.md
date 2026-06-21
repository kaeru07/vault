---
date: 2026-06-21
task: 今日の判断に判断系が出ない問題の点検と是正(ルーティング＋表示)
runId: 20260621-121117
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260621-101304, 20260621-095414]
commitHashes: [39d85b1]
---

# 2026-06-21 今日の判断に判断系が出ない問題の点検＋是正

## 1. 作業目的
- ユーザー指摘「消すか決める等の判断系がゴール承認に行き、今日の判断に判断系が追加されてない気がする。妥当か点検して」。

## 2. 点検結果（原因2つ）
- **今日の判断(decisions)の元は approvals.json**(危険/方針選択/検収)。pending 19件中 **16件が danger 系**(billing/external_publish/secret の「Run一次レビュー」)。
- `decisions = stopFactors.slice(0,3)` ＋ danger 優先 order → danger 16件が先頭3枠を占有し、**方針選択(direction)・人間作業が表示されない**。
- 自分の【要判断】は **proposed ゴールで作成→ゴール承認タブへ**(誤ルーティング)。1件(残りの旧画面)は active 承認されゴール化(自動実行対象として誤モデル化)。→ ユーザー指摘は妥当。

## 3. 是正内容
1. **ルーティング修正**: 判断系は `POST /api/operations/approvals/generate`(category=multi_option=方針選択)で今日の判断へ。【要判断】2件(互換データ廃止/残りの旧画面)を方針選択 approval として作成(appr-1782006446950 / appr-1782006447285)。
2. **ゴール版2件を dropped**(setGoalApproval false)。
3. **表示修正**(lib/command-center.ts): decisions 選択を danger→direction→human_task の**ラウンドロビン**に変更し各種別最低1枠出るように。

## 4. 検証結果
- typecheck/build OK / pm2 restart後 /decide 200で「互換データ」「残りの旧運用画面」「判断してください」(方針選択カード)が今日の判断に表示。
- git push: 39d85b1（並行作業の loopHealth TERMS 1行も同梱・部分ステージ不可のため）

## 5. 未対応（ユーザー判断）
- 今日の判断に滞留する旧「Run一次レビュー」danger 16件の整理（古い可能性・要トリアージ）。

## 6. 危険ポイント
- decisions 表示順の変更のみ（danger を消さず枠配分を調整）。安全判断の urgency は維持。

## 7. 次にやるべきこと（運用ルール化）
- 今後、判断系(消す/方針選択)は approvals/generate(方針選択)で**今日の判断**へ。新ゴールの是非だけ**ゴール承認**(proposed)へ。memory に記録済み。
- 旧 danger 16件のトリアージをユーザーと相談。

## 8. ChatGPT レビュー依頼文
````text
対象: progress 今日の判断(Inbox decisions)
runId: 20260621-121117 / commit 39d85b1
判断系をproposedゴール(ゴール承認)に入れていた誤りを是正し、approvals/generate
(方針選択)で今日の判断へ。decisions選択をdanger一色にならないラウンドロビンに変更。
旧Run一次レビューdanger16件が今日の判断を占有している点は要トリアージ。
確認観点: 種別ラウンドロビンで危険判断の urgency が損なわれないか / 旧danger16件は本来
今日の判断(decisions)でなくレビュー扱いにすべきか。
````
