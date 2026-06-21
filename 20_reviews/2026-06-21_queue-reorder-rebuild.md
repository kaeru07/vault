---
date: 2026-06-21
task: 自動実行キューの並び替えを根本修正(即時反映UI・上下が効かない問題)
runId: 20260621-235648
targetApp: progress
monetizationImpact: none
theme: [workflow]
commitHashes: [ce6ec5c]
---

# 2026-06-21 自動実行キューの並び替えを根本修正

## 1. 作業目的
- ユーザー報告: キューの上下ボタンを押しても並びが変わらない・仕組みが微妙。順番をユーザーが変更でき即時反映するUIへ作り替え。

## 2. 原因
1. `lib/auto-queue.ts` の compareItems が **goalRank を manualOrder より先**に評価 → 上下で manualOrder を変えても goal順に戻る。
2. 画面が1クリックごと**全ページ再読込**で反応が分かりにくい。
3. 先頭が pinnedTop 固定で動かず混乱。

## 3. 修正(Codex委譲・Claude検証)
- compareItems: pinnedTop → safety → **manualOrder** → goalRank → score の順へ(manualOrder を goalRank より優先)。
- `components/queue/QueueReorderList.tsx`(client): 非pinを useState で持ち ↑↓ で即座にローカル入れ替え→ POST /api/auto-queue/reorder で一括保存(楽観更新・全ページ再読込なし)。pin は 📌固定表示+固定解除ボタン。
- `app/api/auto-queue/reorder/route.ts`: orderedWorkItemIds を受け manualOrder=index+1 を epic/todo/goal へ一括永続化。
- `app/queue/page.tsx`: フラットセクションを QueueReorderList に置換。

## 4. 検証(Claude実API)
- tsc0/build0/lint0。stale pin解除後、reorderで先頭3件を逆順送信→executable先頭3が逆順に反映(前#1が後#3)。/queue に QueueReorderList(即座に反映/固定解除)描画。

## 5-7. 未対応/危険/次
- iPhone実機で上下が即時に動くか最終確認。優先順位の支配順は pin > 手動 > goal。破壊操作なし。

## 8. ChatGPT レビュー依頼文
````text
対象: progress 自動実行キュー並び替え / runId 20260621-235648 / commit ce6ec5c
上下が効かない原因(compareItemsでgoalRank>manualOrder)を修正しmanualOrder優先に。
即時反映の楽観更新リスト(QueueReorderList)+一括保存API。実APIで送った順がexecutableに
反映を確認。実装Codex委譲・Claude検証。確認観点: pin>手動>goalの優先順は妥当か/楽観更新と
サーバ順の乖離(pin floating)の扱い。
````
