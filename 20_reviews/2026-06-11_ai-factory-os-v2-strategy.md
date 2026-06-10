---
date: 2026-06-11
task: AI工場OS v2 戦略レビュー（Goal/Epic/Progress/実行サイクル/再開性/自走化の運用設計）
runId: 20260611-001032
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: []
---

# 2026-06-11 AI工場OS v2 戦略レビュー

> コード実装なし（禁止指示）。CTO/PMO/AI工場OS設計者としての運用モデルレビュー。本体は [[../06_research/2026-06-11_ai-factory-os-v2]]。

---

## 1. 作業目的
- ユーザーが毎日5〜15分「承認・却下・優先順位・Goal調整」だけ行い、残りをAI工場が自走前進する運用モデルを設計する。
- コード品質/UI/技術選定/フォルダ整理は対象外。運用モデル最優先。

## 2. 実施内容
- 現行データモデルを実査（goals/epics/recommended-epics/automation-config/app-factory-candidates）。
- 戦略ドキュメント `06_research/2026-06-11_ai-factory-os-v2.md` を作成。
- 構造的問題TOP10 / Goal階層 / Epicライフサイクル / Progress司令塔像 / 再開性 / 自走化 / 責任分界 / ボトルネック / OS v2データモデル / Codex実装TOP10 を整理。
- 実装は行わず提案までに留めた。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| obsidian-sync-vault/06_research/2026-06-11_ai-factory-os-v2.md | 新規。戦略レビュー本体 |
| obsidian-sync-vault/20_reviews/2026-06-11_ai-factory-os-v2-strategy.md | 本レビュー |

## 4. 検証結果
- コード変更なし（実装禁止指示）→ tsc/build n/a
- 実データ確認: goals.json=空(0件) / epics=11(active3/done8) / recommended=20 / factoryEnabled=false を確認
- 機密スキャン: OK / ob sync: Fully synced / GitHub: vault push

## 5. 未対応
- 実装は意図的に未着手（禁止）。Codex実装TOP10は提案のみ。
- Goalの具体値（3〜5本の中身・metric/target）はユーザーのGoal調整待ち。

## 6. 危険ポイント
- なし（読み取り調査 + ドキュメント作成のみ）。本番・DB・課金・認証に未接触。

## 7. 次にやるべきこと
- 人間: 北極星含む3〜5 Goalを定義（最優先・これが全体の原点）。
- AI(別Epic): goals.json実運用化 / Epic.goalId必須化 / 意思決定キュー / backlog統合 / WIP制限。

## 8. ChatGPT レビュー依頼文

````text
以下は Claude(CTOレビュー)による「AI工場OS v2 運用モデル設計」です。PM補助としてレビューしてください。

対象: progress（AI工場の司令塔アプリ）の運用モデル
本体ドキュメント: 06_research/2026-06-11_ai-factory-os-v2.md
日付: 2026-06-11

## 背景（実データ）
- goals.json が空（0 Goal・mainGoal null）。Epic.goal は自由文でGoal外部キーなし。
- epics 11件(active3/done8)が横並び。recommended-epics 20件 + app候補5 + monetization候補が未統合。
- factoryEnabled=false（自走基盤はあるが停止）。

## 提案の骨子
- Goal層を埋める(3〜5・北極星1)。Epic.goalId必須化。候補をGoal-scored単一backlogへ統合+WIP制限。
- 承認をインライン→毎日5〜15分の非同期バッチキューへ。完了→Knowledge→次Epicのループを閉じる。
- 人間=Goal/承認/却下/優先/収益化Go/不可逆operations のみ。残りはAI。

## 確認したい観点
- Goalの個数(3〜5)・寿命(90日)・達成判定(metric必須)の妥当性
- 承認の非同期バッチ化が「毎日5〜15分」を本当に実現するか
- 候補3ストリーム統合のスコアリング設計（Goal係数）の妥当性
- WIP制限(active Epic≤3)が機会損失にならないか
- factory段階ONの前提条件（Goal+非同期承認）は十分か
- 最優先改善TOP3とCodex実装TOP10の順序は妥当か
````

---

## 関連
- 本体: [[../06_research/2026-06-11_ai-factory-os-v2]]
- 関連アプリ: [[../02_apps/progress]]
- フォルダ運用: [[../20_reviews/README]]
