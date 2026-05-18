---
title: Claude Code コマンド（git 管理正本）
type: prompt-ops
created: 2026-05-18
---

# Claude Code コマンド（git 管理正本）

`/root/company/.claude/commands/` 配下の Claude Code スラッシュコマンドのうち、
**バージョン管理したいものの正本をここに置く**。`/root/company` 自体は git 管理外
（`.git` が空）のため、git 管理下の本 Vault に正本を置き、コマンドディレクトリ側は
**symlink** で参照する。

## 配置規約

- 正本: `obsidian-vault/03_prompts/claude-commands/<name>.md`（git 管理・push 対象）
- 参照: `/root/company/.claude/commands/<name>.md` → 上記への symlink

symlink 経由でも Claude Code はコマンドを認識する（動作確認済み: `/vloop`）。

## 現在の管理対象

| コマンド | 正本 | 用途 |
|---|---|---|
| `/vloop` | `vloop.md` | Vault ToDo を pull して連続処理（最大10件/4時間） |

`/vrun`（1件実行用）は未移管。同じ手順（cp → rm → symlink → commit/push）で移管可能。

## 編集時の注意

- 編集は**正本（本フォルダ側）**を直接編集する。symlink 側を編集しても実体は同じ
- 変更したら本 Vault で commit / push する（[[../GitHub反映ルール]] に準拠）
- ob sync は不要（本 Vault は GitHub 管理側。`obsidian-sync-vault` とは別系統）
