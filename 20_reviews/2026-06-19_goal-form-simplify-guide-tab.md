---
date: 2026-06-19
task: ゴール直接追加フォームを簡素化(タイトルのみ必須)＋運用ページを主要タブへ昇格
runId: 20260619-071433
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260619-000632]
commitHashes: [4e1a3c8]
---

# 2026-06-19 ゴール直接追加の簡素化＋運用タブ昇格

> ユーザー指示: タイトルだけ必須／プロジェクトは未設定でも良い／status・decisionPolicy・riskFlag・指標は消す／プロンプトは任意。あと全体のタブに運用ページを追加。

---

## 1. 作業目的

- ゴールの直接追加を、タイトルだけで追加できる最小フォームにする。
- 運用ページ(/guide)を主要タブから辿れるようにする。

---

## 2. 実施内容

- `components/goals/GoalPlannerForm.tsx` の「ゴールを直接追加」を **タイトル(必須) / 案件(任意) / 説明=prompt(任意)** の3項目に簡素化。priority/status/decisionPolicy/riskFlags/metric/今(current)/目標(target)/notes の入力欄・state・submit body・リセットを削除。未指定値は `upsertSingleGoal` の既定（status=active / priority=medium / autonomous / target100・current0）で補完。
- `components/navigation/BottomNav.tsx`: 運用(`/guide`)を主要アイコンタブに追加（6タブ化）、moreItems の重複を除外。
- `components/navigation/TopNav.tsx`: 新ナビに「運用」追加、`/guide` を `NEW_ROUTES` に追加（/guide で新ナビが有効）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/components/goals/GoalPlannerForm.tsx | 直接追加を3項目に簡素化 |
| progress/components/navigation/BottomNav.tsx | 運用を主要タブに昇格・moreItems重複除外 |
| progress/components/navigation/TopNav.tsx | 新ナビに運用追加・NEW_ROUTES更新 |
| progress/app/guide/page.tsx | FAQ更新 |
| progress/docs/operations/current-operating-model.md | 画面構成6タブ化・frontmatter・変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0） / build: OK（next build 0）
- 手動: `/goal-planner` 200 で「タイトル *」「案件(任意)」「説明(任意)」のみ描画、decisionPolicy/riskFlags/指標は非表示。`/guide`・ホーム 200 で「運用」タブ描画。
- タイトルのみ直接追加 API E2E: `upsertSingle({title})` → status=active / priority=medium（既定補完）を実測。テストゴール削除。
- ob sync: n/a / git push: 4e1a3c8

---

## 5. 未対応

- 進捗(今/目標)は直接追加フォームから外したため既定(0/100=0%)で作成される。後から数値を編集する導線は未整備（JSON追加や将来の編集UIで対応可）。
- 実機での運用タブ到達・直接追加操作の通し確認は未（API/curlで代替）。

---

## 6. 危険ポイント

- 影響: 低。フォーム項目削減＋ナビにタブ1つ追加のみ。upsertSingleGoal の既定補完で後方互換（送られない項目は既定値）。
- ロールバック: commit 4e1a3c8 の revert。

---

## 7. 次にやるべきこと

- 直接追加ゴールの進捗(今/目標)を後から編集する導線の検討。
- 実機確認。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: ゴール直接追加フォームを簡素化(タイトルのみ必須)＋運用ページを主要タブへ昇格
runId: 20260619-071433
日付: 2026-06-19
GitHub commit: 4e1a3c8

## 実施内容
- ゴール直接追加を タイトル(必須)/案件(任意)/説明(任意) の3項目に簡素化(priority/status/decisionPolicy/riskFlags/metric/今/目標/notes削除・既定補完)。
- 運用ページ(/guide)をBottomNav主要タブ(6タブ化)とTopNav新ナビへ昇格。

## 検証
- tsc0/build0。/goal-planner 200(3項目のみ)、運用タブ描画。タイトルのみ追加E2E(既定補完)。

## 未対応
- 進捗(今/目標)は既定0/100で作成(後から編集する導線は未)。実機確認は未。

## 確認したい観点
- 直接追加を3項目に絞った設計の妥当性(必要な既定値の補完)
- 進捗を後から編集する導線の要否
- 運用タブを主要タブに昇格した判断(6タブの収まり)
````

---

## 関連

- progress runId: 20260619-071433（正本）
- 関連: components/goals/GoalPlannerForm.tsx / components/navigation/BottomNav.tsx / [[2026-06-19_manual-goal-add]]
- 関連アプリ: [[../02_apps/progress]]
