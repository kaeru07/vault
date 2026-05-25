> [!note] 📋 運用ルール v1（vloop10 / 2026-05-25 確定）
> このフォルダ（`chatgpt/`）は **ChatGPT 連携の補助置き場**。現運用は以下に確定:
> - **承認入口の正本**: [[../20_reviews/ChatGPT承認待ち]] + 各 candidate 承認パック
> - **レビューキューの正本**: [[../20_reviews/_review_queue]]
> - **本フォルダの役割**: ChatGPT 連携の**補助コンテンツ置き場**（依頼コピー / 応答要約 / プロンプト雛形 / 月次会話要約）
> - 本格運用ルール: §運用ルール v1 を参照（下記）
> - 詳細経緯: [[../20_reviews/Vault全体棚卸し]] §Phase 1 §暫定 / 案件別ToDo一覧 A-4 解消

---

# chatgpt/ — ChatGPT 連携補助フォルダ

> ChatGPT 由来の要約・レビュー結果・プロンプト雛形・会話ログの一部を残すためのフォルダ。
> GitHub 経由で ChatGPT 側から参照されることを想定。

---

## 運用ルール v1（vloop10 確定）

### 役割の明確化

| 場所 | 役割 | 本フォルダとの関係 |
|---|---|---|
| `20_reviews/ChatGPT承認待ち.md` | **承認待ちの正本**（candidate 単位の承認状態）| 本フォルダ補助 |
| `20_reviews/_review_queue.md` | **レビューキューの正本**（vloop ごとの作業レビュー）| 本フォルダ補助 |
| `20_reviews/<candidate>_ChatGPT承認パック.md` | 各 candidate の判断材料 §1-§14 | 本フォルダ補助 |
| `chatgpt/` （本フォルダ） | **補助コンテンツ**: 依頼コピー / 応答要約 / プロンプト雛形 / 月次会話要約 | （本ページ）|

### 本フォルダを使う判断基準

- **使う**: ChatGPT に渡したコピー元 Markdown を保管したい / 応答要約を残したい / プロンプト雛形を共有したい
- **使わない**: 承認待ち状態の管理（`ChatGPT承認待ち.md` を使う）/ レビュー対象の管理（`_review_queue.md` を使う）

### 命名規約（v1 確定）

| ファイル種別 | 命名規約 | 例 |
|---|---|---|
| ChatGPT 依頼コピー | `YYYY-MM-DD-<topic>.md` | `2026-05-13-progress-obsidian-review.md` |
| ChatGPT 応答要約 | `YYYY-MM-DD-<topic>-response.md` | `2026-05-13-progress-obsidian-review-response.md` |
| プロンプト雛形 | `prompt-<name>.md` | `prompt-candidate-review.md` |
| 月次会話要約 | `YYYY-MM-conversation-summary.md` | `2026-05-conversation-summary.md` |

### 機密スキャン必須

本フォルダにファイル追加時は以下のパターンを必ずチェック:
- `api[_-]?key` / `sk-` / `Bearer ` / `password` / `secret` / `token`
- `-----BEGIN`（SSH 鍵 / 証明書本文）
- 本番 DB 接続文字列
- 個人情報 / 第三者非公開情報

→ 該当パターンを含む場合は**ファイル追加を中止**し、伏字 or 環境変数名へ書き換える。

### 更新運用

- ChatGPT 連携で残しておきたい補助コンテンツが発生したら、本フォルダへ追加
- 承認状態の更新は `20_reviews/ChatGPT承認待ち.md` 側で実施（本フォルダでは行わない）
- 月次会話要約は手動エクスポート（自動化しない）

---

## 入れていいもの

- ChatGPT に渡したレビュー依頼の **コピー元 Markdown**（progress の「レビュー用コピー」結果）
- ChatGPT から返ってきた要点 / 指摘 / 追加 ToDo の要約
- ChatGPT 用のプロンプト雛形（Claude 用とは別に管理したい場合）
- ChatGPT で生成された設計案・市場リサーチ要約
- 月次の ChatGPT 会話要約ダンプ（手動エクスポート）

## 入れないもの

- ChatGPT 会話の生ログ全文（容量が大きく、機密混入リスク）
- API キー / 認証情報（vault 全体で禁止）
- ChatGPT が出力した個人情報・第三者の非公開情報

## 命名規約

- `YYYY-MM-DD-<topic>.md` 形式
- 例: `2026-05-13-progress-obsidian-review.md`
- ChatGPT 用プロンプト雛形は `prompt-<name>.md`

## ファイルの最低構成（テンプレ）

```markdown
---
date: YYYY-MM-DD
source: chatgpt
related_run_ids: []
related_pages: []
---

# {title}

## 依頼内容（要約）

## ChatGPT の応答（要点）

## 採用 / 修正後採用 / 棄却

## 次の一手
```

## 関連

- [[../20_reviews/ChatGPT承認待ち]] — 承認待ちの正本
- [[../20_reviews/_review_queue]] — レビューキューの正本
- [[../03_prompts/Claude-Code標準運用]]
- [[../20_reviews/Claude作業レビュー運用]] — 新運用（旧 [[../04_reviews/Claude作業レビュー運用]] は参照のみ）
- [[../00_index]]
- [[../20_reviews/案件別ToDo一覧]] A-4 — chatgpt/ 運用ルール確定（vloop10 解消）
