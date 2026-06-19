---
date: 2026-06-19
task: モバイル下タブを主要6タブのみに整理（合計6・均等配置、moreItemsは☰へ集約）
runId: 20260619-220804
targetApp: ny01/progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260618-005653, 20260619-213955]
commitHashes: [235d43a]
---

# 2026-06-19 モバイル下タブを主要6タブに整理

> ユーザー指示「全体のタブに加えて。6つにして合計」。

---

## 1. 作業目的

- モバイル下タブを主要6タブだけに整理（合計6）。横スクロールの長い全画面列挙をやめ、それ以外は右上 ☰ メニューに集約する。

---

## 2. 実施内容

- `components/navigation/BottomNav.tsx` から旧 `moreItems`（横スクロールの全画面25件）を廃止。
- 主要6タブ（ホーム/ToDo/Project/目標/自動実行/運用）を `flex-1 min-w-0` で均等配置（横スクロール撤廃）。
- それ以外の全画面は右上 ☰ メニュー（HamburgerMenu / 正本 `lib/nav-menu.ts`）に集約。
- 安全確認: 削除前に moreItems 25件すべてが ☰ メニューに掲載済みであることを照合（孤立0）。削除後も到達照合スクリプトで全33実ページが「6タブ＋☰＋リダイレクト2(/pending,/operations)」で到達可能と確認。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/components/navigation/BottomNav.tsx | moreItems廃止・6タブをflex-1均等配置 |
| progress/docs/operations/current-operating-model.md | 画面構成(6タブのみ・☰集約)/frontmatter/変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0） / build: OK（next build 0）
- 到達照合: 実ページ33 / 主要タブ6 / 孤立0（menu+tabs+redirect）。
- 手動: ホーム200・下タブに6ラベル（ホーム/ToDo/Project/目標/自動実行/運用）描画・旧moreItemsラベル（作業予約/工場Epic/おすすめEpic/旧ダッシュ）非表示（0件）。
- ob sync: n/a / git push: 235d43a

---

## 5. 未対応

- 実機での6タブの収まり（端末幅での文字切れ）・☰からの全画面到達の通し確認は未（curl＋照合で代替）。

---

## 6. 危険ポイント

- 影響: 中（下タブから直接行けた画面が減る）。ただし全画面は ☰ メニューから到達可能（削除前後で孤立0を照合済み）。
- ロールバック: commit 235d43a の revert。

---

## 7. 次にやるべきこと

- 実機で6タブの表示確認（必要ならラベル短縮）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: モバイル下タブを主要6タブのみに整理（合計6・均等配置、moreItemsは☰へ集約）
runId: 20260619-220804
日付: 2026-06-19
GitHub commit: 235d43a

## 実施内容
- BottomNavから旧moreItems(横スクロール全画面25件)を廃止し、主要6タブ(ホーム/ToDo/Project/目標/自動実行/運用)をflex-1均等配置。それ以外は右上☰メニュー(nav-menu.ts)に集約。
- 削除前にmoreItems25件全てが☰掲載済みを照合(孤立0)、削除後も全33実ページが6タブ+☰+リダイレクト2で到達可能と確認。

## 検証
- tsc0/build0/到達照合PASS/ホーム200・6タブ描画・旧moreItemsラベル非表示。

## 未対応
- 実機での6タブ収まり・☰到達の通し確認は未。

## 確認したい観点
- 下タブを6に絞り残りを☰に集約した判断の妥当性(よく使う画面が☰に隠れないか)
- 6タブの端末幅での収まり(ラベル短縮要否)
````

---

## 関連

- progress runId: 20260619-220804（正本）
- 関連: components/navigation/BottomNav.tsx / lib/nav-menu.ts(☰正本) / [[2026-06-18_hamburger-nav]]
- 関連アプリ: [[../02_apps/progress]]
