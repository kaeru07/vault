---
date: 2026-07-18
task: progressにiOSビルド状況画面・TestFlight確認・ビルド候補ピックアップを実装
runId: 20260718-232410
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260717-091246, 20260718-173245]
commitHashes: [e6bd5ac]
# reviewFileCommit:
---

# 2026-07-18 progressにiOSビルド状況画面・候補ピックアップを実装

## 1. 作業目的

- ユーザー要望「Codemagic〜TestFlight確認まで自動で。画面で状態確認できるように。候補をピックアップする仕組みを」
- 期待効果: iOSアプリのビルド判断がprogress上で完結。ビルドすべきアプリの見落とし防止

## 2. 実施内容

- 実装はCodex委譲・設計/検証/commitはClaude（標準運用）
- `/ios-builds` 画面: apps/*/codemagic.yaml からアプリ自動発見（hima-app・mahjong）、Codemagic直近5ビルド・状態バッジ・ローカル最新コミット・TestFlight状態を表示、30秒自動更新
- ビルド候補判定: 未ビルド / 最新ビルド失敗 / ローカル最新コミットが直近成功ビルドへ未反映 の3条件
- 「候補を今日の判断へ」: multi_option approval としてInboxへ作成（ビルドする/見送る、重複防止付き）
- 「ビルド実行」: 確認ダイアログ後にCodemagic POST /builds
- TestFlight処理状況: ASCキー配置で自動有効化（未配置の現在は「未確認」表示+Publishing成否で判断の案内）
- 運用ドキュメント4点セット更新（guide/TERMS/operating-model）
- 副産物修復: リモートmainが `lib/execution-run-errors.ts` のコミット漏れでtsc破損していたのを同梱修復

## 3. 変更ファイル

- 新規: lib/ios-builds.ts / types/ios-builds.ts / lib/ios-builds.test.ts / app/api/ios-builds/{route,trigger,pickup} / app/ios-builds/{page,IosBuildsClient}
- 追記: lib/nav-groups.ts（サブタブ）/ lib/command-center.ts（TERMS 3語）/ app/guide/page.tsx（スライド）/ docs/operations/current-operating-model.md
- 修復: lib/execution-run-errors.ts(+test) 同梱

## 4. 検証結果

- tsc OK / npm test 23件 OK / クリーンbuild OK / チャンク存在確認 OK
- トークン実値がクライアントバンドルに含まれないことを確認（値非表示のままgrep照合）
- pm2 restart後、API実データで hima-app=候補なし・mahjong=未反映コミットあり を正判定
- headless実描画: 両アプリ表示・候補バッジ・ASC案内すべてOK、JSエラーなし（favicon 404は既存事象）
- pickup実行: mahjong候補が今日の判断へ作成（appr-1784375919656-1）、2回目は重複スキップ
- 他セッションの未コミット変更が大量にあるため、共有4ファイルはHEAD+今回分のみを外科的にステージし、indexツリー抽出tscで単独整合を確認してからcommit/push

## 5. 未対応

- trigger APIの実起動検証（CI分消費のため実装のみ。画面からの初回実行はユーザー操作時）
- 今日の判断で「ビルドする」を選んだ後の自動ビルド起動連携（現状は画面のビルド実行ボタンを人が押す）

## 6. 危険ポイント

- Codemagicトークンはサーバ側のみ・レスポンス/ログ非出力を確認済み
- ビルド実行はCI分（mac instance）を消費する操作。確認ダイアログで誤タップ防止

## 7. 次にやるべきこと

- iPhoneで /ios-builds（アプリ開発タブ内）の実表示確認
- 今日の判断の「iOSビルド候補: 麻雀読みトレーニング」を判断
- ASCキー（asc.env / asc_key.p8）配置でTestFlight処理状況の自動確認を有効化
- 承認→自動ビルド起動の連携は次フェーズ候補

## 8. ChatGPT レビュー依頼文

```
progressアプリに追加したiOSビルド管理機能のレビューをお願いします。
runId: 20260718-232410 / commit: e6bd5ac / 対象: /ios-builds 画面 + API 3本 + lib/ios-builds.ts

内容: apps/*/codemagic.yamlからiOSアプリを自動発見し、Codemagic直近ビルド・TestFlight処理状況(ASCキー配置時)・ローカルコミット差分を表示。候補判定(未ビルド/最新失敗/未反映コミット)を今日の判断(multi_option)へ送る仕組みと、画面からのビルド起動を実装。

確認したい観点:
1. 候補判定3条件の妥当性(過剰検出/見落としの懸念)
2. 「候補を今日の判断へ」を手動ボタンにした設計(定時自動実行に組み込むべきか)
3. ビルド起動を画面ボタンに留めた安全性判断(承認→自動起動まで進めるべきか)
4. リモートmainのtsc破損をfeatコミットに同梱修復した判断
```
