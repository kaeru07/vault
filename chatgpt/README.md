> [!warning] ⏸ 暫定 / 運用ルール未確定
> このフォルダ（`chatgpt/`）は **用途未確定の暫定置き場**。現在は README のみで実体ファイルなし。
> ChatGPT 連携の正本入口は [[../20_reviews/ChatGPT承認待ち]] / [[../20_reviews/_review_queue]] を使用してください。
> 詳細は [[../20_reviews/Vault全体棚卸し]] §Phase 1 §暫定（運用ルール未確定）を参照。
> chatgpt/ フォルダの本格運用ルール確定は [[../20_reviews/案件別ToDo一覧]] A-4 残作業。

---

# chatgpt/ — ChatGPT 連携メモ（暫定置き場）

> ChatGPT 由来の要約・レビュー結果・プロンプト雛形・会話ログの一部を残すためのフォルダ。
> GitHub 経由で ChatGPT 側から参照されることを想定。

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

- [[../03_prompts/Claude-Code標準運用]]
- [[../04_reviews/Claude作業レビュー運用]]
- [[../00_index]]
