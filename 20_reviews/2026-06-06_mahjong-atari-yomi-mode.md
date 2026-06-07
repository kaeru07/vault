---
date: 2026-06-06
task: mahjongアプリに当たり牌読み(捨て牌読み)練習モードを新設
runId: 20260606-220319
targetApp: mahjong
monetizationImpact: low
theme: [app-strategy, workflow]
relatedRunIds: []
commitHashes: []   # 未commit（ユーザー判断待ち）
# reviewFileCommit:
---

# 2026-06-06 mahjongアプリに当たり牌読み(捨て牌読み)練習モードを新設

## 1. 作業目的

- なぜこの作業をするのか: 既存の「何切る」クイズ(mahjong)を流用し、対局終盤の場面から「当たり牌だけを伏せて予想する」捨て牌読み練習を提供するため。
- 背景: 全体の捨て牌・手出し/ツモ切り・副露・リーチ状況から当たり牌を読む練習は需要があるが、既存アプリは「自分が何を切るか」しか扱っていなかった。
- 期待効果: 放銃回避力(ベタオリ・スジ・壁・染め手読み)の体系的トレーニング。1問=ロン/ツモされた直前場面の問題集化。

## 2. 実施内容

- 既存「何切る」クイズを壊さず、別ルート `/yomi` として非破壊で新設。
- 当たり牌のみ「？」で伏せ、回答後に正解牌/ロンツモ/和了者/放銃者/待ち形/役/読み筋/危険理由/他選択肢の否定理由を表示。
- 盤面に4人分の河(1行6枚)・手出し(濃)/ツモ切り(薄+青点)・リーチ宣言牌(赤枠)・副露・ドラ・場風/自風・点数・巡目を表示。凡例つき。
- TileDisplay スプライトを再利用。tsumo問題は河に伏せ牌が無いため上部チップで「？」→正解牌を提示。
- ホームに 難易度/タグ/問題数 選択、結果画面に 正答率+復習モード(間違えた問だけ再出題)。
- 問題データ12問を新規作成。10巡目以降・全員の河あり・読み要素あり。
- 既存ホーム(HomeContent)に「当たり牌読み練習へ」導線を追加。

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| types/yomi.ts | 新規: 型定義(YomiQuestion/roundInfo/players/discards/result/question) |
| lib/yomi.ts | 新規: JSON読み込み・フィルタ・採点・ラベル変換 |
| data/yomi-questions.json | 新規: 12問の問題データ |
| components/YomiBoardView.tsx | 新規: 4人河+手出しツモ切り+リーチ宣言+副露+ドラ+当たり牌伏せ表示 |
| app/yomi/page.tsx | 新規: ホーム(難易度/タグ/問題数選択) |
| app/yomi/quiz/page.tsx | 新規: 問題画面(選択肢=牌, 回答後に読み筋/解説) |
| app/yomi/result/page.tsx | 新規: 結果画面(正答率+復習モード) |
| components/HomeContent.tsx | 既存ホームに当たり牌読みへの導線ボタンを追加 |

## 4. 検証結果

- typecheck: OK (`npx tsc --noEmit` 0 error)
- build: OK (`npm run build` 成功・全9ルート ○Static / API非依存 / Vercel静的化維持)
- lint: n/a (build内のTypeScript検査はpass)
- 手動確認: データ整合スクリプトで 全12問 correctTile∈choices / correctTile==result.hiddenTile / hiddenTile∈waits / loser最終打牌==hiddenTile / 河4人非空 / turn>=10 をパス。テーマ網羅確認(リーチ/ダマ/染め手/対子落とし/リャンメン落とし/スジ/壁/字牌/手出しツモ切り/単騎/カンチャン/親リーチ/ツモ)。
- 機密パターン事前チェック: OK（機密情報なし）
- ob sync: Fully synced（本レビュー保存時に実行）
- git push: 582fdd7 / pushed（2026-06-06 仕上げrun 20260606-232717 で origin/main へ反映済み。詳細は [[2026-06-06_mahjong-yomi-verify-deploy]]）

## 5. 未対応

- iPhone実機でのレイアウト確認（grid 2カラム折返し・牌サイズ）。
- 問題数の20問以上への拡充（各テーマ複数問）。
- git commit / push（既存ルールで明示指示まで実行しない方針のため未実施）。

## 6. 危険ポイント

- 既存機能への影響リスク: 低。既存「何切る」関連ファイル(types/question.ts, lib/quiz.ts, BoardView等)は無変更。追加は新規ファイル＋HomeContentへのボタン1個のみ。
- DB / 認証 / 本番 / 機密: 非接触。データは静的JSONバンドル(API化なし)。
- ロールバック手段: 追加ファイル削除 + HomeContent のボタン差分を戻すだけ。
- 観察すべき項目: 牌スプライト未登録牌(赤5等)が出題に混ざっていないか(現状は通常牌のみ使用)。

## 7. 次にやるべきこと

- フォローアップ作業: iPhone実機確認 → 問題数拡充 → 復習モードの解説一覧化。
- ユーザー判断待ち事項: commit/push 可否、本番(Vercel)反映可否。
- 観察項目: 出題テーマ別の正答率が偏らないか。
- 関連 ToDo の追加候補: 当たり牌読み問題の取り込み機能(画像→JSON)。

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: mahjong
作業: 当たり牌読み(捨て牌読み)練習モードの新設
runId: 20260606-220319
日付: 2026-06-06
GitHub commit: 未push（ローカルのみ）

## 作業目的
- 既存「何切る」クイズを流用し、対局終盤の場面から当たり牌だけを伏せて予想する捨て牌読み練習を新設。

## 実施内容
- /yomi に新ルート（ホーム/問題/結果）を非破壊で追加。
- 4人河・手出し/ツモ切り・リーチ宣言牌・副露・ドラ・場風自風・点数・巡目を表示し、当たり牌のみ「？」で伏せる。
- 回答後に 正解牌/ロンツモ/和了者/放銃者/待ち形/役/読み筋/危険理由/他選択肢の否定理由 を表示。
- 12問作成（10巡目以降・読み要素あり：リーチ/ダマ/染め手/対子落とし/リャンメン落とし/スジ/壁/字牌/手出しツモ切り/単騎/カンチャン/親リーチ/ツモ）。

## 変更ファイル
- types/yomi.ts, lib/yomi.ts, data/yomi-questions.json, components/YomiBoardView.tsx,
  app/yomi/page.tsx, app/yomi/quiz/page.tsx, app/yomi/result/page.tsx, components/HomeContent.tsx

## 検証結果
- typecheck / build: OK（全ルート静的・API非依存）
- 手動確認: 12問の整合スクリプトpass、テーマ網羅確認。
- 機密スキャン: OK

## 未対応
- iPhone実機確認、問題数拡充、commit/push（未実施）。

## 危険ポイント
- 既存「何切る」は無変更。DB/認証/本番/機密 非接触。

## 確認したい観点
- 麻雀の読み筋・待ち形・役の記述に誤りがないか（特にスジ引っ掛け/壁/対子落とし/リャンメン落としの解説）
- 当たり牌以外の情報が十分に見えているか（要件「当たり牌以外はなるべく見せる」）
- 既存機能を壊していないか
- 次にやるべき作業の優先順位は妥当か
- 収益化インパクトの評価は妥当か（low）
````

---

## 関連

- progress runId: 20260606-220319（正本）
- 関連アプリ: [[../02_apps/mahjong]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
