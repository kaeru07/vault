---
title: Claude有料プラン — Agent SDK向け月次クレジット（6/15〜）
date: 2026-05-14
category: AI開発工場/ClaudeCode運用
importance: high
use: research
related_app: all
secret: false
---

# Claude有料プラン Agent SDK 月次クレジット

## 一言要約
2026-06-15 から有料Claudeプランで、プログラム利用向けの月次クレジットが付く（Agent SDK / `claude -p` / Claude Code GitHub Actions / Agent SDK製サードパーティ）。

## 決定事項 / 結論
- 対象: Claude Agent SDK / `claude -p` / Claude Code GitHub Actions / Agent SDK上のサードパーティアプリ
- 自動化（夜間バッチ・大量レビュー・自律実行）の費用設計に直結する枠
- 付随論点: Vault が obsidian-sync-vault と GitHub vault で別系統 → Codex/progress関連の反映漏れが起きていた（mirror push 忘れ）。この会話で「sync側にはあるがGitHub mirror未push」パターンを特定

## 次に活かせるポイント
- 6/15以降のクレジット枠を前提に自動化（市場調査バッチ/レビュー）の運用設計を決める（[[../05_次にやるToDo候補]] 中優先）
- mirror push 漏れ対策は現 § commit後push必須化 / GitHub反映ルールに反映済

## 出典
ChatGPT会話「Claude有料プランの変更」(2026-05-14, 78msg)。
