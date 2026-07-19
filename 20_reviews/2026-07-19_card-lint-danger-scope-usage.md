---
date: 2026-07-19
task: 判断カード機械文ガード+危険判断のプロジェクト単位ブロック+iOSビルド使い方
runId: 20260719-112010
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260719-093122]
commitHashes: [d1b8568]
# reviewFileCommit:
---

# 2026-07-19 判断カードの機械文ガード・危険判断スコープ化・iOSビルド使い方

## 1. 作業目的

ユーザー指摘3点: ①himaカードのような分かりにくい内容が二度と起票されない仕組みにする ②危険判断待ちが無関係プロジェクトの自動実行まで止めるのはおかしい（暇つぶし以外は回るのが正しい） ③iOSビルドページにどのボタンで何が起きるかの説明を付ける

## 2. 実施内容

- **機械文ガード**: lib/human-card-lint 新設（ISO日時/生hash/内部ID/key=value連鎖/JSON断片/英語のみ行）。createApproval/updatePendingApproval 起票時に自動lint→違反はautomation logへ警告（起票は止めない）。旧hima文面=NG・修正後=OKをテストで固定
- **起票9サイト監査**: NG3件を修正（executor_fallback の resultSummary生渡し / blocked-decisions / skills候補の candidate:id 露出）。他6サイトはOK確認
- **危険判断スコープ化**: approval→projectId/epic→goal/createdRunId→targetApp の順でスコープ解決し、該当プロジェクトの候補だけ除外して工場は続行。解決不能が1件でもあれば従来どおり全体停止（安全側）。表示・ガイド・運用ドキュメント追随
  - 補足: himaの承認は前作業の人間語化で選択肢が「問題なし」系になりレビュー型除外に該当、全体停止は既に解消していた（7/11〜昨日まで全体を止めていた事実は判明）
- **iOSビルド使い方**: ページ上部に折りたたみ。ビルド実行=Codemagic→TestFlight自動アップロード(3〜5分・無料枠消費・連打注意)、候補を今日の判断へ=「ビルドする」を選んでも自動起動しない現状仕様を明記、候補/状態バッジとTestFlight欄の読み方、30秒自動更新

## 3. 変更ファイル

18ファイル（新規: human-card-lint / danger-approval-scope 各+テスト。修正: operations-store / factory-runner / factory-metrics / blocked-decisions / approval-effects / ai-review / command-center / skills route / IosBuildsClient / guide / SystemSpecification / operating-model。同梱: goal-completion-sync=前セッションのコミット漏れ解消）

## 4. 検証結果

- tsc OK / npm test 93件 pass / build OK / indexツリーtsc OK
- headless実描画: /ios-builds 使い方ガイドの全説明項目を確認
- スコープ除外の実挙動は次回定時自動実行のログで確認予定

## 5. 未対応

- 「ビルドする」承認→自動ビルド起動の連携（次フェーズ）
- 危険判断スコープ除外の本番実挙動確認（次回定時実行待ち）

## 6. 危険ポイント

- 工場ゲートの緩和方向の変更。安全側フォールバック（未スコープ=全体停止）と純関数テストで担保。本番デプロイ等の危険承認は該当プロジェクトでは引き続き確実に止まる

## 7. 次にやるべきこと

- 次回定時自動実行(11/14/16/23時)後、automation log に「危険判断待ちにより〜を除外して続行」が正しく出るか確認
- iPhoneで /ios-builds の使い方表示を確認

## 8. ChatGPT レビュー依頼文

```
progressの安全設計変更のレビューをお願いします。
runId: 20260719-112010 / commit: d1b8568

内容: (1)承認カードの機械文を起票時に検出するlint+9起票サイト監査修正 (2)危険判断待ちのFactory停止を全体→関係プロジェクトのみに変更(スコープ解決不能時のみ全体停止) (3)iOSビルドページに使い方説明。

確認したい観点:
1. 危険判断のスコープ化はfail-safeとして十分か(全体停止を残すべきケースの漏れ)
2. lintルールの過剰/過少検出の懸念
3. 起票ブロックせず警告のみに留めた判断の妥当性
```
