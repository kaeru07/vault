---
date: 2026-06-02
task: 市場調査・新規アプリ発掘（定時継続業務 第2サイクル / 収益化順5件 + 1位Epic Contract）
runId: 20260602-204657
targetApp: company-meta
monetizationImpact: high
theme: [market-research, app-strategy, monetization]
relatedRunIds: [20260602-202214]
commitHashes: []
---

# 市場調査・新規アプリ発掘 第2サイクル（2026-06-02）

## 1. 作業目的
AI工場の市場調査責任者として、定時実行（11:00 / 23:00）の継続発掘業務を1サイクル実施。日々の市場調査・Vault・Progress・既存アプリを横断し、収益化可能性の高い新規アプリ候補を継続発掘する。完了しない継続業務。

## 2. 実施内容
- STEP1 情報収集: Vault（05_monetization / 06_research / 20_reviews / 02_apps / 00_inbox）、news-app daily（market-research / ai-news / ai-tools、最新2026-05-27・新規追加なし）、Progress Epic（epic-91 active / epic-a5r7n4 active / epic-p2 done）、queue、goals（空）、runs総数204、review結果。
- STEP2 重複チェック → STEP3 市場機会分析 → STEP4 候補作成（5件）→ STEP5 順位付け → STEP6 1位Epic生成 → STEP7 Vault反映 → STEP8 ExecutionRun記録。
- 前回（runId 20260602-202214）発掘の #9-13 を「candidate登録済（パイプライン在庫）」として除外し、Vault在庫から次点を新規発掘。

## 3. 変更ファイル
- `05_monetization/収益化候補一覧.md` — 「新規発掘候補（第2サイクル）」テーブル（#14-18）追記。

## 4. 検証結果
- 重複チェック: 既存アプリ/Epic/candidate(001/005)/前回5件/ドメイン重複(打牌採点・詰将棋)/飽和(クラフトビア)を除外記録。
- ExecutionRun: runId 20260602-204657（success）。
- ob sync / GitHub反映: 本ファイル生成後に実施。

## 5. 未対応
- 2026-05-28以降の新規 daily research が未取得 → 市場シグナルが前回と同一。次サイクルで新規取得が必要。
- Epic Contract は JSON生成のみ（Factory登録は未実施 = Epic生成まで）。
- 候補は idea段階。Epic化前にカテゴリ別競合ASO実査が必要。

## 6. 危険ポイント
- 全候補に external_publish（ストア公開）リスク → Epic は公開前で停止する設計（approval_required）。
- BonsaiCare は Planta 等の強競合あり。差別化前提の検証が必要。

## 7. 次にやるべきこと（次回調査観点）
- 新規 daily-market-research / ai-news / ai-tools（05-28以降）を取得し市場シグナルを更新。
- 各候補カテゴリ（birding / coffee roast / boardgame / fermentation / bonsai）の競合ASO・レビュー不満を `06_research/` に1件ずつ実査し再スコア。
- 1位 BirdLog の Epic Contract をユーザー/Factory承認 → 登録。

## 8. ChatGPTレビュー依頼文
```
対象: company-meta（市場調査・新規アプリ発掘 第2サイクル） / runId 20260602-204657
定時継続発掘業務として、前回発掘の5件を「登録済」で除外し、Vault在庫から次の収益化候補を5件発掘しました（1位=野鳥観察ノートBirdLog、2位=自家焙煎ジャーナル、3位=卓上ゲーム戦績手帳、4位=発酵食ジャーナル、5位=盆栽育成カレンダー）。全件 local-first・API不要・静的JSONで成立、習慣/記録/収集/趣味系、日本+海外両対応。
確認したい観点:
1. 1位 BirdLog の収益化順位・海外市場規模（eBird/Merlin文化圏）・サブスク成立性の妥当性。
2. 各候補の競合（Fishbrain系/Planta/Untappd等）に対する差別化は十分か。過大評価していないか。
3. local-first・AI単独開発の前提に穴はないか。
4. 5件以外に横断材料から見落としている有力カテゴリはあるか。
5. 1位 Epic Contract（doneCriteria / external_publish / approval_required）の粒度は Factory自動実行に適切か。
6. この継続発掘業務の「重複除外の線引き（前回分を除外する運用）」は妥当か。
```
