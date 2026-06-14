---
date: 2026-06-14
task: モバイル下タブを横スクロール全画面タブ化（下タブにない主要画面を撤廃）
runId: 20260614-132906
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260614-105125, 20260614-101321]
commitHashes: [e82e014]
---

# 2026-06-14 モバイル下タブ＝横スクロール全画面タブ化

> ユーザー指示「下タブにない主要画面はなくして。リンクがあって飛ぶものは全て下タブに追加して。」

## 1. 実施内容
- **BottomNav を横スクロール化**（`overflow-x-auto`）。
  - 先頭5つ＝アイコン付き主要タブ: ホーム(`/`) / ToDo(`/decide`) / Project(`/portfolio`) / 目標(`/goal-planner`) / 自動実行(`/queue`)。
  - 続けて `moreItems` で**残りの主要画面を全てテキストタブで列挙**: 作業予約 / Revenue / 運用 / 実行履歴 / ToDo管理 / JSON取込 / 動作確認 / おすすめEpic / 収益化 / 承認 / 自動化 / 工場Epic / Codex / 朝会 / 日別 / AI自走 / レーダー / 案件 / 旧Inbox / 決定事項 / 工場候補 / URL / 旧キュー / 旧ダッシュ / 画面一覧。
  - legacy catch-all／NEW_ROUTES の特別扱いを撤去（active 判定を単純な path 一致へ）。
- **`/legacy`**: 「下タブにない主要画面」グループ表記を撤廃。全画面のカテゴリ別一覧（"画面一覧"タブ）として存続。intro 文も更新。
- guide FAQ・operating-model（画面構成・変更履歴・frontmatter）更新。

→ **リンクで飛べる主要画面（計29＝主要5＋moreItems24）が全て下タブから直接到達でき、「下タブにない主要画面」は無くなった。**

## 2. 変更ファイル
components/navigation/BottomNav.tsx / app/legacy/page.tsx / app/guide/page.tsx / docs/operations/current-operating-model.md

## 3. 検証
- tsc 0 / lint 0 / build 成功。
- 全主要ルート 200（/ /queue /prompt-queue /revenue /guide /logs /legacy）。
- 下タブに 主要5（ホーム/自動実行 等）＋ 全画面タブ（作業予約/実行履歴/画面一覧 等）が描画されることを確認。

## 4. 未対応 / 残課題
- iPhone実機での横スクロール操作性・主要5の視認性・全画面到達の確認。
- 横スクロール下タブが使いにくい場合は「主要5固定＋『すべて』展開シート」方式への変更を検討。
- 旧画面（旧inbox/旧projects/legacy queue 等）の廃止/統合の最終判断（棚卸し review 20260614-101321 の方針表）。

## 5. 次にやるべきこと
- iPhone実機確認。操作性次第で展開シート方式も選択肢。

## ChatGPT レビュー依頼文
```
対象: progress モバイル下タブ全画面タブ化（runId 20260614-132906 / commit e82e014）
変更: components/navigation/BottomNav.tsx, app/legacy/page.tsx, app/guide/page.tsx, docs/operations/current-operating-model.md
背景: ユーザー指示で「下タブにない主要画面」を撤廃し、リンクで飛べる主要画面を全て横スクロール下タブに列挙。
観点:
1. 約29項目の横スクロール下タブは iPhone で実用的か（主要5固定＋『すべて』シート方式の方が良いか）。
2. 主要5(アイコン)＋残りテキストタブの視覚階層は分かりやすいか。
3. 旧画面まで全部タブに出すことの是非（ノイズ vs 到達性）。
4. /legacy を「画面一覧」として残す必要性。
```
