# 02_apps — アプリ別ノート

`apps/` 配下の各アプリについて、仕様・運用メモ・状態を集約する。**1 アプリ 1 ノート**。

## 既存ノート

- [[progress]] — 進捗管理アプリ（ny01/progress、ポート 3010、作業履歴の正本）
- [[mahjong]]
- [[nanikiru-shorts]]

## 新規アプリを追加するとき

1. `90_templates/app-note-template.md` をコピーして `02_apps/<app-name>.md` にする
2. 概要・スタック・状態・主要機能・重要ファイルを埋める
3. `apps/app-index.md`（company 側）も更新する
4. 関連プロンプト・レビューへの `[[link]]` を貼る

## 書く内容

| 項目 | 内容 |
|---|---|
| 概要 | パス / スタック / ポート / デプロイ先 / 状態（active / in_progress / user_action_pending / archived） |
| 主な機能 | 利用者から見た機能を箇条書き |
| データ構造 / 重要ファイル | 後から触るとき迷わないように |
| 運用メモ | 「これは触ると壊れる」「ここは変えない」「pm2 再起動はユーザー判断」など |
| TODO | 進行中のタスク（細かい作業は progress アプリ側へ） |
| 関連 | 関連プロンプト・レビュー・アイデア |

## 書かない内容

- 機密情報（API キー / .env の中身 / 認証情報）— vault 全体で禁止
- progress アプリ側で管理する詳細タスクの進捗（重複させない）
- 作業ログ全文（`engineering/reports/` が正本）

## 運用ルール

- アプリの**実装は company 側のそれぞれのリポジトリで完結**させ、ここには「判断・記録」だけ残す
- 仕様変更・運用ルール変更があったら、Claude Code 作業終了時にここへ 1 〜 3 行で追記する
- アーカイブ対象になったアプリは、ノート冒頭に「状態: archived」と書いて残す（削除しない）

## 関連

- [[../03_prompts/claude-code-standard]]
- [[../04_reviews/review-template]]
- [[../05_monetization/ideas]]
