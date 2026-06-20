---
date: 2026-06-20
task: 麻雀アプリをCapacitorでiOS化＋Codemagic(TestFlight)ビルド準備
runId: 20260620-215415
targetApp: mahjong
monetizationImpact: medium
theme: [mahjong, app-strategy, workflow]
relatedRunIds: []
commitHashes: []
---

# 2026-06-20 麻雀アプリ iOS化＋Codemagic(TestFlight)準備

## 1. 作業目的
- レンタルMac無しで、Codemagicのクラウドmac で iOS(TestFlight)ビルドできる構成を準備する（公開ではなくTestFlight到達がゴール）。
- 対象: `/root/mahjong/mahjong`（Next.js 16・GitHub kaeru07/mahjong）。

## 2. 実施内容（branch: ios-codemagic-test）
- 現状確認: Next.js 16.2.3 / React 19 / App Router、画面 `/ /quiz /questions /import /result`、questions.json 10問、API route無し・全8ページStatic prerender、build/tsc通過。
- 静的出力: `next.config.ts` に `output:'export'` / `images.unoptimized` / `trailingSlash` を統合 → `out/`(8html)生成確認。
- Capacitor: `@capacitor/core,cli,ios` 8.4.1 導入、`capacitor.config.ts`（appId `com.kaeru07.mahjong` / appName `Mahjong` / webDir `out`）。
- iOS生成: `npx cap add ios` / `cap sync` 成功。**重要: Capacitor 8 は CocoaPods でなく SwiftPM 構成**（Podfile/.xcworkspace 無し・`ios/App/App.xcodeproj` ＋ `CapApp-SPM/Package.swift`）。Bundle ID = `com.kaeru07.mahjong` 確認。
- 共有スキーム不在だったため `ios/App/App.xcodeproj/xcshareddata/xcschemes/App.xcscheme` を新規作成（CIでscheme `App` を解決可能に）。
- `codemagic.yaml`: mac_mini_m2 / xcode latest / node 22、ios_signing(app_store/bundle id)、scripts = npm ci → npm run build → npx cap sync ios → xcode-project use-profiles → xcode-project build-ipa(`--project ios/App/App.xcodeproj --scheme App`)、artifacts = ipa + xcodebuild log + app、publishing.app_store_connect `auth: integration` / `submit_to_testflight: true`。**秘密情報は非記載**・App Store Connect integration はUI登録前提。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| next.config.ts | output:export / images.unoptimized / trailingSlash |
| capacitor.config.ts | 新規(appId/appName/webDir out) |
| codemagic.yaml | 新規(SPM対応 build-ipa / ASC integration / TestFlight) |
| ios/（25ファイル） | Capacitor iOSプロジェクト(SwiftPM)＋共有スキーム |
| package.json / package-lock.json | @capacitor/core,cli,ios 8.4.1 |

## 4. 検証結果
- npm ci: OK / npm run build: OK(out/8html) / npx cap sync ios: OK
- ios/App.xcodeproj・共有スキーム存在・webDir=out・ios public/index.html 同期確認
- 秘密情報スキャン: 実値なし（コメント行と integration 名 `ASC_API_KEY` 参照のみ）
- git: 自分の変更のみcommit、既存の無関係変更(BoardView.tsx/AGENTS.md/CLAUDE.md)は除外。ios配下 public/・生成config は gitignore で非コミット(CIで再生成)
- push: `git push -u origin ios-codemagic-test` 成功（kaeru07/mahjong）

## 5. 未対応（ConoHaでは不可・Codemagic/ユーザー側）
- 実iOSビルド/署名/IPA生成/TestFlightアップロード（Codemagic クラウドmac）
- Apple Developer登録 / App Store Connect API Key(.p8)登録 / Codemagic UI設定（ユーザー手動）

## 6. 危険ポイント
- 秘密鍵/Apple ID/.p8/パスワードは要求・記載なし。本番公開/課金なし。TestFlightまで=公開ではない。
- Capacitor 8 が CocoaPods でなく SPM の点が、CocoaPods前提の一般手順と異なる（yamlはSPMに合わせ済み）。

## 7. 次にやるべきこと
- Codemagicで kaeru07/mahjong を連携 → ios-codemagic-test を選択
- App Store Connect API Key を Codemagic に登録し、integration名を codemagic.yaml の `ASC_API_KEY` に一致させる
- App Store Connect に Bundle ID `com.kaeru07.mahjong` のアプリ作成
- Start build → IPA生成 → TestFlight到達を確認（失敗時は xcodebuild log artifact を確認）

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: mahjong (Next.js16 + Capacitor8 iOS)
作業: Codemagicで iOS(TestFlight)ビルドする準備
runId: 20260620-215415 / branch: ios-codemagic-test (kaeru07/mahjong)

## 実施
Next静的書き出し(out/)→Capacitor8でiOS化(SwiftPM構成・CocoaPods不要)。共有スキーム
App.xcscheme追加。codemagic.yaml(mac_mini_m2/xcode latest/node22、npm ci→build→
cap sync→use-profiles→build-ipa、ASC integration認証でTestFlight)。秘密情報非記載。

## 検証
npm ci/build/cap sync すべてOK。out/8html・ios/App.xcodeproj・webDir out確認。

## 確認したい観点
1. Capacitor8のSwiftPM構成で codemagic.yaml の build-ipa(--project/--scheme App)は妥当か。
2. cocoapods:default を残したのは無害か(Podfile無し)。
3. ios_signing(app_store)＋publishing auth:integration でTestFlight到達まで足りるか。
4. Next16 + output:export の WebView 表示で注意点はあるか(trailingSlash/相対パス等)。
````
