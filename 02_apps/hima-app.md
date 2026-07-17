# hima-app（暇潰し！）

iOS App Store公開を目指すCapacitorアプリ。リポジトリ: kaeru07/hima-tsubushi-app（VPS: `/root/company/apps/hima-app`）。

- Bundle ID: `jp.kaeru07.himatsubushi` / SKU: himatsubushi-001
- リリース自動化: `scripts/ios-release.mjs`（check / prepare / build / status）。設定の正は `release/ios-app.json`
- CI: Codemagic workflow `ios-appstore`（TestFlight自動送信・審査提出は手動）

## 直近の更新

- 2026-07-17 runId: 20260717-091246 — Codemagic再発行トークンでビルド自動化が開通。build#2（v1.0.0）が全ステップ成功しTestFlightへアップロード完了（関連: [[../20_reviews/2026-07-17_codemagic-build-testflight-success]]）
- 次の確認事項: App Store ConnectのTestFlightでビルド#2の処理完了（processing→ready）をiPhoneから確認する
