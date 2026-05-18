---
title: prompt_inbox と work_queue の分離運用（提案ドラフト）
type: prompt-ops
status: proposal
created: 2026-05-18
updated: 2026-05-18
source: 00_inbox/chatgpt-import/05_次にやるToDo候補.md（中優先・出典 Obsidian活用法提案）
---

# prompt_inbox と work_queue の分離運用（提案ドラフト）

> `05_次にやるToDo候補.md` 中優先「prompt_inbox と work_queue の分離運用を検討」の**提案ドラフト**。
> 本ファイルは方針案。**正式採用はユーザー / pm 判断**。AI はこのドラフトに基づくフォルダ移動・大量リネームを enact しない
> （[[../CLAUDE.local.md 由来]] Vault 破壊的操作はユーザー判断 / [[Vault同期ルール]]）。

## 課題（現状）

`00_inbox/` が「思いつきプロンプト」「ChatGPT 投函」「登録済タスク」「未整理メモ」「import」を**混在**して抱えている。
承認前の発想と、承認済みで実行待ちのものが同じ場所にあり、「次に何を実行してよいか」が判別しにくい。

## 提案: 3 レーン + レビューを役割分離（命名はリネームせず役割で定義）

| レーン | 役割 | 現状の対応 | 状態の正本 |
|---|---|---|---|
| **prompt_inbox** | 思いつき・未承認の発想/依頼の貯め場 | `00_inbox/`（混在を整理） | なし（未承認） |
| **work_queue** | 人間/pm が承認した「実行してよい」キュー | `00_inbox/tasks/` または `07_tasks/` を昇格先に | 承認メタ（担当/優先/完了条件） |
| **execution（実行記録）** | 実際に走った作業の正本 | progress ExecutionRun（:3010） | progress（[[Claude-Code標準運用]]） |
| **review_queue** | 実行後レビューの追跡 | `20_reviews/_review_queue.md` | チェックボックス（既存運用・変更しない） |

> **重要**: review_queue は work_queue と別物（実行**後**の検証レーン）。今回の分離で `_review_queue.md` 運用は変更しない。

## 遷移ルール（案）

```
prompt_inbox（未承認・自由投函）
   │  人間/pm が「やる」と判断（承認）
   ▼
work_queue（承認済み・実行待ち / 担当・優先・完了条件付き）
   │  Claude Code / Codex が着手 → commit/push → progress POST
   ▼
execution（progress ExecutionRun = 実行の正本）
   │  作業後レビュー登録
   ▼
review_queue（_review_queue.md / ChatGPT・人間レビュー）
```

- **AI は prompt_inbox の項目を勝手に work_queue へ昇格しない**（承認は人間/pm）。
  収益化シナリオの candidate→approved と同じ思想（[[../05_monetization/収益化シナリオ承認フロー]]）
- AI が触れてよいのは work_queue（承認済み）以降。prompt_inbox は読み取り・整理提案まで
- フォルダ物理移動・リネームは破壊的操作のため**ユーザー判断**（提案に留める）

## 最小実装案（リネームなし・運用ルールのみで開始）

1. `00_inbox/README.md` に「ここは prompt_inbox（未承認の貯め場）」と明記
2. 承認されたものだけ `00_inbox/tasks/`（= work_queue）へ人間/pm が移す
3. AI は `00_inbox/tasks/` 配下の承認済みのみ着手対象とする（本セッションの ZIP タスクはこの運用に合致）
4. 物理フォルダ名 `prompt_inbox/` `work_queue/` への改称は将来オプション（`[[link]]` 一括書換が必要なため保留）

## 未対応点 / 仮説
- 物理リネームは `[[link]]` / ob sync / iPhone 互換に影響するため本ドラフトでは提案せず（[[Vault同期ルール]] のバックアップ方針に従う）
- `07_tasks/inbox` と `00_inbox/tasks` の役割重複は要統合判断（仮説: work_queue 正本は `00_inbox/tasks/` に寄せる）
- 候補ファイルの着手判断はユーザー/pm のため status: proposal で据え置き

## 次の一手
1. ユーザー / pm が「リネームなし・運用ルールのみ」案を採用するか判断
2. 採用時、`00_inbox/README.md` に役割定義を 1 段落追記（破壊的でない）
3. `07_tasks` と `00_inbox/tasks` のどちらを work_queue 正本にするか決定

## 関連
- [[Claude-Code標準運用]] / [[Vault同期ルール]] / [[../20_reviews/README]]
- [[../05_monetization/収益化シナリオ承認フロー]]（承認ゲート思想の流用元）
- 出典候補: 00_inbox/chatgpt-import/05_次にやるToDo候補.md
