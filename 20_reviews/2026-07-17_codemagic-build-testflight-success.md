---
date: 2026-07-17
task: Codemagic新トークンでiOSビルド自動化が開通しTestFlightアップロード成功
runId: 20260717-091246
targetApp: hima-app
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: [24935d1, be7f15e]
# reviewFileCommit:
---

# 2026-07-17 Codemagic新トークンでiOSビルド自動化が開通しTestFlightアップロード成功

> 前件 [[2026-07-17_codemagic-token-build-automation]]（旧トークン401・再発行待ち）の後続。再発行トークンで自動化が開通した。

---

## 1. 作業目的

- なぜ: ユーザーが再発行した CODEMAGIC_API_TOKEN を `/root/.secrets/appstore/codemagic.env` に保存。これを使ってiOSビルド自動化（Codemagic起動→TestFlight）を完成させる
- 背景: 前件で旧トークンが401拒否と切り分け済み。CLI（ios-release.mjs）と codemagic.yaml は準備済みだった
- 期待効果: `npm run ios:release:build` 一発でTestFlightまで届く状態の確立

## 2. 実施内容

- `check` で新トークンの有効性を確認: Codemagic API接続OK・hima-app接続済み（appId 6a539fd6a717d4f35c3edc49）
- `CODEMAGIC_APP_ID` を codemagic.env へ追記（README.txt記載の任意項目・値は非表示運用のまま）
- `ios-release-automation` ブランチ（CLI/docs/release設定）を master へマージしpush（24935d1）。codemagic.yaml は両ブランチ同一を確認してからマージ
- ビルド1回目（buildId 6a590c06b01234168bc4f3c0）: 署名・Build IPAまで全成功、Publishing のみ **ASCエラー90189 Redundant Binary Upload**（build#1は過去試行でアップロード済み）で失敗
- `release/ios-app.json` buildNumber 1→2 → `prepare` でyaml再生成 → commit be7f15e push
- ビルド2回目（buildId 6a590d3bec6a555199371b77）: **全12ステップ success・App.ipa(1MB) を App Store Connect へアップロード完了**（2026-07-17 01:59 JST）

## 3. 変更ファイル

- `apps/hima-app/release/ios-app.json` — buildNumber 1→2
- `apps/hima-app/codemagic.yaml` — prepareで再生成（BUILD_NUMBER=2注入）
- `/root/.secrets/appstore/codemagic.env` — CODEMAGIC_APP_ID追記（git外・実値は一切表示せず）
- masterへのマージで scripts/ios-release.mjs / docs/ios-release.md / release/ios-app.json / package.json / .gitignore が master に追加

## 4. 検証結果

- yaml lint: 全項目OK / 秘密情報スキャン: 9ファイル クリーン
- Codemagicビルド: finished（Preparing〜Cleaning up 全12ステップ success）
- TestFlightアップロード: Publishing success（altool エラーなし）
- 過去の失敗（status65 provisioning profile / integration名不一致）は再発なし。手動reference署名方式が機能

## 5. 未対応

- TestFlight側の処理状況（processing→ready）はASCキー未配置のためAPI監視不可。App Store Connect UIでの確認が必要
- 審査提出・公開は自動化スコープ外（従来方針どおり手動）

## 6. 危険ポイント

- トークン実値はチャット・ログ・リポジトリに出していない（keys onlyでgrep確認）
- Codemagicビルドはmacインスタンスのビルド時間を消費（2回で約6分）。無料枠内だが再ビルド乱発は注意

## 7. 次にやるべきこと

- App Store ConnectでTestFlight処理完了とビルド#2の表示を確認（iPhoneから可）
- `asc.env` / `asc_key.p8` を配置すると `status` コマンドでTestFlight処理監視・apple-ensureが有効化
- mahjongアプリへ同CLIを横展開（docs/ios-release.md「新しいアプリを追加するとき」3ステップ）

## 8. ChatGPT レビュー依頼文

```
hima-app（暇潰し！iOSアプリ）のビルド自動化が開通しました。レビューをお願いします。
runId: 20260717-091246 / commits: 24935d1(CLIをmasterへマージ), be7f15e(buildNumber 2)

経緯: 再発行されたCodemagic APIトークンで、CLI(ios-release.mjs)からビルド起動→1回目はASC 90189(build#1重複アップロード)で失敗→buildNumber 2へ更新して再ビルド→全ステップ成功、App.ipaがTestFlightへアップロード完了。

確認したい観点:
1. buildNumberの管理方式: release/ios-app.jsonの手動インクリメントで運用開始したが、CI側で自動インクリメント(例: ASC APIから最新build番号+1)にすべきか
2. ブランチ運用: ios-release-automationをmasterへマージしたが、今後のリリース作業はmaster直運用でよいか
3. TestFlight以降の手動工程(処理確認・審査提出)の切り分けは妥当か
```
