---
date: 2026-06-08
task: progressのアプリURL一覧をiPhone確認用に再設計（localhost非表示・確認URL最優先・フィルタ追加）
runId: 20260608-235115
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: []
commitHashes: [d5a8977]
# reviewFileCommit:
---

# 2026-06-08 progressのアプリURL一覧をiPhone確認用に再設計

> 1 作業 = 1 ファイルの自動生成レビュー。

---

## 1. 作業目的

- なぜこの作業をするのか: progressの「アプリURL」一覧は iPhone Safari / iPhoneアプリから各アプリの動作確認をするための一覧。だが多くのアプリが `http://localhost:3000` 等を主要URLとして表示しており、iPhoneから開けず確認用一覧として機能していなかった。
- 背景: URL台帳スキーマ(lib/app-urls.ts)は kind / confidence は持つが「iPhoneから実際に到達できるか」という概念が無く、UIが localhost も Vercel も同列のクリック可能URLとして並べていた。VPS内 pm2 で localhost バインドしている NetScope(localhost:3000/8080/8888) や progress(localhost:3010) も主要URL扱いになっていた。
- 期待効果: iPhoneで押せるURL(Vercel/公開VPS/HTTPS公開)だけを確認URLとして上部に出し、localhost等は「iPhone直接不可」として補助情報に隔離。未確認は推測でURLを作らず未確認と明示。

---

## 2. 実施内容

- 主な変更:
  - `lib/app-urls.ts`: `IphoneAccess`(ok/blocked/unknown)型と `classifyRecordAccess()` を追加。URLのhostが localhost/127.0.0.1/0.0.0.0/::1/プライベートIP(10./192.168./172.16-31.)なら blocked、kind=ssh_port_forward も blocked、url='未確認'は unknown、それ以外(公開host)は ok。
  - `enrichApp()` / `enrichAppUrls()`: アプリ単位で公開URL(ok)を選好順(vercel>vps>api>...)で集約し `iphonePrimary` を決定。公開URLが無く blocked があれば actionHint='要Vercel化 / 要公開URL確認'、全unknownなら'要公開URL確認'。
  - `components/app-urls/AppUrlsBoard.tsx`(新規・クライアント): iPhone確認URLを緑枠で最優先表示(iPhoneで開くボタン)、無ければ「iPhone直接確認不可」表示+actionHintバッジ。localhost/内部ポート/未確認枠は `<details>` 折りたたみ+「iPhone直接不可」バッジ。iPhone確認可否バッジ。すべて/iPhoneで見れる/直接不可/未確認のフィルタ+件数、ok→blocked→unknown順ソート。
  - `app/app-urls/page.tsx`: enrichして件数内訳とiPhone確認用一覧である旨のヘッダーを出し、AppUrlsBoardへ委譲する薄いサーバーコンポーネントに作り直し。
- 関連調査・判断:
  - データ(data/real/app-urls.json)は当日先行で既に localhost:3000 の根拠なし登録を除去済みだった。今回は「データ」ではなく「分類ロジックとUI表示」に欠落があったと特定し、そちらを修正。
  - 分類はURL host基準にしたため、kind=vps でも host が localhost なら blocked になる(NetScopeが該当)。これは「VPS内部からしか届かない」実態と一致。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/app-urls.ts | IphoneAccess型・classifyRecordAccess・enrichApp/enrichAppUrls 追加 |
| progress/components/app-urls/AppUrlsBoard.tsx | 新規。iPhone確認URL最優先表示・localhost折りたたみ・各種バッジ・フィルタ |
| progress/app/app-urls/page.tsx | enrich+AppUrlsBoard描画の薄いサーバーコンポーネントに作り直し |

---

## 4. 検証結果

- typecheck: OK（npx tsc --noEmit exit0）
- build: OK（npx next build 成功 / /app-urls 2.78kB ƒ dynamic）
- lint: OK（No ESLint warnings or errors）
- 手動確認: 実データ12件で分類を確認。NetScope=blocked(primaryなし)/progress=blocked/news-app=unknown。localhostが確認URL(primary)に昇格しないことを確認。
- 機密パターン事前チェック: OK（差分に機密ヒットなし）
- ob sync: Fully synced（本レビュー保存時）
- git push: d5a8977（9e6af71..d5a8977 origin main）

---

## 5. 未対応

- iPhone確認OK(公開URL所持)のアプリは現状0件。これは「推測でURLを作らない」方針の結果であり、実際に公開URLを確認/Vercel化する作業は別タスク。
- pm2本番(progress 3010)は旧ビルドのままメモリ常駐。新UIを本番反映するには pm2 restart が必要（restartはユーザー判断に委ねる）。
- Vercel API 403 で deployment URL が取れないアプリ(shogi-kakoi-trainer / news-app / map)のVercel URLは未確認のまま。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。app-urls 画面のみの変更で他ルートに影響なし。データスキーマは非破壊(既存フィールドのみ参照、derive追加)。
- DB / 認証 / 本番 / 機密に触れたか: なし。
- ロールバック手段: git revert d5a8977（3ファイルのみ）。
- 観察すべき項目: iPhoneでフィルタ操作・折りたたみが正しく動くか。pm2 restart後に本番で確認URLが正しく出るか。

---

## 7. 次にやるべきこと

- フォローアップ作業: 公開URLが必要なアプリ(NetScope/progress/news-app等)のVercel化または公開VPS URL確認。確認できたら data/real/app-urls.json に公開URLを追加すると自動でiPhone確認OKに昇格する。
- ユーザー判断待ち事項: progress 本番への pm2 restart 反映可否。
- 観察項目: iPhone実機でのフィルタ/折りたたみUXとバッジ視認性。
- 関連 ToDo の追加候補: 「主要アプリのVercel化/公開URL整備」Epic。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress
作業: アプリURL一覧をiPhone確認用に再設計（localhost非表示・確認URL最優先・フィルタ追加）
runId: 20260608-235115
日付: 2026-06-08
GitHub commit: d5a8977

## 作業目的
- progressの「アプリURL」一覧はiPhoneから動作確認するための一覧だが、多くのアプリがlocalhostを主要URL表示しiPhoneから開けなかった。iPhoneで押せる公開URLだけを確認URLとして上部表示し、localhostは補助情報に隔離する。

## 実施内容
- lib/app-urls.tsにiPhone到達性分類(classifyRecordAccess: host=localhost/loopback/private IP/ssh_port_forward→blocked, 未確認→unknown, 公開→ok)とenrichApp(公開URL選好でprimary決定・actionHint生成)を追加
- AppUrlsBoard.tsx新規(iPhone確認URL最優先・localhost折りたたみ・iPhone直接不可/確認可否バッジ・すべて/見れる/直接不可/未確認フィルタ)
- page.tsxをenrich+board委譲の薄いサーバーコンポーネントに作り直し

## 変更ファイル
- progress/lib/app-urls.ts
- progress/components/app-urls/AppUrlsBoard.tsx
- progress/app/app-urls/page.tsx

## 検証結果
- typecheck/build/lint: 全OK
- 手動確認: 実データ12件分類確認。NetScope/progress=blocked(localhost確認URL非表示), news-app=unknown
- 機密スキャン: ヒットなし

## 未対応
- iPhone確認OK(公開URL)アプリは現状0件(推測URL不作成のため)
- pm2本番3010は要restart(新UI未反映)

## 危険ポイント
- app-urls画面のみ・データ非破壊・revertはd5a8977のみ

## 次にやるべきこと
- 主要アプリのVercel化/公開URL確認→data/real/app-urls.jsonに追記で自動昇格

## 確認したい観点
- host基準分類(kind=vpsでもlocalhostならblocked)の妥当性
- 公開URL選好順(vercel>vps>api)とactionHint文言の妥当性
- 未確認を推測で埋めない方針の徹底ができているか
- 既存機能を壊していないか
````

---

## 関連

- progress runId: 20260608-235115（正本）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
