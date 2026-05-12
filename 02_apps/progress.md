# progress（進捗管理アプリ / ny01/progress）

VPS 上で稼働中の社内進捗管理 Next.js アプリ。Claude Code の作業ログを正本として記録する基盤。

## 概要

- パス: `/root/company/apps/ny01/progress`
- ポート: 3010
- プロセス管理: pm2
- データパス: `data/real/`（`.env.local` の `PROGRESS_DATA_PATH` で指定）
- スタック: Next.js (App Router) / TypeScript / Tailwind / NDJSON+JSON ファイルストア

## 主な機能

- 案件 / タスク / work-queue 管理
- ExecutionRun（作業履歴）の登録・レビュー
- JSON 一括取り込みでの ToDo 追加
- 集中作業モード用の work-queue スコープ運用
- 日別進捗ページ

## 重要ファイル

- `data/real/app-progress.json` — 案件サマリー
- `data/real/project-tasks.json` — タスク詳細
- `data/real/work-queue.json` — 今日の作業キュー
- `data/real/work-log.ndjson` — 時系列ログ
- `data/real/overall-progress.md` — 人向け要約

## 運用メモ

- Claude Code は work-queue にある queued/in_progress のみを着手対象とする
- 1 task = 1 review = 1 execution-run（複数タスクの統合禁止）
- task.status → done は `app-progress.json` の nextAction 原子更新と同時に行う

## 関連

- [[../03_prompts/claude-code-standard]]
- [[../04_reviews/review-template]]
