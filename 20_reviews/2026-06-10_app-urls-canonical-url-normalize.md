---
date: 2026-06-10
task: アプリURL一覧 入力URLをcanonical URLに正規化して表示（中断作業の仕上げ）
runId: 20260610-212135
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260610-123141]
commitHashes: [0d2d730]
---

# 2026-06-10 アプリURL一覧 入力URLをcanonical URLに正規化して表示

> 1 作業 = 1 ファイルの自動生成レビュー。前回(20260610-123141 / commit 3a33325)で URL編集UIを入れた直後に途中で落ちていた「URL正規化」対応を仕上げたもの。

---

## 1. 作業目的

- なぜこの作業をするのか: ユーザー依頼「入力したurlを正規として表示する対応して。途中で落ちたはず」
- 背景: 前回セッションでURL正規化を実装途中のまま中断。`lib/url-normalize.ts` が未追跡のまま、commit 3a33325 の `app-urls.ts` がそれを import しており、未コミット差分も残っていた（build破綻リスクのある中途状態）。
- 期待効果: 手入力URL（スキーム無し `example.com/foo` や IP `160.251.143.146:3010/...`）を canonical な href へ正規化して保存・リンク表示できる。

---

## 2. 実施内容

- 主な変更（中断状態の差分を検証して確定）:
  - `lib/url-normalize.ts` 新規: `normalizeUrlString()`。スキーム無し→ドメインは https / IP・localhost は http、protocol-relative(`//host`)→https、最終的に `new URL().href` で canonical 化。client/server 両用の純粋関数（fs 非依存）。
  - `lib/app-urls.ts`: `normalizeUrlInput` が url を `normalizeUrlString` で正規化。
  - `components/app-urls/AppUrlsBoard.tsx`: 入力欄を `type=url`→`text` 化、`onBlur` で正規化、placeholder を「example.com/path（https:// は自動補完）」に変更。
- 関連調査・判断:
  - 単体ロジック8ケースをnodeで確認（`example.com/foo`→`https://...`、`160.251.143.146:3010/app-urls`→`http://...`、`localhost:3000`→`http://...`、`//cdn`→`https://`、空/未確認→`未確認`、大文字スキーム正規化）。
  - PATCH 実機確認で `160.251.143.146:3010/app-urls`→`http://160.251.143.146:3010/app-urls` を確認。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/url-normalize.ts | 新規。normalizeUrlString（スキーム補完/protocol-relative補完/href化） |
| progress/lib/app-urls.ts | normalizeUrlInput が normalizeUrlString を使用 |
| progress/components/app-urls/AppUrlsBoard.tsx | 入力欄 type=text 化 + onBlur 正規化 + placeholder 自動補完明示 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit exit 0）
- build: OK（next build exit 0）
- lint: OK（next lint 警告0）
- 手動確認: 正規化ロジック8ケースOK / PATCH実機でスキーム補完を確認 / `/app-urls` HTTP 200 / pm2 restart progress
- 機密パターン事前チェック: OK（コード3ファイルにキー・トークン等なし）
- ob sync: Fully synced
- git push: 0d2d730（ny01 main）

---

## 5. 未対応

- スキップした項目: 全アプリ分の入力UX一巡確認は未実施（progress エントリでのPATCH確認のみ）
- 環境制約で実行できなかったこと: iPhone 実機でのリンク開封確認は未実施
- ユーザー判断待ち: なし

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。正規化は保存・表示の入口のみ。既存URLは編集保存時に再正規化される。
- DB / 認証 / 本番 / 機密に触れたか: 触れていない。
- **発生した事故と復旧（要注意）**: PATCH 実機テストを **本番 data/real に直接** 投げたため、PATCH の置換セマンティクスで progress アプリの urls 配列（3件）が test 1件に上書きされた。app-urls.json はバックアップ機構なし（store.writeJson は直接上書き・git 未追跡）。観測事実（pm2/ss/curl・operationMemo 規約）から vercel(未確認)/vps(公開IP)/vps_internal(localhost) の3件を復元し、pm2 restart・page200・total12アプリ整合を確認。**evidenceDetail 等の細部は再構成のため、UI で実値を要確認。**
- ロールバック手段: コード3ファイルは commit 0d2d730（`git revert` 可）。data は復元済み。
- 観察すべき項目: 次回 PATCH 検証は必ず一時データ複製（前回 run はport3099複製でE2E）で行う。

---

## 7. 次にやるべきこと

- フォローアップ作業: `data/real/app-urls.json` のバックアップ/履歴機構（writeJson 前の .bak）を検討。
- ユーザー判断待ち事項: 復元した progress エントリの evidenceDetail が実態と一致するか UI で確認。
- 観察項目: 他アプリ URL を手入力した際の onBlur 正規化挙動。
- 関連 ToDo の追加候補: store.writeJson に簡易スナップショット追加。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場・ToDo・実行履歴ダッシュボード）
作業: アプリURL一覧 入力URLをcanonical URLに正規化して表示（中断作業の仕上げ）
runId: 20260610-212135
日付: 2026-06-10
GitHub commit: 0d2d730（リポジトリ kaeru07/ny01, main）

## 作業目的
- 手入力URLを canonical な href に正規化して保存・表示。前回中断分の仕上げ。

## 実施内容
- lib/url-normalize.ts 新規（normalizeUrlString: スキーム補完/protocol-relative補完/href化, client/server両用）
- lib/app-urls.ts: normalizeUrlInput が normalizeUrlString を使用
- AppUrlsBoard.tsx: 入力欄 type=text 化 + onBlur 正規化 + placeholder 自動補完明示

## 変更ファイル
- progress/lib/url-normalize.ts（新規）
- progress/lib/app-urls.ts
- progress/components/app-urls/AppUrlsBoard.tsx

## 検証結果
- typecheck/build/lint: いずれも OK
- 手動確認: 正規化8ケース / PATCH実機で 160.251.143.146:3010/app-urls→http補完 / /app-urls 200
- 機密スキャン: OK

## 未対応
- 全アプリ分の入力UX一巡 / iPhone実機リンク確認は未実施

## 危険ポイント
- PATCH実機テストを本番data/realに投げ、置換セマンティクスでprogressエントリのurls配列を上書き→観測事実から3件復元（バックアップ機構なし）。evidenceDetail細部は要確認。

## 確認したい観点
- スキーム推定（ドメイン=https / IP・localhost=http）の妥当性、誤判定ケース
- onBlur 正規化のUX（編集途中の確定タイミング）
- data/real 直書き＋バックアップ無しの運用リスクと改善案
- 既存機能を壊していないか
- 収益化インパクトの評価は妥当か（none）
````

---

## 関連

- progress runId: 20260610-212135（正本）
- 関連 run: 20260610-123141（URL編集UI追加 / commit 3a33325）
- 関連アプリ: [[../02_apps/progress]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
