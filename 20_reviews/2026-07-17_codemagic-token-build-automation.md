---
date: 2026-07-17
task: Codemagic APIトークン連携でiOSビルド自動化を有効化（トークン401で保留）
runId: 20260717-000559
targetApp: hima-app
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: [c398942]
# reviewFileCommit:
---

# 2026-07-17 Codemagic APIトークン連携でiOSビルド自動化を有効化

---

## 1. 作業目的

- なぜこの作業をするのか: ユーザーが `/root/.secrets/appstore/codemagic.env` に `CODEMAGIC_API_TOKEN` を保存したため、既存のiOSリリース自動化パイプライン（hima-app `scripts/ios-release.mjs`）からCodemagicビルドを起動できる状態にする
- 背景: hima-app には check / prepare / build / status のCLIが実装済み。トークン配置がビルド起動・監視を有効化する最後のピースだった
- 期待効果: `npm run ios:release:build` 一発でCodemagic上のiOS App Storeビルド（TestFlight送付まで）を起動できる

## 2. 実施内容

- トークンファイルを既存パイプラインが読む設計であることを確認（`loadSecrets()` が `/root/.secrets/appstore/codemagic.env` を参照）
- `npm run ios:release:check` を実行し、2つの問題を検出
  1. 秘密スキャンの誤検出: スキャンパターン `/x-auth-token:\s*\S{10,}/` がスクリプト自身のパターン定義行に自己マッチ → リテラル分割（`new RegExp('x-auth-' + 'token:...')`）で修正
  2. Codemagic API 401: トークンが認証されない
- トークン診断（値は非表示）: 63文字・引用符/CR/空白なし・base64url文字のみ → **ファイル形式は正常**
- Codemagic API `GET /apps` を node fetch と curl（`x-auth-token` / `Authorization: Bearer` 両方式）で試行 → すべて HTTP 401 → **トークン値自体が無効**（コピー誤り・失効・種類違いのいずれか）と判断

## 3. 変更ファイル

- `apps/hima-app/scripts/ios-release.mjs` — 秘密スキャンの x-auth-token パターンを自己マッチしない形に修正（1行）

## 4. 検証結果

- `ios:release:check`: 設定 / Git / codemagic.yaml / capacitor.config / 秘密スキャン（9ファイル クリーン）全緑。Codemagic API のみ401
- 機密スキャン: 実値の露出なし（診断スクリプトも値を一切出力しない設計）
- ob sync: n/a（一旦中止中）
- git push: hima-app `ios-release-automation` へ c398942 push済み

## 5. 未対応

- Codemagicビルド起動（`ios:release:build`）— トークン401のため実行不可
- ASCキー（`asc.env` / `asc_key.p8`）未配置 — Apple側検査・TestFlight状況確認が未有効
- Codemagicへのリポジトリ接続確認（トークン有効化後に `check` で判定される）

## 6. 危険ポイント

- トークン値はチャット・リポジトリ・ログに一切出していない（長さ・文字種の判定のみ）
- ビルド起動はCodemagicのビルド時間（mac_mini_m2）を消費する。無料枠超過時は課金に関わるため、起動は最小回数にする

## 7. 次にやるべきこと

1. **ユーザー**: Codemagic UI（User settings → Integrations → Codemagic API → Show）でトークンを再確認・再発行し、`codemagic.env` の値を差し替える
2. トークン有効化後: `npm run ios:release:check` 全緑確認 → リポジトリ未接続なら https://codemagic.io/apps で Add application（初回のみ）→ `npm run ios:release:build`
3. branch不一致の解消: `release/ios-app.json` は `branch: master` だが作業は `ios-release-automation`。ビルド前にmasterへマージするか設定を変える（ユーザー判断）

## 8. ChatGPT レビュー依頼文

```
hima-app（暇潰し！iOSアプリ）のCodemagicビルド自動化の検証結果をレビューしてください。
runId: 20260717-000559 / commit: c398942（ios-release-automation）

状況:
- iOSリリース自動化CLI（check/prepare/build/status）は実装済み。CODEMAGIC_API_TOKENを配置したがAPIが401で拒否
- トークンのファイル形式は正常（63文字・余分な文字なし）と診断済み。x-auth-token / Bearer 両方式で401 → 値自体が無効と判断
- 副産物として秘密スキャンの自己マッチ誤検出を修正

確認したい観点:
1. 「トークン値が無効」という切り分けは妥当か。他に401の原因はあり得るか（Codemagicのチーム/個人アカウント差異など）
2. ビルド起動前にbranch不一致（設定master vs 作業ios-release-automation）をどう解消すべきか
3. ビルド時間の課金リスク管理として最初の1回をどう最小化するか
```
