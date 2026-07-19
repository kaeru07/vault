---
date: 2026-07-19
task: 麻雀アプリの当たり牌読みモードをTestFlightへ反映
runId: 20260719-173244
targetApp: mahjong
monetizationImpact: medium
theme: [mahjong, app-strategy, workflow]
relatedRunIds: [20260620-215415]
commitHashes: [ae7f66c, 3ec96db]
codemagicBuildId: 6a5c8a4ecdcd9fa083e88c5e
---

# 2026-07-19 麻雀アプリ 当たり牌読みモード TestFlight反映

## 1. 作業目的

- `main` 側にある当たり牌読み練習(`/yomi`)を iOS/TestFlight 確認用ブランチへ取り込む
- Codemagic で iOS IPA を作成し、TestFlight へアップロードする

## 2. 実施内容

- `ios-codemagic-test` に `origin/main` をマージし、`/yomi` 入口・クイズ・結果・レビュー画面と問題データ10問を取り込んだ
- 既存画面と `/yomi` 画面の React Hooks lint エラーを、lazy `useState` 初期化と keyed child component 分割で解消した
- Codemagic の build number 重複を避けるため、`BUILD_NUMBER=2` と `agvtool` 反映ステップを `codemagic.yaml` に追加し、Xcode project の `CURRENT_PROJECT_VERSION` も 2 へ更新した
- commit `ae7f66c` / `3ec96db` を `ios-codemagic-test` へ push した
- Codemagic workflow `ios-capacitor-test` を起動し、buildId `6a5c8a4ecdcd9fa083e88c5e` の Publishing success を確認した

## 3. 検証結果

- `npm run lint`: OK
- `node scripts/validate-yomi.mjs`: OK（10問、全件PASS）
- `npm run build`: OK（12 static routes、`/yomi` 系3画面を含む）
- `npx cap sync ios`: OK（`out` を `ios/App/App/public` へ同期）
- Codemagic: Preparing / Fetching / Signing / Install / Build / Sync / Version / IPA / Publishing / Cleanup が全て success

## 4. 未対応

- iPhone TestFlight 実機での `/yomi` 入口 → クイズ → 結果 → レビュー画面の操作確認
- App Store審査提出・公開は今回のスコープ外

## 5. 注意

- 今回の TestFlight build number は `1.0(2)`。次回アップロード時は `BUILD_NUMBER` を 3 以上に上げる必要がある
- 未追跡ディレクトリ `converted/`、`inputdata/`、`screenshot/` はユーザー作業の可能性があるため触っていない

## 6. 次にやるべきこと

- TestFlight で `mahjong` 1.0(2) をインストールする
- `/yomi` 入口、クイズ回答、結果、AI良問判定レビューの4画面を実機で確認する

## 7. ChatGPT レビュー依頼文

```text
mahjong（Next.js 16 + Capacitor iOS）の当たり牌読みモード TestFlight 反映レビューをお願いします。
runId: 20260719-173244 / commits: ae7f66c, 3ec96db / Codemagic buildId: 6a5c8a4ecdcd9fa083e88c5e

内容: main の /yomi 当たり牌読み練習を ios-codemagic-test にマージし、React Hooks lint を解消。BUILD_NUMBER=2 を codemagic.yaml と Xcode project に反映し、Codemagic workflow ios-capacitor-test で IPA作成と TestFlight Publishing success を確認。

確認したい観点:
1. /yomi の sessionStorage 初期化を lazy useState に寄せた変更で、ページ遷移時の状態復元に問題がないか
2. keyed child component による選択状態リセットがクイズ遷移として妥当か
3. Codemagic の agvtool build number 更新手順が次回以降のTestFlight運用に十分か
```
