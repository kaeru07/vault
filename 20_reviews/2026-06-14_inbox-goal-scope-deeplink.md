---
date: 2026-06-14
task: Inboxをゴール単位で紐づけ・次回実行予定から該当レビューへ直接遷移
runId: 20260614-021255
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260613-174719, 20260613-234811]
commitHashes: [d582a50]
---

# 2026-06-14 Inbox ゴール紐づけ＋該当レビュー直接遷移

> Codex 実装 → Fable レビュー（reviewer）・e2e検証・コミット。

## 1. 原因
司令塔トップ「Inboxでレビューする」の遷移先が `/decide` 固定で、`InboxTabs` が URL クエリ未対応・「今日の判断」タブ既定だったため、レビューがあっても今日の判断0件画面に飛んで詰まっていた。

## 2. 変更内容
- `InboxTabs` を `useSearchParams` 対応: `?tab=today|review|candidates|aiHold` / `?reviewFilter=...` / `?filter=needs_followup`(→followow) / `?focusRunId=` / `?goalId=`。
- `focusRunId`: 該当レビューカードへスクロール＋ハイライト＋「次回実行予定から移動しました」案内、run の状態に応じてフィルタ自動切替（reviewed/needs_followup/snoozed/未確認）。
- `goalId` 紐づけ（変換レイヤー）: `item.goalId`→`run.epicId`経由`Epic.goalId`→`targetApp`×`Goal.projectId`→不明は `unassigned`。未紐づけは消さず表示・絞り込み可。
- ゴール単位件数を Inbox と同一ソースで集計。
- `deriveResolution` の遷移先を `/decide?tab=...&goalId=...&focusRunId=...` に修正（レビューありで0件today へ飛ぶ導線を禁止）。
- 次回自動実行予定カードに Goal名 / 候補外理由 / こうすれば動きます / 該当レビュー・Goalレビュー一覧・キュー調整。
- 0件タブで同Goal他タブに件数があれば案内ボタン。

## 3. 変更ファイル
components/newux/InboxTabs.tsx / lib/command-center.ts / lib/auto-queue-score.ts / lib/auto-queue.ts / types/auto-queue.ts / components/newux/InboxActions.tsx / app/decide/page.tsx / app/page.tsx / app/guide/page.tsx / docs/operations/current-operating-model.md

## 4. goalId紐づけ仕様
変換レイヤーで付与（新正本を増やさない）。推定順: item.goalId → ExecutionRun.epicId 経由 Epic.goalId → targetApp と Goal.projectId 一致 → `unassigned`。unassigned は削除せず「未紐づけ」表示・絞り込み可。

## 5. 「Inboxでレビューする」の新遷移先
- レビュー待ち系: `/decide?tab=review&goalId=...&focusRunId=...`
- 要修正: `&filter=needs_followup`
- 承認系: `/decide?tab=today&goalId=...`
- AI保留: `/decide?tab=aiHold&goalId=...`

## 6. focusRunId挙動
該当 run のカードを探し、状態に応じ未確認/要修正/あとで/レビュー済みフィルタへ自動切替、ハイライト＋「次回実行予定から移動しました」。

## 7. 0件タブの改善
goalId絞り込み中に対象タブ0件でも、同Goalの他タブに件数があれば「レビュー待ちを開く」等の案内ボタンを表示。0件放置を解消。

## 8. 次回自動実行予定カードの改善
「最優先指定中だが候補外」枠に Goal名・候補外理由・「こうすれば動きます」・該当レビュー(focusRunId付き)・Goalレビュー一覧(goalId付き)・キュー調整。

## 9. 検証結果（Fable独立実行）
- tsc 0 / lint 0 / build 成功（/decide は dynamic）。
- e2e: resolution href = `/decide?tab=review&goalId=goal-ai-factory-os&focusRunId=20260613-234811`（0件today回避）。`/decide?tab=review` がレビュータブを描画（35件）。`?goalId=goal-ai-factory-os` で 35→17 に絞り込み。`?focusRunId=...` で「次回実行予定から移動しました」案内を確認。
- goalId推定は run.epicId→Epic.goalId 経由で goal-ai-factory-os に正しく割当（unassigned へ誤落ちしない）。
- reviewed/snoozed/needs_followup 回帰なし・物理削除なし。

## 10. iPhone表示確認結果
未実機（ポート待受制限）。1カラム・flex-wrap で崩れにくい構成。実機目視は残課題。

## 11. 残課題
- iPhone実機での deeplink/ハイライト/フィルタ自動切替/0件案内の目視。
- unassigned 件数の削減（既存 run の epicId 紐付け精度）。

## 12. 次にやるべきこと
- 実機確認後に実運用へ。必要なら未紐づけ削減のため run↔epic 紐付けを補強。

## ChatGPT レビュー依頼文
```
対象: progress Inbox ゴール紐づけ＋該当レビュー直接遷移（runId 20260614-021255 / commit d582a50）
変更: InboxTabs.tsx, command-center.ts, auto-queue.ts, auto-queue-score.ts, types/auto-queue.ts,
  InboxActions.tsx, app/decide/page.tsx, app/page.tsx, guide, operating-model
背景: 「Inboxでレビューする」が/decide固定で今日の判断0件に飛び詰まる→/decide?tab=review&goalId&focusRunIdへ修正＋Goal単位絞り込み＋focusハイライト。
観点:
1. goalId推定順(item→run.epicId→targetApp→unassigned)は妥当か。誤割当リスクは。
2. URLクエリ駆動(useSearchParams)でSSR/クライアント初期表示がズレないか。
3. focusRunIdのフィルタ自動切替で、対象カードが必ず可視になるか。
4. ゴール件数とInbox件数の一致(同一ソース集計)は保証されているか。
5. 0件タブ相互案内のUXは過不足ないか。
```
