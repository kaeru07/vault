---
date: 2026-06-21
task: CodemagicビルドIPA失敗(status65/provisioning profile)の署名調査・yaml堅牢化
runId: 20260621-124626
targetApp: mahjong
monetizationImpact: medium
theme: [mahjong, app-strategy, workflow]
relatedRunIds: [20260620-231106]
commitHashes: [f060cf8]
---

# 2026-06-21 Codemagic Build IPA 署名失敗(status65)の調査・修正

## 1. 作業目的
- Build IPA が status65 `App requires a provisioning profile` で失敗。レンタルMac無しで ConoHa+GitHub+Codemagic で TestFlight 成功まで進めたい。秘密情報は要求/記載しない。

## 2. 原因
- このエラーは **自動署名(CODE_SIGN_STYLE=Automatic)のまま archive した時の典型**。App ターゲットは自動署名で、`xcode-project use-profiles` が取得済みプロファイルを適用→管理署名へ変換する前提だが、**適用すべき App Store 用プロファイルが取得できていない(0件)** ため自動署名のまま archive → 失敗。

## 3. 調査結果（ユーザー指定チェック項目）
- Bundle ID: pbxproj / capacitor.config.ts / yaml BUNDLE_ID すべて `com.kaeru07.mahjong` 統一 ✓
- pbxproj 署名: App ターゲット=Automatic・**PROVISIONING_PROFILE 手書き固定なし・手動署名固定なし**(OK・編集不要)
- アプリ位置: package.json/ios/codemagic.yaml はリポジトリ直下(サブディレクトリ無し→cd不要) ✓
- 共有スキーム: App.xcscheme コミット済み ✓
- build-ipa: SwiftPM のため `--project ios/App/App.xcodeproj --scheme App`(--workspace不使用)で実構成と一致 ✓
- integration名: `app_store_connect: ASC_API_KEY` ✓(API Key 実体はユーザーが Codemagic UI 登録)

## 4. 修正内容（codemagic.yaml のみ）
- Set up code signing: `fetch-signing-files` に `--platform IOS` を明示。`use-profiles` 後に Provisioning Profiles を `ls` し、**0件なら原因(API Keyロール App Manager/Admin要・Bundle ID/配布証明書上限)を明示して exit1**。→ 自動署名のまま archive して謎エラーになる前に署名段階で止め、ログで切り分け可能に。
- Build IPA: `set -e` 追加。`--project/--scheme` 据え置き。
- **pbxproj は編集せず**(標準 Capacitor レシピ通り use-profiles に署名を委譲・手動固定しない)。

## 5. 検証結果
- YAML parse: workflow ios-capacitor-test / integration ASC_API_KEY / --project(--workspace無) / --platform IOS / guard / submit_to_testflight すべてOK。秘密情報スキャン clean。Bundle ID 統一確認。
- git push origin ios-codemagic-test 成功(f060cf8)・ブランチ直下に codemagic.yaml 存在確認。

## 6. 未対応（ConoHa不可・ユーザー側）
- 実 archive/署名/IPA/TestFlight(Codemagic クラウドmac・ユーザーの API Key 設定依存)。

## 7. 次にやるべきこと（ユーザー）
- Codemagic で Branch `ios-codemagic-test` を選び **Rebuild**。署名ステップのログで `fetched provisioning profiles` を確認。
- 0件エラーが出たら: ASC_API_KEY の API Key ロールを **App Manager/Admin** に / Bundle ID `com.kaeru07.mahjong` のアプリを App Store Connect に用意 / 配布証明書の上限(2)を確認。

## 8. ChatGPT レビュー依頼文
````text
対象: mahjong Codemagic iOS署名
runId: 20260621-124626 / branch ios-codemagic-test / commit f060cf8
Build IPA status65『requires a provisioning profile』。原因=use-profilesが適用する
App Store用プロファイル未取得で自動署名のままarchive。yamlに--platform IOS＋
プロファイル未取得を明示エラー化＋切り分けログを追加。pbxprojは自動署名のまま(編集なし)。
確認観点: 標準use-profilesレシピでProvisioningStyle=Automatic(TargetAttributes)が
archiveで悪さしないか / fetch-signing-files --create が配布証明書も作るか / 他に署名で
詰まる点はないか。
````
