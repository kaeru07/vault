---
status: active
last_touched: 2026-05-13
monetization_impact: low
theme: [progress, workflow, obsidian, activity-mining]
phase: maintenance
url: http://localhost:3010
---

# progress（進捗管理アプリ / ny01/progress）

> VPS 上で稼働中の社内進捗管理 Next.js アプリ。**Claude Code の作業ログを正本として記録する基盤**。

---

## 概要

- パス: `/root/company/apps/ny01/progress`
- ポート: 3010（pm2 管理）
- スタック: Next.js (App Router) / TypeScript / Tailwind / NDJSON + JSON ファイルストア
- データ: `data/real/`（`.env.local` の `PROGRESS_DATA_PATH` で指定）

---

## 現在の役割

- **作業履歴の正本**：Claude Code が作業終了時に `POST /api/execution-runs` で ExecutionRun を登録する
- **work-queue**：集中作業モードでの「今日やるタスク」の参照元
- **レビュー基盤**：ExecutionRun に対する自己/相互レビューの記録
- **会話ログより優先される情報源**（CLAUDE.local.md の「progress 正本運用」）

---

## 主要機能

- 案件 / タスク / work-queue 管理
- ExecutionRun（作業履歴）の登録・レビュー
- JSON 一括取り込みでの ToDo 追加
- 集中作業モード用の work-queue スコープ運用
- 日別進捗ページ

### 重要ファイル

- `data/real/app-progress.json` — 案件サマリー
- `data/real/project-tasks.json` — タスク詳細
- `data/real/work-queue.json` — 今日の作業キュー
- `data/real/work-log.ndjson` — 時系列ログ
- `data/real/overall-progress.md` — 人向け要約

---

## 現状の課題

- **iPhone / 外出先からの参照導線が弱い**（localhost:3010 のみ）
- レビュー結果と ExecutionRun の紐付けが手動
- 収益化視点での集計ビュー（どのアプリにどれだけ時間を使ったか）が未実装
- 抽出可能な「自家用ツール → 公開可能なテンプレ」の境界が未定義

---

## 改善候補

- **Obsidian Vault 連携**：focus-work レポートを Obsidian 側に自動追記する CLI
- **work-queue から Obsidian Daily へ転記**するスクリプト
- アプリ別・週次の時間集計ビュー（[[../06_research/activity-mining]] と統合）
- 「未完了」「興味の波」を抽出するクエリビュー
- 公開可能テンプレ範囲の文書化（[[途中プロジェクト一覧]] の progress テンプレ販売）

---

## 今後の方向性

- 短期: **作業履歴を取りこぼさない**運用を固める（POST 抜け対策・確認導線）
- 中期: **Obsidian と双方向連携**（Vault を見れば作業 / 興味 / 収益化候補が一望できる状態）
- 長期: **収益化資産化**（自家用 → テンプレ販売 / note 化 / SaaS 化の判断）

---

## 運用メモ

- Claude Code は work-queue にある queued / in_progress のみを着手対象とする
- 1 task = 1 review = 1 execution-run（複数タスクの統合禁止）
- task.status → done は `app-progress.json` の nextAction 原子更新と同時に行う
- pm2 再起動はユーザー判断（自動再起動は行わない）

---

## 関連

- [[../03_prompts/Claude-Code標準運用]]
- [[../04_reviews/Claude作業レビュー運用]]
- [[../06_research/activity-mining]]
- [[途中プロジェクト一覧]]

## 直近の更新

- 2026-07-18 runId: 20260718-232410 — iOSビルド状況画面(/ios-builds)を追加。Codemagic/TestFlight状態確認とビルド候補→今日の判断のピックアップが可能に（関連: [[../20_reviews/2026-07-18_progress-ios-builds-screen]] / [[hima-app]]）
- 次の確認事項: iPhoneでアプリ開発タブ→iOSビルドを開き、hima-app/mahjongの状態表示と「iOSビルド候補: 麻雀読みトレーニング」の今日の判断を確認
- 2026-07-19 runId: 20260719-093122 — iOSビルドを下タブへ追加。今日の判断カード(ビルド候補/完了作業の危険確認)を人間語化（関連: [[../20_reviews/2026-07-19_decide-cards-humanize-ios-tab]]）
- 次の確認事項: iPhone下タブの「iOSビルド」と、今日の判断の麻雀ビルド候補・hima完了作業確認カードの文言を確認
