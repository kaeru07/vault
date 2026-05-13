# 🗂 Vault Index（入口）

> Obsidian Vault の入口ページ。**判断に迷ったらまずここを開く**。各フォルダの README とテンプレートへの導線をすべてここから辿れる。
> 詳細な目的・運用ルールは [[README]] へ。

---

## 🚀 今すぐ開くページ

- 📅 [[01_daily/today]] — 今日の進捗・気付き
- 🎯 [[05_monetization/収益化ロードマップ]] — 最優先方針（短期 / 中期 / 長期）
- 📋 [[05_monetization/収益化候補一覧]] — 全候補テーブル（週次更新）
- 🛑 [[02_apps/途中プロジェクト一覧]] — 停止中・棚上げ
- 🛠 [[03_prompts/Claude-Code標準運用]] — Claude Code の規模・role・検証・連携ルール
- 🔄 [[03_prompts/Vault同期ルール]] — `ob sync` の手順

---

## 📚 フォルダ別インデックス

### 00_inbox/

未整理メモ・思いつき置き場。
- [[00_inbox/README|📖 フォルダ運用]]
- [[00_inbox/inbox|inbox（一行雑記）]]
- [[00_inbox/未整理メモ|未整理メモ]]

### 01_daily/

日次の進捗・振り返り。**毎日 1 行でも書く**。
- [[01_daily/today|today（今日）]]

### 02_apps/

アプリ別の仕様・状態・課題・収益化メモ。**1 アプリ 1 ノート**。
- [[02_apps/README|📖 フォルダ運用]]
- [[02_apps/progress|progress（進捗管理アプリ / ny01）]]
- [[02_apps/nanikiru-shorts|nanikiru-shorts（麻雀何切る動画）]]
- [[02_apps/mahjong|mahjong（麻雀問題アプリ全体）]]
- [[02_apps/shogi-trainer|shogi-trainer（将棋囲い学習アプリ）]]
- [[02_apps/途中プロジェクト一覧|途中プロジェクト一覧]]

### 03_prompts/

Claude Code 用プロンプト雛形 + 運用ルール。
- [[03_prompts/README|📖 フォルダ運用]]
- [[03_prompts/Claude-Code標準運用|Claude-Code 標準運用]] ★まずここ
- [[03_prompts/Vault同期ルール|Vault 同期ルール（ob sync）]]
- [[03_prompts/claude-code-light|claude-code-light（軽量）]]
- [[03_prompts/claude-code-standard|claude-code-standard（標準）]]
- [[03_prompts/claude-code-full|claude-code-full（フル）]]

### 04_reviews/

Claude Code 作業の 1 件レビューを**手動キュレーション**で蓄積。差し戻し・棄却も残す。
- [[04_reviews/README|📖 フォルダ運用]]
- [[04_reviews/Claude作業レビュー運用|レビュー運用ルール]]
- 個別レビュー: `04_reviews/YYYY-MM-DD-<title>.md`（[[90_templates/claude-review-template|テンプレ]]）

### 20_reviews/

作業終了時に **1 作業 1 ファイル**で自動生成するセッションレビュー（ChatGPT 依頼文込み）。
- [[20_reviews/README|📖 フォルダ運用]]
- 個別レビュー: `20_reviews/YYYY-MM-DD_<task-slug>.md`（[[90_templates/session-review-template|テンプレ]]）

### 05_monetization/

収益化方針・候補・撤退ライン。
- [[05_monetization/README|📖 フォルダ運用]]
- [[05_monetization/収益化ロードマップ|🎯 収益化ロードマップ]]
- [[05_monetization/収益化候補一覧|📋 収益化候補一覧]]
- 個別アイデア: `05_monetization/<idea>.md`（[[90_templates/monetization-idea-template|テンプレ]]）

### 06_research/

市場調査・競合分析・Activity Mining 設計。
- [[06_research/GooglePlay市場調査|Google Play 市場調査（俯瞰）]]
- [[06_research/activity-mining|Activity Mining 設計]]
- 個別調査: `06_research/<topic>.md`（[[90_templates/market-research-template|テンプレ]]）

### 90_templates/

新規ノートはまずここからコピーする。
- [[90_templates/app-note-template|app-note-template]] — 1 アプリ単独ノート
- [[90_templates/claude-task-template|claude-task-template]] — 1 作業依頼
- [[90_templates/claude-review-template|claude-review-template]] — 1 作業レビュー
- [[90_templates/monetization-idea-template|monetization-idea-template]] — 1 収益化アイデア
- [[90_templates/market-research-template|market-research-template]] — 1 市場調査

---

## 🔗 progress アプリとの関係

- **事実は progress / 判断は Obsidian** を原則とする
- progress アプリ URL: `http://localhost:3010`
- レビュー待ち（progress 側）: `http://localhost:3010/logs?mode=review`
- 連携設計レビュー（最新）: progress 上で `runId: 20260513-202846`
- 自動連携は実装されていない（Phase A 運用中 → 詳細 [[03_prompts/Claude-Code標準運用]] / `/root/company/CLAUDE.local.md`）

---

## 📌 運用の Quick Reference

### 新規ノートを作る

1. `90_templates/` から該当テンプレをコピー
2. 適切なフォルダへ配置
3. `[[link]]` で関連ページを繋ぐ
4. 完成したらこの 00_index.md にエントリ追加（任意）

### Claude Code 作業終了時

1. progress に POST（[[03_prompts/Claude-Code標準運用]] §progress連携）
2. 該当する場合のみ Obsidian に 1〜3 行追記（CLAUDE.local.md §Obsidian要約追記ルール）
3. Vault 編集していたら `ob sync` 実行（[[03_prompts/Vault同期ルール]]）

### 機密情報の取り扱い

- `.env` / API キー / トークン / パスワードは**絶対に書かない**
- 過去メモに混入を発見したら即座に削除し、漏洩キーはローテーション

---

## 🗺 構造の俯瞰（テキスト）

```
00_index.md          ← このページ
README.md            ← Vault 全体の目的・運用ルール
00_inbox/            未整理メモ
01_daily/            日次ノート
02_apps/             アプリ別ノート（1 アプリ 1 ノート）
03_prompts/          Claude Code プロンプト・運用ルール
04_reviews/          作業レビュー（時系列）
05_monetization/     収益化方針・候補
06_research/         市場調査・Activity Mining
90_templates/        新規ノート用テンプレート
attachments/         画像など（private/ 配下は git 除外）
```

---

## 🧭 こんなとき何を開く

| やりたいこと | 開くページ |
|---|---|
| 今日何やるか決めたい | [[01_daily/today]] → [[05_monetization/収益化ロードマップ]] |
| 収益化候補を見直したい | [[05_monetization/収益化候補一覧]] |
| アプリの状態を確認したい | [[02_apps/README]] → 各アプリ |
| Claude Code に作業を頼みたい | [[90_templates/claude-task-template]] |
| レビューを残したい | [[90_templates/claude-review-template]] |
| 市場調査を始めたい | [[06_research/GooglePlay市場調査]] → [[90_templates/market-research-template]] |
| 棚上げ案件を見直したい | [[02_apps/途中プロジェクト一覧]] |
| Vault のルールを知りたい | [[README]] / [[03_prompts/Claude-Code標準運用]] |

---

## 📅 メンテナンス

- **週次**: [[05_monetization/収益化候補一覧]] の状態・次アクションを更新
- **月次**: [[06_research/activity-mining]] の Phase 0 レビュー（手動の振り返り）
- **随時**: 育った [[00_inbox/inbox]] / [[00_inbox/未整理メモ]] を該当フォルダへ移動

---

## 関連（外部の正本）

- `/root/company/CLAUDE.md` — company 全体の運用司令塔
- `/root/company/CLAUDE.local.md` — 3 原則 + progress 連携 + Vault 同期 + Obsidian 要約追記ルール
- `/root/company/apps/ny01/progress` — 作業履歴の正本（VPS ローカル / pm2:3010）
- `/root/company/engineering/reports/` — focus-work / dangerous-work レポート
