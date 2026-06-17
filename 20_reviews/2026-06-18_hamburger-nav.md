---
date: 2026-06-18
task: 全画面ハンバーガーメニュー(☰)を新設し全ページの到達を保証（孤立ページ解消）
runId: 20260618-005653
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260617-220624]
commitHashes: [98c10a5]
---

# 2026-06-18 全画面ハンバーガーメニュー（☰）新設

> ユーザー指示「基本的に、存在するページはタブかハンバーガーメニューから遷移できるように変えて。遷移しづらい画面は困る」への対応。

---

## 1. 作業目的

- なぜ: ページ数が増え、特にデスクトップ TopNav は主要数タブしか出ず大半の画面へ直接行けない／モバイル BottomNav は30項目の横スクロールで探しにくい＝「遷移しづらい画面」が発生していた。
- 期待効果: どの画面からでも全ページへ1タップで到達できる。孤立ページ（タブにもメニューにも無い画面）を構造的に無くす。

---

## 2. 実施内容

- 全画面メニューの正本 `lib/nav-menu.ts`（`NAV_GROUPS` / `ALL_NAV_LINKS`）を新設。全ページをカテゴリ別（メイン/よく使う/AI工場/判断・記録/計画・候補/全体管理・旧画面）に定義。
- `components/navigation/HamburgerMenu.tsx`（client ドロワー）を新設。☰ボタン→右からドロワー、現在地ハイライト、画面遷移で自動クローズ、背面スクロールロック。記録用に `data-usage-label="メニューを開く"`、ドロワー本体は `data-usage-ignore`。
- `app/layout.tsx` のヘッダー右クラスタ（モバイル fixed / デスクトップ flex 両方）に ☰ を常時設置。
- `app/legacy/page.tsx` のインライン定義を削除し `NAV_GROUPS` を参照（画面一覧とハンバーガーが同一正本＝二重管理解消）。
- 到達照合スクリプト（/tmp/reach-check.mjs）で全実ページの掲載漏れを機械確認。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/nav-menu.ts | 新規。全画面メニュー正本 NAV_GROUPS/ALL_NAV_LINKS |
| progress/components/navigation/HamburgerMenu.tsx | 新規。☰ドロワー |
| progress/app/layout.tsx | ☰をモバイル/デスクトップ両クラスタに設置 |
| progress/app/legacy/page.tsx | インライン定義削除→NAV_GROUPS参照 |
| progress/app/guide/page.tsx | FAQ追加・Legacy説明更新 |
| progress/docs/operations/current-operating-model.md | 画面構成節改稿・frontmatter・変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit 0）
- build: OK（next build 0）
- 到達照合: PASS（実ページ33／メニュー・タブ掲載31／リダイレクトのみ2[/operations→/automation・/pending→/tasks]／孤立0）
- 手動確認: pm2 restart 後 `/` `/legacy` `/usage` `/queue` 実描画200。☰「メニューを開く」がモバイル・デスクトップ両クラスタに描画。`/legacy` が正本参照で全カテゴリ描画。白画面/エラーなし。チャンク200。
- 機密パターン事前チェック: OK（ナビ文言のみ）
- ob sync: n/a（ob sync 一旦中止中）
- git push: 98c10a5（origin/main へ push 済み）

---

## 5. 未対応

- 実機（iPhone/PC）での☰開閉→各画面遷移の通し確認は未実施（curl とスクリプト照合で代替）。
- BottomNav の横スクロール項目（約25件）は☰前提なら削減してUI簡素化できるが、今回は既存タブを近道として残置（破壊回避）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。新規2ファイル＋layoutに☰追加＋/legacyをDRY化のみ。既存タブ・各ページ・実行ロジック・データ不変更。
- DB / 認証 / 本番 / 機密: 触れていない。
- ロールバック手段: commit 98c10a5 の revert。
- 観察すべき項目: 新ページ追加時に nav-menu.ts へ追記しないと再び孤立する（運用ルール化が必要）。

---

## 7. 次にやるべきこと

- 実機で☰の操作性（開閉・スクロール・遷移）を確認。
- 「新ページ追加時は lib/nav-menu.ts に1行追加」を運用ルール／PRチェックに組み込む。
- ☰前提で BottomNav 横スクロール項目の削減（UI簡素化）を検討。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: 全画面ハンバーガーメニュー(☰)を新設し全ページの到達を保証
runId: 20260618-005653
日付: 2026-06-18
GitHub commit: 98c10a5

## 作業目的
- ページが増えタブから辿れない画面が発生。全ページをタブかハンバーガーから必ず辿れるようにする。

## 実施内容
- 全画面メニュー正本 lib/nav-menu.ts(NAV_GROUPS) を新設。
- HamburgerMenu.tsx(☰ドロワー・現在地ハイライト・遷移で自動クローズ・背面スクロールロック)を layout のヘッダー右(モバイル/デスクトップ両方)に常時設置。
- /legacy を NAV_GROUPS 参照に改修(二重管理解消)。
- 到達照合スクリプトで孤立0(33ページ中31掲載+リダイレクト2)を確認。BottomNav/TopNav既存タブは近道として残置。

## 検証結果
- tsc0 / build0 / 到達照合PASS / 実描画200・☰両クラスタ描画・白画面なし

## 未対応
- 実機通し確認は未(curl/スクリプト代替)。BottomNav横スクロール項目の削減は未実施(残置)。

## 危険ポイント
- 新規中心＋layoutに☰追加＋/legacy DRY化。既存タブ/ページ/ロジック不変更。新ページ追加時にnav-menu.ts追記を忘れると再孤立。

## 確認したい観点
- 全ページ到達保証の設計(単一正本nav-menu.ts)の妥当性
- ハンバーガーのUX(モバイル/デスクトップ両対応・ドロワー方向・スクロール)
- 既存タブ(BottomNav横スクロール30項目)との役割重複をどう整理すべきか
- 新ページ孤立を防ぐ運用ルール/自動チェックの組み込み方
- 収益化インパクト評価は妥当か(low)
````

---

## 関連

- progress runId: 20260618-005653（正本）
- 関連 run: 20260617-220624（使用状況ページ）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
