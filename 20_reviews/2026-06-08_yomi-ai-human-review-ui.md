---
date: 2026-06-08
task: AI良問判定×人間評価すり合わせレビュー画面（/yomi/review）の追加
runId: 20260608-105312
targetApp: mahjong
monetizationImpact: low
theme: [mahjong, workflow, app-strategy]
relatedRunIds: []
commitHashes: [23b5aed]
# reviewFileCommit:   # 任意。本ファイル自身の push commit を残したい場合に手動で埋める
---

# 2026-06-08 AI良問判定×人間評価すり合わせレビュー画面（/yomi/review）の追加

---

## 1. 作業目的

- なぜこの作業をするのか: `/yomi` 候補の品質採点（`yomi-score.mjs` による S/A/B/C 自動判定）が**人間の感覚と一致しているか**を検証したい。
- 背景: 直近で B→A 自動採点を導入したが「自動 S/A は人手レビューで最終確認することを推奨」とされており、配点・閾値（score>=7→S / >=5→A）が妥当か未検証。
- 期待効果: AI判定 vs 人間判定の一致率を可視化し、過大評価／過小評価のパターンから配点・閾値の修正方針を出せるようにする。

---

## 2. 実施内容

- 採点ロジック（`scripts/lib/yomi-score.mjs`）を Next アプリから使えるよう **`lib/yomiScore.ts` へ TS 移植**。`tsx` で全10問の出力が mjs と完全一致することを確認。
- **`app/yomi/review/page.tsx` を新規作成**:
  - AIが S/A 判定した問題を動的抽出して一覧表示（ハードコードしない）。
  - 各問に「問題ID / 盤面（`YomiBoardView` revealed）/ 河 / 当たり牌 / AIスコア / AI判定理由（読み要素の加点内訳）」を表示。
  - 人間が **S/A/B/C** を採点（`localStorage("yomiReviewRatings")` 保存・再クリックで解除）。
  - 集計: **一致率 / 過大評価トップ5 / 過小評価トップ5 / AI:A以上×人間:C 件数 / 重み修正案 / コピー用プレーンテキスト**。
- `app/yomi/page.tsx` にレビュー画面への導線ボタンを追加。
- 関連判断: 検証対象はユーザー記憶では8問だが、正本 `yomi-questions.json` では S/A が**10問（y001〜y012）**。件数をハードコードせず「AIが S/A 判定した問題を動的抽出」する設計にした。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `apps/mahjong/lib/yomiScore.ts` | 新規。`yomi-score.mjs` の TS 移植（`scoreQuestion` / `RANK_NUM` / `RANK_ORDER` 等）。出力一致を検証 |
| `apps/mahjong/app/yomi/review/page.tsx` | 新規。レビューUI + 集計（一致率・過大過小評価・修正案）。localStorage 保存 |
| `apps/mahjong/app/yomi/page.tsx` | yomi トップにレビュー画面導線ボタンを追加 |

---

## 4. 検証結果

- typecheck: OK（`tsc --noEmit` 0 error）
- build: OK（`next build` 成功・`/yomi/review` 静的生成）
- lint: OK（`eslint` 0 error。localStorage 初期同期の effect に限定 disable コメント）
- 手動確認: `npm run dev` 起動 → `GET /yomi/review` HTTP 200。全 S/A 問題10件（y001〜y012）・AI判定理由・当たり牌・盤面・集計パネル・人間評価ボタンの描画を確認。
- AI/TS 採点一致: `tsx` 実行で mjs と全10問のランク・スコア・理由が一致
- 機密パターン事前チェック: OK（実値なし）
- ob sync: Fully synced（本ファイル保存後に実行）
- git push: 23b5aed（mahjong main へ push 済み）

---

## 5. 未対応

- 人間評価データの入力そのものはユーザー操作待ち（画面は完成、評価値が入ると集計が動く）。
- 過大評価リスト・修正案は評価入力後に出る設計のため、未評価状態ではサマリーは「まだ評価がありません」表示（仕様）。
- 配点・閾値の実際の調整は人間評価の結果を見てから（本作業では UI 提供までで、重み変更は未実施）。

---

## 6. 危険ポイント

- 既存機能への影響: 新規ページ＋導線追加のみ。既存 quiz/result/home・`yomi-questions.json`・`scripts/`・型スキーマは未変更。
- **配点ロジックの二重管理**: `scripts/lib/yomi-score.mjs`（ingest 正本）と `lib/yomiScore.ts`（アプリ表示用）が同一ロジックの重複。**配点・閾値を変える時は必ず両方を同時更新**する必要がある（コメントで明記済み）。将来は共有化を検討。
- DB / 認証 / 本番 / 機密: いずれも非該当。

---

## 7. 次にやるべきこと

- ユーザーが10問に人間評価を入力 → 一致率・過大過小評価・AI:A以上×人間:C を確認。
- 過大評価に頻出する読み要素（現物 / 壁 / ワンチャンス 等の補助要素）が単独で点を稼ぎすぎていないか確認し、`yomi-score.mjs` + `lib/yomiScore.ts` の配点を**同時調整**。
- AI:A以上×人間:C が多い場合は「補助要素のみで A 到達するケースを B に落とす」条件追加を検討。
- 二重管理の解消（採点ロジックの単一ソース化）を中期課題として検討。

---

## 8. ChatGPT レビュー依頼文

```
対象アプリ: mahjong（麻雀 /yomi 当たり牌読み）
runId: 20260608-105312 / commit: 23b5aed
変更: /yomi/review に「AI良問判定 × 人間評価」すり合わせ画面を新規追加。
  - AIが S/A 判定した問題（現状10問）を表示し、人間が S/A/B/C を採点
  - 一致率 / 過大評価トップ5 / 過小評価トップ5 / AI:A以上×人間:C 件数 / 重み修正案 を集計
  - 採点ロジック yomi-score.mjs を lib/yomiScore.ts へ TS 移植（出力一致を検証済み）

確認してほしい観点:
1. 検証設計の妥当性: AI vs 人間の一致率で「良問判定の質」を測るアプローチは適切か。ランクを S=4..C=1 の数値差(delta)で過大/過小評価を測る方法に歪みはないか。
2. 修正案ロジック: 「過大評価問題に頻出する読み要素を配点引き下げ候補にする」ヒューリスティックは妥当か。無スジ×染め手の重複加点など既知の偏りを拾えるか。
3. サンプル数: S/A が10問しかない状態で一致率を見て配点を動かすのは早計でないか。何問あれば判断材料になるか。
4. 二重管理リスク: yomi-score.mjs と lib/yomiScore.ts の同一ロジック重複。単一ソース化すべきか、許容範囲か。
5. UX: 人間が S/A/B/C を付ける際の基準が曖昧になりやすい。採点ガイド（各ランクの定義）を画面に出すべきか。
```
