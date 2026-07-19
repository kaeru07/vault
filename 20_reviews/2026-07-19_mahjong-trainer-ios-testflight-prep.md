---
date: 2026-07-19
task: ny01/mahjong-trainer iOS TestFlight準備
app: ny01/mahjong-trainer
status: partial
runId: 20260719-182259
---

# 2026-07-19 mahjong-trainer iOS TestFlight準備

## 結果
- `/root/company/apps/ny01/mahjong-trainer` を対象に、Next.js静的export + Capacitor iOS + Codemagic workflow を追加。
- Codemagic app `ny01` を新規作成し、workflow `mahjong-trainer-ios-testflight` を起動可能にした。
- `main` へ push 済み。最新コミットは `b403218`。

## 確認
- `npm run lint`: OK
- `npm run build`: OK
- `npx cap sync ios`: OK
- Codemagic build #3: Next build / Capacitor sync / version設定まで成功。
- Codemagic build #4: 現在のHEADで `MAHJONG_TRAINER_APP_STORE_PROFILE` 未登録により停止。

## 残ブロッカー
- Bundle ID `com.kaeru07.mahjongtrainer` 用の App Store provisioning profile が Codemagic に未登録。
- Reference name は `MAHJONG_TRAINER_APP_STORE_PROFILE`。
- App Store Connect app record が未作成なら先に作成が必要。

## 次アクション
- Codemagic Code signing identities に `MAHJONG_TRAINER_APP_STORE_PROFILE` を追加。
- その後、Codemagic app `ny01` の `mahjong-trainer-ios-testflight` を Rebuild。
- TestFlightアップロード後、iPhoneで起動・対局・待ち読み記録・局後確認を実機確認。
