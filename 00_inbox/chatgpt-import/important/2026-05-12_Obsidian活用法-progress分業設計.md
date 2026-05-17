---
title: Obsidian活用法 — progressとの分業設計（分岐会話を統合）
date: 2026-05-12
category: Obsidian/GitHub連携
importance: high
use: review
related_app: progress, vault
secret: false
note: 「Obsidian活用法提案」系の枝分かれ会話6件超を1件に統合
---

# Obsidian活用法 — progressとの分業設計

> 「obisdianの活用法は？」から派生した枝分かれ会話（2026-05-12〜14, 最大198msg）を統合要約。

## 一言要約
**progress = ToDo実行管理 / Obsidian = 背景・理由・プロンプト資産・長期記憶** という役割分担を確定。

## 決定事項 / 結論
- progress=事実・実行履歴、Obsidian=判断・要約・長期記憶。rawReport全文はObsidianへ流さない
- Obsidianは「ToDo操作のメイン画面」にはしない（操作はprogressが向く）。背景・再利用プロンプト・放置案件の掘り起こしに使う
- 「プロンプト貯め場（prompt_inbox）」と「承認済み実行キュー（work_queue）」を分ける運用案
- `_review_queue.md` のチェック状態をレビュー状態の正本にする（iPhoneで `[ ]→[x]`）→ **実装済み**
- 段階導入 A→B→C（自動書き出しAPIは後回し）。monetizationImpact/theme を早期付与
- **要注意指摘: obsidian-sync-vault がgit管理外** → バックアップ方針を先に決めるべき（一部CLAUDE.local.mdに反映済）

## 次に活かせるポイント
- prompt_inbox/work_queue 分離は未実装 → [[../05_次にやるToDo候補]] 中優先
- READMEの「commit&push→iPhone pull」記述は ob sync 中心へ更新すべき（古い記述残存）

## 出典
ChatGPT「Obsidian活用法提案」系 枝分かれ多数（2026-05-12〜14）。多くの運用ルールは既にCLAUDE.local.mdへ反映済。
