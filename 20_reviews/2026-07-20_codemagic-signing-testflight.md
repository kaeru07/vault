---
date: 2026-07-20
task: Codemagic iOS署名の完全自動化 — 麻雀トレーナー TestFlight アップロード成功
runId: 20260720-020756
targetApp: mahjong-trainer
monetizationImpact: medium
theme: [workflow, mahjong, app-strategy]
relatedRunIds: [20260719-234712]
commitHashes: [994d987, 6f8c7fa, 47f9895, 886763f, f44d455, 231ccc3, a4ac26a]
---

# 2026-07-20 Codemagic iOS署名の完全自動化 — TestFlight アップロード成功

---

## 1. 作業目的

- なぜ: Codemagic の管理型自動署名（前 run で導入）が「No matching profiles found」で失敗し続けたため、根本原因を特定して署名を完全自動化する
- 背景: ユーザー要望は「プロファイルのダウンロード/アップロードを一切せず自動で」。暇つぶしアプリは手動署名（プロファイル UI アップロード）だったため同じ手は使いたくない
- 期待効果: 新規アプリごとの署名セットアップがゼロタッチになる（麻雀トレーナーが最初の適用例）

## 2. 実施内容

- 診断で確定した事実:
  - Bundle ID `com.kaeru07.mahjongtrainer` は Apple 側に登録済みだった
  - 配布証明書は新形式 Apple Distribution（タイプ `DISTRIBUTION`）1枚のみ。旧 `IOS_DISTRIBUTION` フィルタでは 0 件になる
  - ASC API キーにプロファイル作成権限あり（`profiles create` 成功、Apple 側 ID 6J5SFFSP8J）
  - プロファイルを作成しても管理型 `ios_signing` は失敗 → Codemagic 側に対応する証明書秘密鍵が無いことが原因と判断
- 最終方式（cli-tools 方式）:
  - `openssl genrsa 2048` で専用秘密鍵を生成し `/root/.secrets/appstore/mahjong_cert_private_key.pem` に保管（600）
  - Codemagic API `POST /apps/{id}/variables` で secure 変数 `CERTIFICATE_PRIVATE_KEY`（group: `mahjong_signing`）として登録
  - workflow の署名ステップを `keychain initialize` → `app-store-connect fetch-signing-files --create` → `keychain add-certificates` → `xcode-project use-profiles` に変更
- 診断用に `register-bundle-id` ワークフローを追加（Bundle ID 確認・プロファイル作成に使用。現在は不要）

## 3. 変更ファイル

- `apps/ny01/codemagic.yaml` — 管理型 ios_signing → cli-tools 署名方式 / register-bundle-id ワークフロー追加（commits 994d987〜a4ac26a、全 push 済み）

## 4. 検証結果

- Codemagic 実ビルド buildId 6a5d0037: 全ステップ success（Next.js build / cap sync / 署名 / IPA 生成 / Publishing）
- App.ipa 生成・App Store Connect アップロード・処理完了（ASC build 224191a7）
- ASC アプリ枠「麻雀実戦読みトレーナー」(ID 6792473618) は作成済みと判明
- 機密スキャン: 秘密鍵の実値はチャット・リポジトリに未出力（.secrets 保管 + Codemagic secure 変数のみ）

## 5. 未対応

- 外部テスター向けベータ審査の自動提出のみ失敗: Beta App Information（Feedback Email）と Beta App Review Information（氏名・電話・メール）が ASC 未入力のため
- 入力はユーザーの個人情報を含むため代行せず、ユーザー操作とする

## 6. 危険ポイント

- Apple アカウントに証明書とプロファイルが 1 組増える（fetch-signing-files --create が生成）。既存の暇つぶし用署名には影響しない（revoke はしていない）
- `CERTIFICATE_PRIVATE_KEY` は Codemagic secure 変数と VPS の .secrets のみに存在。漏洩時は該当証明書の revoke で無効化可能

## 7. 次にやるべきこと

- ユーザー: https://appstoreconnect.apple.com/apps/6792473618/testflight/test-info で Feedback Email / 氏名 / 電話 / メールを入力（iPhone で 2 分）
- 以降のビルドは再実行だけで外部テスター審査提出まで自動。内部テストは現ビルドで既に可能
- 任意: 用済みの register-bundle-id ワークフロー削除
- 横展開: 次の新規アプリはこの cli-tools 方式をテンプレ化すればゼロタッチ（ios-release.mjs の手動アップロード手順は不要になる）

## 8. ChatGPT レビュー依頼文

```
Codemagic iOS 署名の自動化構成をレビューしてください。
対象: kaeru07/ny01 codemagic.yaml（commit a4ac26a）/ runId 20260720-020756
構成: ASC APIキー統合 + secure変数の専用RSA鍵で
keychain initialize → app-store-connect fetch-signing-files --create →
keychain add-certificates → xcode-project use-profiles。
結果: IPA生成〜TestFlightアップロード成功（外部テスター審査提出のみテスト情報未入力で保留）。
確認したい観点:
1. 管理型 ios_signing ではなく cli-tools 方式を選んだ判断の妥当性
2. 秘密鍵の保管（VPS .secrets + Codemagic secure変数）のリスク許容度
3. 新規アプリへの横展開時にテンプレ化すべき項目の漏れ
```
