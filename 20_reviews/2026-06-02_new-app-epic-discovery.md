---
date: 2026-06-02
task: 新規アプリEpic候補の横断発掘（Vault/Epic/Research/news横断・収益化順5件 + 1位Epic Contract生成）
runId: 20260602-202214
targetApp: company-meta
monetizationImpact: high
theme: [market-research, app-strategy, monetization]
relatedRunIds: [20260602-121838]
commitHashes: [7435e89]
---

# 新規アプリEpic候補 横断発掘（2026-06-02）

## 1. 作業目的
収益化可能性の高い新規アプリを継続的に発見し AI 工場へ供給するため、Vault・Progress Epic・Research・news-app daily を横断分析し、既存資産と重複しない新規アプリ候補を収益化順に最大5件発掘する。実装はせず Epic 生成（Contract JSON）まで。

## 2. 実施内容
- 横断読込: Vault（00_inbox / 05_monetization / 06_research / 20_reviews / 02_apps）、Progress 既存 Epic（epic-91 / epic-a5r7n4 / epic-p2）、queue、goals、news-app daily（market-research / ai-news / ai-tools 2026-05-26・27）。
- 既存除外を確認（Progress / Goal Planner / NetScope / mahjong=何切るcandidate-001 / news-app / 司令塔 / shogi-trainer / nanikiru-shorts / scrape-lab / token-speed-tool=candidate-005 / poker idea / 市場調査SaaS / Obsidian note / progressテンプレ販売）。
- `06_research/local-first-app-ideas-2026-05-14`（30案・idea段階・未candidate）を、2026-05-27 市場シグナル（Play Shorts 縦動画 ASO への発見導線移行＝重要度S / 汎用AIチャット飽和 / on-device AI）で再評価し、上位5件を新規アプリ候補へ昇格。
- 8評価軸でスコアリングし収益化順に整列。1位の Epic Contract JSON を生成。

## 3. 変更ファイル
- `05_monetization/収益化候補一覧.md` — 「新規発掘候補（2026-06-02横断分析）」テーブル（#9-13）を追記。

## 4. 検証結果
- 機密スキャン: パターンなし。
- ob sync: Fully synced。
- GitHub反映: obsidian-vault へ mirror → commit/push `7435e89`。
- ExecutionRun: runId 20260602-202214（POST success）。

## 5. 未対応
- Epic Contract は JSON 生成のみ。Factory への登録（POST）は実装に当たるため未実施（ユーザー承認待ち）。
- 2-5位は単独ノート（monetization-idea-template）未作成（候補昇格時に作成）。

## 6. 危険ポイント
- 候補出典の30案は idea 段階で市場の一次検証が薄い。Epic 化前に各カテゴリの Google Play 競合 ASO 実査が必要。
- 全候補に external_publish（ストア公開）リスクあり → Epic は公開前で停止する設計（decisionPolicy=approval_required）。

## 7. 次にやるべきこと
- 1位 釣果ログ AnglerLog の Epic Contract をユーザー承認 → Factory 登録。
- 各候補カテゴリの競合 ASO・レビュー不満を `06_research/` に1件ずつ実査。
- Play Shorts 縦動画素材を全候補の ASO 主導線に組み込む前提を共通化。

## 8. ChatGPTレビュー依頼文
```
対象: company-meta（新規アプリEpic候補の横断発掘） / runId 20260602-202214 / commit 7435e89
Vault・Progress Epic・Research・news-app daily を横断して、既存資産と重複しない新規アプリ候補を収益化順に5件発掘しました（1位=釣果ログ AnglerLog、2位=部位レスト筋トレ、3位=香水コレクション台帳、4位=アナログレコード台帳、5位=AI日めくりロジックパズル）。すべて local-first・API不要・静的JSONで成立し、日本+海外両対応・AI開発相性◎を条件に選定。出典は local-first-app-ideas-2026-05-14（idea段階）を 2026-05-27 市場シグナル（Play Shorts縦動画ASO / 汎用AIチャット飽和）で再評価したものです。
確認したい観点:
1. 収益化順位（1位 釣果ログ）の妥当性。海外市場規模・サブスク成立性の見立ては妥当か。
2. 競合（Fishbrain等の釣果SNS、各カテゴリ既存アプリ）に対する差別化軸は十分か。過大評価していないか。
3. AI単独開発のしやすさ（local-first / 静的データ）の前提に穴はないか。
4. 5件以外に、横断材料から見落としている有力カテゴリはあるか。
5. 1位の Epic Contract（doneCriteria / riskFlags=external_publish / approval_required）の粒度は Factory 自動実行に適切か。
```
