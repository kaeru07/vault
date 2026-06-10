---
date: 2026-06-11
title: AI工場OS v2 設計レビュー（CTO/PMO戦略レビュー）
theme: [workflow, app-strategy]
reviewer: Claude (Opus 4.8 / 役割=Fable 5想定のCTOレビュー)
status: proposal
---

# AI工場OS v2 設計レビュー

> 目的: ユーザーが毎日5〜15分「承認・却下・優先順位・Goal調整」だけ行い、残りはAI工場が自律前進する状態を作る運用モデルの設計。コード品質・UI・技術選定は対象外。運用モデル最優先。

## 0. 現状診断（実データ根拠）

- `goals.json` = `{ goals: [], mainGoalId: null }` → **Goal層が空（0件）**。器はあるが中身ゼロ。
- `epics.json` = 11件（active 3 / done 8）。各Epicの `goal` は**外部キーでなく自由文の一行説明**。→ Goal→Epic階層は実在しない。
- active 3件 = epic-91 AI工場オペレーションセンター(20%) / BirdLog MVP(0%) / 動作確認Todoページ(0%)。**進行中が低進捗で滞留**。
- `recommended-epics.json` = 20件（doneCriteria/monetizationImpact/decisionPolicy/factoryEligiblePreview を持つ充実スキーマ）。
- `app-factory-candidates.json` = 5件（新規アプリ案・monetizationHypothesis）。
- `automation-config.json` = executorMode / autoResume / autoFallback / **factoryEnabled=false**。工場は作られているが停止中。
- → **候補ストリームが3系統（recommended-epics / app-candidates / monetization-candidates）に分裂**し、Goalに束ねられていない＝横並び増殖。

## 1. 構造的問題 TOP10

1. Goal層が空。北極星がなく全Epicが横並び（優先順位の原点が無い）。
2. Epic.goal が自由文。Goal紐付けが構造化されていない（orphan検出不能）。
3. 候補ストリーム3系統が未統合。重複・増殖・比較不能。
4. WIP制限なし。active Epicが低進捗のまま積み上がる。
5. 承認がインライン同期。人間が毎セッション同期ブロッカーになる。
6. 完了判定が曖昧。doneCriteriaはrecommended側にあるがEpic本体で必須化されていない。
7. 完了→ナレッジ→次Epic生成のループが閉じていない（手動）。
8. factoryEnabled=false。自走基盤はあるが「向かう先(Goal)」と「承認の非同期化」が無く起動できない。
9. 収益化ファネルが可視化されていない（候補→MVP→公開→収益の段が見えない）。
10. 再開性がEpic単位止まり。「今日どのGoalを前進させるか」のGoal単位再開が無い。

## 2. 理想のGoal階層

```
Goal (なぜ=北極星/測定可能)         3〜5個・うち mainGoal 1個
  └ Epic (何を=出荷可能な成果)       Goalあたり active 1〜2 / 全体 active ≤3
      └ Project (どのアプリ/repo)     Epicのターゲット（N:M）
          └ Execution Run (1作業単位) 既存ExecutionRun
              └ Review (検証/人間判定) reviewStatus + 20_reviews + _review_queue
                  └ Knowledge (蒸留)  Vault/decision-log/research
                      └ Next Epic     KnowledgeからGoalに紐づく次Epicを生成
```

### Goal運用モデル
- **個数**: 同時 active 3〜5。北極星 `mainGoal` 1個。多すぎ=横並び再発、少なすぎ=機会損失。
- **寿命**: 四半期相当（〜90日）。90日で「達成/継続/撤退/再定義」を判定（Goal review cadence）。
- **優先順位**: Goalに priority(weight) を持たせ、backlogスコアの係数にする。
- **達成判定**: Goalごとに metric + target（例: 収益化候補3本を公開 / news-app DAU N / /yomi 良問100問）。metricは集計可能な値に限定。曖昧目標は禁止。

## 3. 理想のEpicライフサイクル

```
proposed → approved → active → in_review → done
                    ↘ merged（統合）/ split（分割）/ dropped（廃止）
```

- **生成**: Knowledge or 候補backlogから。必ず goalId・doneCriteria・single executor を付けて proposed。
- **承認(approved)**: 人間が意思決定キューで承認（Goal紐付き・WIP空き確認）。
- **統合(merged)**: 同一Goal内で重複/近接するEpicは1本化。orphan/類似検出で候補提示。
- **分割(split)**: doneCriteriaが複数の出荷物を含む場合に分割。
- **廃止(dropped)**: Goal撤退・前提崩壊・90日無進捗で自動的に廃止候補へ。
- **完了(done)**: doneCriteria全達成 + Review通過。完了時にKnowledge抽出を必須トリガ。

## 4. Progressアプリのあるべき姿（司令塔）

「作業画面」ではなく「経営ダッシュボード+意思決定キュー」。1日5〜15分で回るUIに再設計。

### ホーム画面 表示TOP10
1. 今日の意思決定キュー（承認待ちカードN件・ワンタップ承認/却下/優先/Goal調整）
2. 北極星Goal + 各Goal達成度ゲージ
3. 進行中Epic(WIP)と各 nextAction / blocker / 最終Run
4. 工場ステータス（factory ON/OFF・最終Run・次スケジュール・executor上限残）
5. 詰まり（blocker / stale / failed run / review超過）
6. 収益化ファネル（候補→MVP→公開→収益）
7. 直近24-48hの成果（completed run / shipped）
8. 次にAIが着手予定のEpic（Goal紐付きbacklog top）
9. ナレッジ新着（review/knowledge ハイライト）
10. 人間専用TODO（公開/課金/認証/実機確認など承認以外で人間しかできない事項）

## 5. 再開性を高める仕組み
- 各Epicに **Resume Packet**（goalId / doneCriteria / nextAction / latestRunId / blockers / 最終Review）を必須・固定フォーマットで保持（nextAction・latestRunIdは既存）。
- Goal単位の再開ビュー「今日はこのGoalを前進させる」。
- progress正本 + 20_reviews + _review_queue が既に強い再開資産。欠けているのは**Goal単位の再開導線**のみ。
- 90日放置でも「Goal→active Epic→Resume Packet」を辿れば即着手可能にする。

## 6. 自走化を高める仕組み
- 工場が pull する先 = **Goal-scoredな単一backlog**（3候補ストリームを統合・Goal係数でスコア）。
- 承認を**非同期バッチ化**（インライン承認を廃し、意思決定キューに集約。人間は1日1回処理）。
- **完了→Knowledge抽出→次Epic候補生成**の自動チェーンを閉じる。
- stale/blocker/review超過を検知ジョブが意思決定キューへ自動起票。
- factoryを段階ON（Goalと非同期承認が揃ってから低リスクEpicでON）。

## 7. 人間とAIの責任分界点
- **人間（5〜15分/日）**: Goal設定・調整 / Epic承認・却下 / 優先順位変更 / 収益化Go判断 / 不可逆operations（公開・課金・認証・本番・force push）。＝CLAUDE.local の承認必須6点と一致。
- **AI工場**: 調査 / 情報収集 / 要件整理 / Epic生成（候補） / 実装 / レビュー / ナレッジ蓄積 / 次Epic生成 / 収益化準備（公開直前まで）。

## 8. ボトルネック分析（進捗を止める最大要因）
- **第1**: Goal層が空 → 優先順位が決まらず、人間が毎セッション事実上のプライオリタイザになる＝同期ブロッカー化。
- **第2**: 承認がインライン → 人間待ちで工場が止まる。
- **第3**: ループ未閉鎖 → 完了が次の燃料(次Epic)にならず、人間が次を考える。
→ いずれも「人間が同期的に介在しないと前に進まない」構造。Goal確立＋承認の非同期化で解消。

## 9. AI工場OS v2 中核データモデル
- **Goal**(id, title, isNorthStar, metric, target, deadline/寿命, priority, status)
- **Epic**(id, **goalId(FK)**, title, doneCriteria, status[proposed→…→done], decisionPolicy, executor, nextAction, latestRunId, blockers, progress)
- **Project**(appId, repoPath, urls, health) ← Epic と N:M（targetApps）
- **ExecutionRun**(既存: runId, epicId, runStatus, changedFiles, checks, summary, reviewStatus)
- **Review**(reviewStatus + 20_reviews md + _review_queue) ← Run と1:1
- **Knowledge**(Vault/decision-log/research) ← Reviewから蒸留 → 次Epic候補を生成

関係: Goal 1—N Epic / Epic 1—N Run / Epic N—M Project / Run 1—1 Review / Review →蒸留→ Knowledge / Knowledge →生成→ 候補Epic →承認→ Goal配下のEpic。

## 10. Codexで実装すべき改善 TOP10
1. Goal実運用化（goals.jsonを埋めるUI + mainGoal設定）
2. Epic.goalId 必須化 + orphan(Goal未紐付き)警告
3. 意思決定キューAPI + ホーム承認カード（非同期バッチ）
4. 候補backlog統合（recommended/app/monetization → Goal-scored単一backlog）
5. WIP制限の可視化・enforcement
6. Goal達成度ゲージ（metric集計）
7. stale/blocker/review超過 検知→キュー自動起票
8. 完了→Knowledge抽出→次Epic候補生成 の自動チェーン
9. 工場ステータスパネル（executor上限・autoFallback・next schedule）
10. Resume Packet 標準化（Epic単位の固定再開フォーマット）

## 最優先改善 TOP3
1. **Goal層を埋める**（空→3〜5 Goal + 北極星）。全優先順位の原点。
2. **承認をインライン→毎日5〜15分の非同期バッチキューへ**。人間を同期ブロッカーから外す。
3. **候補をGoal-scored単一backlogに統合 + WIP制限**でEpic増殖を止める。

## 最初の30日（CTOとして）
- Week1: Goal層確立（3〜5 Goal・北極星・metric/target/寿命）。Epicを既存Goalへ再マッピング。
- Week2: 意思決定キュー + ホーム再設計（承認/却下/優先/Goal調整をワンタップ）。
- Week3: 候補backlog統合 + WIP制限 + stale/blocker検知の自動起票。
- Week4: ループ自動化（完了→Knowledge→次Epic）+ 低リスクEpicでfactory段階ON。KPI=「人間の1日操作時間≤15分」「人間待ちで止まる時間の最小化」。
