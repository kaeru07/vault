---
date: 2026-06-18
task: ゴール進捗0%問題を修正（進捗率を「今/目標」target/currentに統一）
runId: 20260618-085742
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260618-012023]
commitHashes: [851b7c5]
---

# 2026-06-18 ゴール進捗0%問題の修正

> 調査(2026-06-18_goal-progress-zero-investigation / runId 20260618-012023)で特定した原因の修正。
> ユーザー選択「一番単純: 今/目標の数字で出す」に基づく。

---

## 1. 作業目的

- 自動実行(/queue)・目標タブの全ゴール進捗が 0% になる不具合を直す。
- ユーザーが設定した「今/目標」(target/current) の数字をそのまま % に出す。

---

## 2. 実施内容

- 進捗率の正本を `goalAchievement(goal)`（target>0 → `round(min(100, current/target*100))`、target無 → Todo完了率）に統一。
- `buildGoalProgress`（lib/auto-queue.ts・/queue と目標タブの進捗源）を変更:
  - `ratio = goalAchievement(goal)`
  - 表示 `done/total` も target ありなら `current/target`、無ければ `doneTodo/totalTodo`（％と一致）
  - 旧 `itemTotal/itemDone`（キューアイテム数ベース）を廃止
- `buildGoalProgressCards`（lib/command-center.ts・司令塔カード／現状未使用）も同方針に統一。
- 旧フォールバック（Todo→紐付Epic平均→数値指標）を廃止し、「作業の進み具合の平均」は進捗率に使わない（ユーザー選択どおり）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/auto-queue.ts | buildGoalProgress: ratio=goalAchievement、done/total=current/target |
| progress/lib/command-center.ts | buildGoalProgressCards: achievementPct=goalAchievementに統一・basis簡素化 |
| progress/app/guide/page.tsx | FAQ「Goal進捗の%は何を見ている?」を新方式に更新 |
| progress/docs/operations/current-operating-model.md | Goal進捗節・frontmatter・変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit 0）
- build: OK（next build 0）
- 手動確認: pm2 restart 後 `/api/auto-queue` の goalProgress を実測 → **100%(60/15) / 13%(10/80) / 33%(30/90) / 0%(0/5)**（修正前は全て0%）。`/queue`・`/goal-planner` 実描画200・白画面なし。
- 機密パターン事前チェック: OK
- ob sync: n/a（ob sync 一旦中止中）
- git push: 851b7c5（origin/main へ push 済み）

---

## 5. 未対応

- `goal-ai-factory-os` は current60 > target15（上限で100%表示）。target/current のスケール是正はデータ運用判断のため未対応。
- 進捗計算の完全な単一ヘルパ化（goalAchievement を唯一の正本関数として全箇所参照）は未実施（buildGoalProgress / buildGoalProgressCards 双方を goalAchievement 呼びに統一済みで実害なし）。

---

## 6. 危険ポイント

- 既存機能への影響: 低。進捗率の算出方法のみ変更（表示専用）。キュー順・Factory実行・スコアには非干渉（ratio/done/total は表示フィールド）。
- ロールバック: commit 851b7c5 の revert。
- 観察: target/current を持たないゴールが今後増えた場合、Todo完了率にフォールバックする挙動が期待どおりか。

---

## 7. 次にやるべきこと

- goal-ai-factory-os の target/current スケール是正（current 60 / target 15 の意味づけ確認）。
- 必要なら進捗計算を単一ヘルパに完全集約。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: ゴール進捗0%問題を修正（進捗率を「今/目標」target/currentに統一）
runId: 20260618-085742
日付: 2026-06-18
GitHub commit: 851b7c5

## 原因
- buildGoalProgress が ratio=done/total(todos＋キューアイテム数のみ)で算出し goal.target/current を無視。全ゴール todos=0・数値指標管理のため done≒0→全ゴール0%。

## 修正
- 進捗率の正本を goalAchievement(target>0→round(min(100,current/target*100))、target無→Todo完了率)に統一。
- buildGoalProgress(/queue・目標タブ)と buildGoalProgressCards(司令塔)を変更。表示done/totalもtargetありならcurrent/target。旧フォールバック(Todo→紐付Epic平均→数値指標)廃止。

## 検証
- tsc0/build0。/api/auto-queueで 100%(60/15)/13%(10/80)/33%(30/90)/0%(0/5) を実測(旧 全0%)。/queue /goal-planner 実描画200。

## 未対応
- goal-ai-factory-os は current60>target15(上限100%)でスケール要見直し(データ運用)。

## 確認したい観点
- 進捗の正本を「今/目標」に固定する設計の妥当性(作業進捗を進捗率に使わない判断)
- target未設定ゴールのTodo完了率フォールバックの妥当性
- done/total表示とratioの整合
- 進捗計算を単一ヘルパに集約すべきか
````

---

## 関連

- progress runId: 20260618-085742（正本）
- 関連調査: 20260618-012023（[[2026-06-18_goal-progress-zero-investigation]]）
- 関連: lib/auto-queue.ts buildGoalProgress / lib/goal-reader.ts goalAchievement
- 関連アプリ: [[../02_apps/progress]]
