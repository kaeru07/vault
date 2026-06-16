---
date: 2026-06-17
task: 自動実行キューをGoal順で並べる（表示＋実行）
runId: 20260617-010618
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260616-225004, 20260615-212004]
commitHashes: [4bd93f9]
---

# 2026-06-17 自動実行キューをGoal順で並べる（表示＋実行）

> 1 作業 = 1 ファイルの自動生成レビュー。

---

## 1. 作業目的

- なぜ: ユーザー要望「自動実行キューにGoalを表示してほしい。キューはGoalに紐づくものだから、Goalの順番でキュー順も変わるイメージ」。
- 背景: 従来のキューはGoal横断のフラットなスコア順で、Goalは score への boost 程度。各カードに `Goal ○○` を小さく表示するのみ。表示(`buildAutoQueue`/`compareItems`/`/queue` UI)と実行(`scanFactoryDispatch`)は別ロジックだった。
- 期待効果: Goalがキュー順を決める第一キーになり、目標タブでGoalを並べ替えると次にAIが実行する作業も変わる。Goal単位でまとまって見える。

## 2. 実施内容

- スコープ確認: ユーザーに「表示だけ」か「表示＋実行」かを確認 →「表示＋実行の両方」を選択。
- 共有ヘルパ `rankGoals(goals)` / `goalRankOf()` を `lib/goal-reader.ts` に新設。Goalを `pinnedTop > priorityBoost > priority(high>med>low) > goals.json 安定順` で順位付け。
- `lib/auto-queue.ts`: `compareItems` に Goal順階層を挿入。並び＝①明示pin ②安全枠(要修正/自走化アンカー) ③**Goal順** ④Goal内(手動順→score→優先度→直近)。`buildGoalProgress` も rankGoals 順に整列。`buildAutoQueue` で goalRank を生成し全ソートに適用。
- `lib/factory-dispatch.ts`: `scanFactoryDispatch` の候補ソートを同一階層に統一（表示順＝Factoryの次回実行選択順）。`readGoals` で rankGoals を取得。未使用化した `PRIORITY_SCORE` を削除。
- `app/queue/page.tsx`: `groupItemsByGoal` を追加し、items を Goal ごとにグループ表示（Goalヘッダに進捗/boost/「このGoalだけ」/「Goal詳細」導線、未紐付けは末尾「Goal未設定」）。重複していたカード内 `Goal ○○` 行は撤去（ヘッダがGoalを担当）。
- ドキュメント(ny01セット): `guide/page.tsx`(並び順Goal順ブロック＋FAQ追記)、`docs/operations/current-operating-model.md`(frontmatter updated/updateNote・キュー表・変更履歴) を更新。

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/goal-reader.ts | rankGoals/goalRankOf 共有ヘルパ新設 |
| lib/auto-queue.ts | compareItemsにGoal順階層・goalProgress同順・cmpクロージャ |
| lib/factory-dispatch.ts | scan候補ソートを同一階層に統一・PRIORITY_SCORE削除 |
| app/queue/page.tsx | Goalグループ表示・カード内Goal行撤去 |
| app/guide/page.tsx | 並び順(Goal順)ブロック＋FAQ |
| docs/operations/current-operating-model.md | frontmatter/キュー表/変更履歴 |

## 4. 検証結果

- typecheck: OK（tsc --noEmit 0）
- build: OK（next build 0）
- lint: n/a（tsc/buildで代替）
- 手動確認: pm2 restart 後 /queue /guide HTTP200 + 参照チャンク200（白画面なし）。guideに「並び順（Goal順）」描画確認。live API `/api/auto-queue` で goalProgress が rankGoals 順に返ることを確認。
- 並び規則: `rankGoals` の順序（pin>boost>優先度>安定順）を独立スクリプトで検証 PASS。
- **未観測**: 現状キューが0件（active Goalにtodo無し・open epic無し）のため、Goalグループ表示とGoal順の実挙動はライブ未観測。空状態は正常描画。
- 機密スキャン: clean
- ob sync: n/a（一旦中止中）
- git push: 4bd93f9（origin/main）

## 5. 未対応

- キューにアイテムが入った状態でのグループ表示・Goal順のライブ目視確認（現状0件のため）。
- command-center `TERMS` への新語登録は不要と判断（rankGoalsは内部実装・画面表示は「Goal順」など既存の人間語）。

## 6. 危険ポイント

- 既存機能への影響: 実行系(`scanFactoryDispatch`)のソートを変更したため、Factoryの次回実行選択順が変わる。ただし明示pin・自走化アンカー(goal-ai-factory-os)・要修正は安全のためGoal順より上位に据置し、アンカー飢餓を回避（既存boost値を据置度に流用）。
- DB/認証/本番/機密: 触れていない。データスキーマ変更なし（読み取り順のみ）。
- ロールバック: `git revert 4bd93f9`。
- 観察項目: キュー投入時、Goalグループ順・next選択が意図通りか。`/api/operations/goal-ai-factory-os-next-run-selection-test?ignoreFactoryState=1` でアンカー先頭維持を確認できる。

## 7. 次にやるべきこと

- キュー投入後のライブ目視確認。
- 目標タブのGoal並べ替え操作（pin/boost/priority変更）→キュー順反映の通し確認。

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: 自動実行キューをGoal順で並べる（表示＋実行）
runId: 20260617-010618
日付: 2026-06-17
GitHub commit: 4bd93f9

## 作業目的
- 自動実行キューをGoalごとに表示し、Goalの順番（pin>boost>優先度）がキュー順＝Factoryの次回実行選択を決めるようにする。Goalを並べ替えると次に動く作業も変わる。

## 実施内容
- rankGoals共有ヘルパ新設。compareItems(auto-queue)とscanFactoryDispatch(factory)の両ソートに同一階層(①明示pin ②安全枠[要修正/自走化アンカー] ③Goal順 ④Goal内)を適用。/queueをGoalグループ表示化。guide/operating-model更新。

## 変更ファイル
- lib/goal-reader.ts, lib/auto-queue.ts, lib/factory-dispatch.ts, app/queue/page.tsx, app/guide/page.tsx, docs/operations/current-operating-model.md

## 検証結果
- tsc0 / build0 / pm2再起動後 /queue /guide 実描画200+チャンク200
- rankGoals順序規則 独立テストPASS
- 現状キュー0件のためグループ表示はライブ未観測（空状態は正常）

## 危険ポイント
- 実行系scanFactoryDispatchのソート変更でFactory次回選択順が変わる。自走化アンカー/pin/要修正はGoal順より上位据置で飢餓回避。

## 次にやるべきこと
- キュー投入後のライブ目視・Goal並べ替え→キュー反映の通し確認

## 確認したい観点
- 表示順と実行next選択順が本当に一致しているか（buildAutoQueue vs scanFactoryDispatch）
- 自走化アンカーがGoal順導入後も飢餓しないか
- Goal順の規則(pin>boost>優先度>安定順)はユーザーの「並べ替え」期待に合うか
- 既存のpin/hold/manualOrder/上下移動と矛盾しないか
````

---

## 関連

- progress runId: 20260617-010618（正本）
- 関連 run: 20260616-225004（Goal/Todo/Queue統合・前提）, 20260615-212004（自走化アンカー設計）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
