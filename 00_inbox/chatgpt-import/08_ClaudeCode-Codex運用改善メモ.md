---
title: Claude Code / Codex 運用改善メモ（ChatGPT会話より）
type: prompt
created: 2026-05-17
runId: 20260517-185136
---

# Claude Code / Codex 運用改善メモ

> AI開発/ClaudeCode + progress 系 high 会話から抽出した運用改善点。多くは既にCLAUDE.local.mdへ反映済。

## 確立済み（CLAUDE.local.md等へ反映済）

- **POST範囲拡大**: 設計判断/運用方針/複数案比較/収益化/DB/セキュリティ判断は changedFiles:[] でも progress POST必須 → [[important/2026-05-13_意思決定ログ-POST範囲拡大ルール]]
- **Opus 4.7 プロンプト方針**: 「管理は厳密・実装は自由」。手順・hooks・useMemo等を過剰固定しない。共通プロンプト+案件固有(目的/対象/変更OK/禁止/完了条件)が最強 → [[important/2026-05-13_Opus47プロンプト方針]]
- **progress=実行 / Obsidian=判断** の役割分担、review_queueをレビュー状態の正本に → [[important/2026-05-12_Obsidian活用法-progress分業設計]]
- **commit後push必須 / mirror push漏れ対策**（sync-vaultとGitHub vaultが別系統で反映漏れが起きていた）→ [[important/2026-05-14_Claude有料プラン-AgentSDKクレジット]]

## 未反映・検討余地（運用改善候補）

- **Codexを読み取り専用レビュアーとして常用**（Claude主導→Codex対立レビュー、execution-runs.jsonを渡し標準出力でレビュー要約）→ [[important/2026-05-16_ClaudeとCodexのAgent活用]]
- **prompt_inbox と work_queue の分離**（思いつき貯め場と承認済み実行キュー）→ 現 _review_queue 運用との整合確認が必要
- **Claude有料プラン Agent SDK 月次クレジット(2026-06-15〜)** の自動化費用設計
- obsidian-sync-vault が git管理外 → バックアップ方針の点検（一部のみ反映済）

## 次に活かせるポイント

- 上記「未反映」を [[05_次にやるToDo候補]] と突き合わせ、運用ルール追記 or progress起票を判断
- プロンプト作成時は「管理=厳密・実装=自由」を既定にする

## 関連
- [[02_重要会話候補]] / [[03_カテゴリ別まとめ]] / `03_prompts/Claude-Code標準運用`（既存Vault）
