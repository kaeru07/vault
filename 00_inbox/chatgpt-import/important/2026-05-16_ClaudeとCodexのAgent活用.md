---
title: ClaudeとCodexのAgent活用（Codex読み取り専用レビュー）
date: 2026-05-16
category: AI開発工場/ClaudeCode運用
importance: high
use: review
related_app: progress, vault
secret: false
---

# ClaudeとCodexのAgent活用

## 一言要約
Codex を「本格SDK利用」ではなく **Codex CLI を読み取り専用で呼ぶレビュアー**として位置づけるのが現実的、という結論。

## 決定事項 / 結論
- Claude Code 主導 → Codex で対立レビューする構成が現時点で最も安全
- Codex公式: Python SDK なし / TypeScript SDK あり。今回方式は `Python → codex exec subprocess`
- 読み取り専用設計（`execution-runs.json` を渡し標準出力でレビュー要約+改善ToDo）なら progress 本体を壊さない
- 「Codex → Claude Code を呼ぶ」公式機能は無い。やるなら自作ラッパー（Plugin/Hook/MCP経由）

## 次に活かせるポイント
- Codex を execution-runs レビューの常用2ndオピニオンにする運用を確立
- progress本体・正本JSON・.env は read-only を厳守

## 出典
ChatGPT会話「ClaudeとCodexのAgent活用」(2026-05-16, 36msg)。関連: [[../02_重要会話候補]]
