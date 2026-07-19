---
date: 2026-07-19
task: Codemagic iOS署名を自動署名方式へ切替（mahjong-trainer）
runId: 20260719-234712
targetApp: mahjong-trainer
monetizationImpact: low
theme: [workflow, mahjong]
relatedRunIds: []
commitHashes: [994d987]
---

# 2026-07-19 Codemagic iOS署名を自動署名方式へ切替

---

## 1. 作業目的

- なぜこの作業をするのか: ny01 の Codemagic ビルドがプロビジョニングプロファイルの箇所で停止していた。ユーザー要望は「できるだけ Codemagic 側で自動化し、プロファイルのダウンロード/アップロードをしない」こと
- 背景: 既存 codemagic.yaml が手動署名方式（Codemagic UI にアップロード済みの証明書・プロファイルを名前で参照する `certificates:` / `provisioning_profiles:` 指定）だったため、UI 側に実体が無く先へ進めなかった
- 期待効果: App Store Connect API キー連携による自動署名で、Codemagic が証明書とプロファイルを自動生成・取得。手動のプロファイル管理が不要になる

## 2. 実施内容

- 主な変更:
  - `codemagic.yaml` の `ios_signing` を手動参照から自動署名（`distribution_type: app_store` + `bundle_identifier: com.kaeru07.mahjongtrainer`）へ置換
- 関連調査・判断:
  - 選択肢: (1) Apple Developer でプロファイル作成→DL→Codemagic へ UL（手動） / (2) ASC API キー連携の自動署名
  - (2) を採用。ユーザー要望に合致し、`integrations.app_store_connect: ASC_API_KEY` が既に yaml に定義済みで差分最小のため
  - 既存の `xcode-project use-profiles` ステップは自動署名でもそのまま有効
- 付随対応: progress アプリ（3010）が `.next` 欠損でクラッシュループ（再起動2100回超）していたため `next build` を実行して復旧（pm2 操作はしていない）

## 3. 変更ファイル

- `apps/ny01/codemagic.yaml` — ios_signing を automatic code signing に変更（-4/+2 行）

## 4. 検証結果

- yaml diff 目視確認: OK
- ny01 repo: commit 994d987 → `git push origin main` 成功
- Codemagic 実ビルド: 未実施（ユーザーのビルドトリガー待ちのため runStatus: partial）
- progress アプリ: next build 成功・3010 応答復帰（401 = Basic 認証の正常応答）
- 機密スキャン: 本レビューに実値なし

## 5. 未対応

- Codemagic でのビルド再実行（ユーザー操作）
- ASC API キー統合の前提確認（下記 7. 参照）

## 6. 危険ポイント

- 自動署名は Apple Developer Portal に証明書・プロファイルを自動作成する（Apple アカウント側にリソースが増える）。既存の手動作成プロファイルと重複しても実害は小さいが、Portal 上で不要物が増える可能性あり
- ASC API キーの権限が不足していると `Fetching signing files` で別エラーになる

## 7. 次にやるべきこと

- Codemagic UI → Teams > Integrations で App Store Connect 統合の名前が `ASC_API_KEY` と一致しているか確認
- その API キーの権限が App Manager 以上か確認
- App Store Connect にアプリレコード（bundle id: com.kaeru07.mahjongtrainer）を作成済みか確認（TestFlight 公開に必須。API では作れないため手動 1 回）
- Codemagic でビルド再実行し、Fetching signing files ステップの成功を確認

## 8. ChatGPT レビュー依頼文

```
Codemagic の iOS 自動署名切替をレビューしてください。
対象: kaeru07/ny01 の codemagic.yaml（commit 994d987）/ runId 20260719-234712
変更: ios_signing を手動参照（certificates/provisioning_profiles 名前指定）から
distribution_type: app_store + bundle_identifier: com.kaeru07.mahjongtrainer の自動署名へ変更。
確認したい観点:
1. ASC API キー連携の自動署名として設定に不足はないか（integrations 名 ASC_API_KEY 前提）
2. Capacitor (Next.js static + ios/App/App.xcodeproj) 構成で xcode-project use-profiles → build-ipa の流れに落とし穴はないか
3. TestFlight 公開まで進めるのに他に必要な手動前提（ASC アプリレコード等）の漏れはないか
```
