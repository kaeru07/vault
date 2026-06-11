# AI工場 Goal Mapping Migration Report

作成日: 2026-06-11

## 投入したGoal

| id | title | metric | target | current | priority | status | North Star |
| --- | --- | --- | ---: | ---: | --- | --- | --- |
| goal-ai-factory-os | AI工場OS自走化 | daily_decision_minutes | 15 | 60 | high | active | true |
| goal-execution-review-loop | Execution→Review→Next Epicループ完成 | closed_loop_rate | 80 | 10 | high | active | false |
| goal-ai-executor-ops | AI実行者の役割分担と安全運用 | safe_execution_rate | 90 | 30 | high | active | false |
| goal-value-validation-pipeline | 価値検証パイプライン構築 | validated_project_count | 5 | 0 | high | active | false |

補足: Progressの既存Goal型は priority が high/medium/low のため、指定の P0/P1 は型互換を優先して high に正規化した。

## mainGoalId / North Star

- mainGoalId: `goal-ai-factory-os`
- North Star Goal: `goal-ai-factory-os`
- North Star は1件のみ。

## goalIdを付与したEpic

| epicId | goalId | status | 分類理由 |
| --- | --- | --- | --- |
| epic-91 | goal-ai-factory-os | active | ProgressをAI工場の管制塔にするオペレーションセンターEpic。司令塔化、Progress改善、継続消化基盤に該当。 |
| epic-p2-factory-multiple-run- | goal-execution-review-loop | done | Factory Runner、複数Run、ExecutionRun記録、Claude上限時のCodex継続検証が主題。実行結果を運用ループへつなぐEpic。 |
| epic-a5r7n4 | goal-value-validation-pipeline | done | アプリ案の選定、要件整理、実装、検証、レビュー記録、収益化候補アプリの継続開発が主題。 |
| epic-anglerlog-mvp-local-firs | goal-value-validation-pipeline | done | 収益化候補アプリMVPの開発・ローカル検証。Project/対象領域はAnglerLogで、Goalは価値検証。 |
| epic-birdlog-mvp-local-first- | goal-value-validation-pipeline | active | 収益化候補アプリMVPの開発・ローカル検証。Project/対象領域はBirdLogで、Goalは価値検証。 |
| epic-progress-url | goal-ai-factory-os | done | Progress内で全アプリURLを一元管理する司令塔機能。 |
| epic-url-iphone-vps-url | goal-ai-factory-os | done | ProgressのURL一覧をiPhone確認用の運用導線へ整えるEpic。 |
| epic-vault | goal-value-validation-pipeline | done | ニュースアプリの市場調査ビューとVault日次更新の反映。対象領域はニュース/調査で、Goalは価値検証。 |
| epic-vercel | goal-value-validation-pipeline | done | ニュースアプリ研究ビューの本番反映確認。対象領域はニュース/調査で、Goalは価値検証。 |
| epic-vault-4suq | goal-value-validation-pipeline | done | ニュースアプリ研究ビューのVault同期自動化。対象領域はニュース/調査で、Goalは価値検証。 |

## Goal別Epic件数

| goalId | Epic件数 |
| --- | ---: |
| goal-ai-factory-os | 3 |
| goal-execution-review-loop | 1 |
| goal-ai-executor-ops | 0 |
| goal-value-validation-pipeline | 6 |
| orphan | 1 |

## orphanのまま残したEpic

| epicId | title | status | 残した理由 |
| --- | --- | --- | --- |
| epic-progress-todo | progressアプリに動作確認Todoページを追加する | active | Progress改善(A)と、Epic完了後の人間確認・レビュー導線(B)の両方にまたがる。主Goalの判断を人間に委ねるため、無理に分類しない。 |

## 判断に迷ったEpic

- `epic-progress-todo`: Goal紐付け要判断。既存のorphan Epic検出によりProgressホームの意思決定キューに表示される。

## recommended-epics系ファイル

`data/real/recommended-epics.json` の20件に `goalId` を付与した。

- 収益化候補、公開、課金候補: `goal-value-validation-pipeline` 18件
- 既存Epic継続候補: `goal-ai-factory-os` 1件
- Factory失敗修正候補: `goal-execution-review-loop` 1件

## Project扱い

麻雀、ニュース、NetScope、転職、スクレイピング、資格アプリ、収益化アプリ候補はGoalとして追加していない。既存の `targetApp` / `targetApps` / candidateの `targetApp` は対象領域として保持した。

## 変更しなかった理由

- 既存Goalは削除していない。今回は空だったため4Goalを追加した。
- 既存Epicは削除していない。
- 既存Epicの `status` / `priority` / `doneCriteria` は変更していない。
- Project機能は新規実装していない。
- UI全面刷新、外部公開設定、認証情報、環境変数は変更していない。

## 次に人間が判断すべきこと

1. `epic-progress-todo` の主Goalを `goal-ai-factory-os` にするか `goal-execution-review-loop` にするか決める。
2. `goal-ai-executor-ops` に紐付くEpicを新規起票するか、既存候補から昇格するか決める。
3. `recommended-epics.json` の suggested 候補を、Goal別に承認/却下/保留する。
4. active EpicのWIPを見て、BirdLog、AI工場OS、動作確認Todoの優先順位を決める。

## 次にCodexで実装すべきこと

1. Goal別backlog統合ビューで recommended/app-factory/monetization candidates を同じGoal軸で並べる。
2. `epic-progress-todo` の人間判断後、Goal紐付けを反映する。
3. `goal-ai-executor-ops` 用に、AI実行者ロール/安全運用/危険操作承認フローのEpicを作成する。
