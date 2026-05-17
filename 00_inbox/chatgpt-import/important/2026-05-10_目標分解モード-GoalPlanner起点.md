---
title: 目標分解モード提案 — Goal Planner の起点
date: 2026-05-10
category: progressアプリ
importance: high
use: app_design
related_app: progress
secret: false
---

# 目標分解モード提案（Goal Planner 起点）

## 一言要約
「目標を伝えたら、人間/Claude/Codexのやるべき順序と進捗を可視化し、すぐ続きが分かる仕組み」要望 → progress の **Goal Planner（目標分解モード）実装の発端**。

## 決定事項 / 結論
- ToDoを1件ずつ手追加するのが面倒 → 目標（例: 資格アプリをGoogle Play公開し広告収入）から動線を自動分解
- Goal由来ToDoは `pending_approval` で登録（勝手にキューへ流さない）= 安全設計として妥当
- レビュー指摘: Goal Planner UI上で pending_approval が「次の一手」として強表示されると着手OKと誤解されうる → 承認状態の明示表示が必要
- → **Goal Planner は実装済み**（progress runId 20260511-232927 等）。本会話は設計意図の出典

## 次に活かせるポイント
- 既に実装済みのため新規着手不要。承認状態の表示改善が残課題（観察項目）
- 「目標→動線分解」の設計思想は今後の機能追加時の参照点

## 出典
ChatGPT会話「目標分解モード提案」(2026-05-10, 31msg)。
