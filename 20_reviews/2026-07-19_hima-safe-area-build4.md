---
date: 2026-07-19
task: 暇潰し！全画面セーフエリア対応＋build#4 TestFlight反映
runId: 20260719-163024
targetApp: hima-app
monetizationImpact: low
theme: [app-strategy]
relatedRunIds: [20260718-173245, 20260717-091246]
commitHashes: [8e0d06c]
# reviewFileCommit:
---

# 2026-07-19 暇潰し！全画面セーフエリア対応＋build#4 TestFlight反映

## 1. 作業目的

- ユーザー指摘「全体的に上に寄っている」を、iPhoneのノッチ/ステータスバー領域込みで解消する
- build#3で未対応だったランキング閲覧画面も縦中央に寄せる
- 修正後のビルドをTestFlightへ届け、iPhone実機確認に進める

## 2. 実施内容

- 原因: `viewport-fit=cover` を使っている一方で、アプリ全体に `safe-area-inset-top` の余白が無く、ステータスバー/Dynamic Island下に食い込んでいた
- 修正:
  - `#app` に上部セーフエリア分のpaddingを追加
  - `#toast` の表示位置をノッチ下へ追随
  - `#endless` の最下部ボタン周辺に下部セーフエリア分の余白を追加
  - `#rankview` を `#result` と同じauto margin方式で縦中央化
  - `#rankview` のインライン `margin-top:24px` がauto marginを打ち消していたため除去
- buildNumber 3→4 → prepare → commit `8e0d06c` → push → Codemagic build#4 起動

## 3. 変更ファイル

- `apps/hima-app/www/index.html` — セーフエリアCSS、トースト位置、下部余白、ランキング中央化
- `apps/hima-app/release/ios-app.json` — buildNumber 4
- `apps/hima-app/codemagic.yaml` — prepare再生成（BUILD_NUMBER=4）

## 4. 検証結果

- headless実描画: iPhone 14 Pro相当のセーフエリア（上59px/下34px）をCSS模擬注入
- 390x844 / 390x600 の2サイズで home / play / result / rankview を確認
- home: タイトル開始位置がノッチ下へ移動、最下部ボタンも画面内
- play: ヘッダがセーフエリア下から開始、シートと「やめる」ボタンの表示崩れなし
- result: 上下余白のバランスを確認
- rankview: 初回検証で中央化不発を検出し、インラインstyle除去後に中央化を確認
- yaml lint OK / secret scan OK
- Codemagic build#4（buildId `6a5c5f48650a75a32c18e4eb`）: 全ステップsuccess、TestFlightアップロード完了

## 5. 未対応

- 実機セーフエリア値はheadlessで模擬したため、最終確認はiPhone TestFlightで行う
- App Store審査提出・公開は今回のスコープ外

## 6. 危険ポイント

- なし（CSS中心、ゲームロジック無変更）
- buildNumber更新とTestFlightアップロードは既存のCodemagic自動化フローを使用

## 7. 次にやるべきこと

- iPhone TestFlightでビルド 1.0.0(4) へ更新する
- ホーム / プレイ / 結果 / ランキングの4画面で、ノッチ回避と中央寄せを実機確認する

## 8. ChatGPT レビュー依頼文

```
hima-app（暇潰し！）のiOSセーフエリア対応レビューをお願いします。
runId: 20260719-163024 / commit: 8e0d06c / Codemagic buildId: 6a5c5f48650a75a32c18e4eb

内容: viewport-fit=cover環境で上部safe-area paddingが無く、iPhoneで全画面がステータスバー/Dynamic Island下に食い込んでいたため、#app/#toast/#endless/#rankviewのCSSを調整。ランキング画面はauto margin中央化がインラインmargin-topで打ち消されていたため除去。build#4は全ステップ成功しTestFlightへアップロード済み。

確認したい観点:
1. #app全体にsafe-area paddingを付ける方針で、各画面の二重余白リスクはないか
2. トーストと最下部ボタンのsafe-area追随は十分か
3. #rankviewのインラインstyle除去による副作用はないか
```
