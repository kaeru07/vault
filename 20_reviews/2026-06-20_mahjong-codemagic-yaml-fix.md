---
date: 2026-06-20
task: codemagic.yaml更新(workflow ios-capacitor-test)＋No configuration file found解消
runId: 20260620-231106
targetApp: mahjong
monetizationImpact: medium
theme: [mahjong, app-strategy, workflow]
relatedRunIds: [20260620-215415]
commitHashes: [219c1a2]
---

# 2026-06-20 codemagic.yaml更新＋"No configuration file found"解消

## 1. 作業目的
- Codemagicが Branch:main を見て「No configuration file found」になる問題の解消。原因は codemagic.yaml が `ios-codemagic-test` ブランチのみで main に無いこと。
- ユーザー新仕様: workflow名 `ios-capacitor-test`、署名スクリプト・TestFlight。

## 2. 実施内容
- `codemagic.yaml` 刷新: workflow id `ios-capacitor-test` / name "iOS Capacitor Test"、mac_mini_m2 / xcode latest / node latest / cocoapods default。`integrations.app_store_connect: ASC_API_KEY`(プレースホルダ)追加。vars BUNDLE_ID / XCODE_PROJECT(`ios/App/App.xcodeproj`) / XCODE_SCHEME(`App`)。
- scripts: npm ci||npm install → npm run build → npx cap sync ios → 署名(`keychain initialize` / `app-store-connect fetch-signing-files $BUNDLE_ID --type IOS_APP_STORE --create` / `keychain add-certificates` / `xcode-project use-profiles`) → `xcode-project build-ipa --project $XCODE_PROJECT --scheme App`。
- artifacts: ipa + xcodebuild log。publishing app_store_connect `auth: integration` / `submit_to_testflight: true`。
- **重要差異**: Capacitor 8 は SwiftPM 構成で `App.xcworkspace` が無いため、ユーザーテンプレの `--workspace` を `--project` に変更（コメントで明記）。
- 秘密情報非記載（.p8 / Issuer ID / Key ID / 証明書なし）。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| mahjong/codemagic.yaml | workflow ios-capacitor-test へ刷新・SPM対応(--project)・integrations追加・初回向け署名(--create)・TestFlight |

## 4. 検証結果
- npm run build: OK(out/index.html) / npx cap sync ios: OK
- codemagic.yaml リポジトリ直下・workflow ios-capacitor-test・integrations有・build-ipa は --project・秘密情報スキャン clean
- commit "Add Codemagic iOS build configuration" → `git push origin ios-codemagic-test` 成功(219c1a2)。origin/ios-codemagic-test に codemagic.yaml 存在確認

## 5. 未対応
- 実iOSビルド/署名/IPA/TestFlight(Codemagicクラウドmac)。Apple Developer / ASC API Key登録(ユーザー手動)。
- main には未反映(意図的・ios-codemagic-testで検証してからmerge)。

## 6. 危険ポイント
- 秘密情報なし・本番公開/課金/審査提出なし。SwiftPM構成のためxcworkspace前提の一般手順と異なる(yamlはSPM対応済み)。

## 7. 次にやるべきこと（ユーザー）
- CodemagicでBranchを ios-codemagic-test に変更 → Check for configuration files → workflow ios-capacitor-test 表示 → ASC integration選択(名前を ASC_API_KEY に一致) → Start build。
- mainでビルドしたい場合は ios-codemagic-test を main へ merge。

## 8. ChatGPT レビュー依頼文
````text
対象: mahjong (Next.js16 + Capacitor8 iOS) Codemagic設定
runId: 20260620-231106 / branch ios-codemagic-test (kaeru07/mahjong) / commit 219c1a2

## 状況
Codemagicが main を見て No configuration file found。yamlは ios-codemagic-test のみ。
workflow ios-capacitor-test に刷新。Capacitor8がSwiftPMのため build-ipa は --workspace
でなく --project ios/App/App.xcodeproj --scheme App。署名は fetch-signing-files --create、
publishing auth:integration で TestFlight。秘密情報非記載。

## 確認したい観点
1. SwiftPM構成で --project ビルドは妥当か(xcworkspaceを作るべきか)。
2. fetch-signing-files --create + integration でTestFlight初回到達に十分か。
3. main に置かず ios-codemagic-test 運用で良いか(Codemagic側Branch切替前提)。
````
