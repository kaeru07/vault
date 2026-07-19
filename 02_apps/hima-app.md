# hima-app（暇潰し！）

iOS App Store公開を目指すCapacitorアプリ。リポジトリ: kaeru07/hima-tsubushi-app（VPS: `/root/company/apps/hima-app`）。

- Bundle ID: `jp.kaeru07.himatsubushi` / SKU: himatsubushi-001
- リリース自動化: `scripts/ios-release.mjs`（check / prepare / build / status）。設定の正は `release/ios-app.json`
- CI: Codemagic workflow `ios-appstore`（TestFlight自動送信・審査提出は手動）

## 直近の更新

- 2026-07-19 runId: 20260719-163024 — 全画面のiOSセーフエリア対応で上寄りを解消し、ランキング画面も縦中央化。build#4(1.0.0(4))をTestFlightへアップロード完了（関連: [[../20_reviews/2026-07-19_hima-safe-area-build4]]）
- 次の確認事項: iPhone TestFlightでビルド4へ更新し、ホーム/プレイ/結果/ランキングの4画面でノッチ回避と中央寄せを確認

- 2026-07-18 runId: 20260718-173245 — 結果画面を縦中央寄せに修正しbuild#3(1.0.0(3))をTestFlightへアップロード完了（関連: [[../20_reviews/2026-07-18_hima-result-screen-center]]）
- 次の確認事項: iPhone TestFlightでビルド3へ更新し、クリア後画面が中央に表示されるか確認

- 2026-07-17 runId: 20260717-091246 — Codemagic再発行トークンでビルド自動化が開通。build#2（v1.0.0）が全ステップ成功しTestFlightへアップロード完了（関連: [[../20_reviews/2026-07-17_codemagic-build-testflight-success]]）
- 次の確認事項: App Store ConnectのTestFlightでビルド#2の処理完了（processing→ready）をiPhoneから確認する
