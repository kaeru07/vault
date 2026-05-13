# chatgpt/ — ChatGPT 連携メモ

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
