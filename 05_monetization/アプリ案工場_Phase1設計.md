---
title: アプリ案工場 Phase1 設計仕様（API/課金なし・設計のみ）
type: monetization-design
status: proposal
created: 2026-05-18
updated: 2026-05-18
source: 00_inbox/chatgpt-import/important/2026-05-16_アプリ案工場-Phase1構想.md（05候補 高優先1番）
related_app: progress
---

# アプリ案工場 Phase1 設計仕様（API/課金なし）

> [[../00_inbox/chatgpt-import/important/2026-05-16_アプリ案工場-Phase1構想]] を実装可能な粒度に**設計**する。
> **実装・本番コード変更はしない**（progress は稼働本番アプリ＝ /vloop 停止条件）。
> 実装は人間承認後に progress 投入 ToDo として行う（[[progress連携基準]]）。本ファイルは設計のみ。

## Phase1 スコープ（構想の確定事項を踏襲）

- API / 追加課金 / 外部DB / Supabase **不使用**
- progress 内に画面追加。手入力でアプリ案 → 収益化評価軸でスコア → モック作成 ToDo を自動登録
- 保存はローカル JSON（既存 ExecutionRun/queue/Obsidian 連携を壊さない）

## 画面（案・ルート `/app-factory`）

- 入力フォーム: アプリ名 / 一言概要 / 想定収益方法 / 対象市場
- 評価軸入力（各 1〜5・構想の8軸）:
  収益化方法 / API不要可否 / 著作権リスク / 実装難易度 / 広告収益見込み / 拡散性 / 継続性 / 総合
- 一覧: 総合スコア降順（**収益化インパクト最優先**）。フィルタ: API不要のみ / 著作権リスク低のみ
- アクション: 「モック作成 ToDo を登録」ボタン → ローカル JSON に1件追記（progress の既存 ToDo 形式に準拠）

## データ構造（案・既存 ExecutionRun 型は変更しない）

```
AppIdea {
  ideaId: string            // YYYYMMDD-<slug>
  name: string
  summary: string
  revenueMethod: string
  targetMarket: '日本'|'海外'|'両方'
  scores: {                 // 各1..5
    収益化方法:number, API不要:number, 著作権:number, 実装難易度:number,
    広告見込み:number, 拡散性:number, 継続性:number
  }
  scoreTotal: number
  revenueImpact: 'high'|'medium'|'low'|'none'
  status: 'idea'|'mock_todo_registered'   // approvedはここで扱わない
  createdAt: string
}
```

- 保存先（案）: `progress` のローカルデータ層に `app-ideas.json` を新設（ExecutionRun と疎結合）
- 「モック作成 ToDo 登録」= progress の ToDo 仕組みへ1件追加（**収益化シナリオの approved 化とは別物**。
  candidate→approved ゲート（[[収益化シナリオ承認フロー]]）には触れない）

## 既存 progress との非破壊統合

- 追加ルート `/app-factory`（読み書きはローカル JSON のみ）
- 既存画面・API・ExecutionRun スキーマは**変更しない**（追加のみ）
- アプリ案 → モック ToDo は「アイデア段階の作業化」であり、収益化シナリオの承認フローとは独立
  （アプリ案工場で高スコアでも、収益化シナリオの approved は人間のみ）

## Phase2 以降（構想踏襲・本設計外）

- Phase2: Codex/Claude が市場調査 Markdown を読み候補生成 → Obsidian 蓄積 → progress 順位付け
  （[[../06_research/market-research-hub設計]] と接続可能）

## 未対応点 / 仮説
- 「モック作成 ToDo」の progress 側データ形式は既存 ToDo 構造に合わせる前提（実装時に要現物確認）。仮説で確定しない
- progress は本番稼働アプリのため、実装は architect 方針確認 → 人間承認後（[[../03_prompts/Claude-Code標準運用]]）
- 本ファイルは設計のみ。実装着手は本セッションでは停止条件（設計タスクで本番コード変更しない）に従い未実施

## 次の一手
1. ユーザー/pm が本設計を承認 → progress 投入 ToDo 化（[[progress追加ToDo案]] ToDo#4 と並ぶ実装候補）
2. 承認時 architect フェーズで progress の ToDo データ形式を現物確認してから実装
3. Phase2 は market-research-hub 採用とセットで再設計

## 関連
- [[../00_inbox/chatgpt-import/important/2026-05-16_アプリ案工場-Phase1構想]]（出典構想）
- [[progress_収益パイプライン設計]] / [[progress追加ToDo案]] / [[収益化シナリオ承認フロー]] / [[../06_research/market-research-hub設計]]
