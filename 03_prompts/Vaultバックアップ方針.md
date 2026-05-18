---
title: obsidian-sync-vault バックアップ方針（確定版）
type: prompt-ops
status: active
created: 2026-05-18
updated: 2026-05-18
source: 00_inbox/chatgpt-import/05_次にやるToDo候補.md（中優先・複数回指摘）
---

# obsidian-sync-vault バックアップ方針（確定版）

> `05_次にやるToDo候補.md`「obsidian-sync-vault のバックアップ方針を確定」の確定ドキュメント。
> CLAUDE.local.md に分散していたバックアップ関連ルールを**点検・集約**し、抜けを明示する。
> 本ファイルは方針の集約であり、**破壊的操作（rm/mv/リネーム）は引き続きユーザー判断**。

## 正本構造（再掲・現状）

| 種別 | パス | git | ob sync | 冗長化 |
|---|---|---|---|---|
| 稼働 Vault（正本） | `/root/company/obsidian-sync-vault` | **なし** | あり（bidirectional） | ob sync クラウドのみ |
| クラウドコピー | Obsidian Sync サーバー | n/a | n/a | iPhone と共有 |
| GitHub 管理 Vault | `/root/company/obsidian-vault` | あり（vault.git） | なし | git 履歴 |

**最大リスク**: 稼働 Vault が git 管理外。`rm -rf` 事故時は ob sync クラウドからのみ復元可、かつ削除がクラウドへ伝播し得る。

## 確定方針（チェックリスト化）

### A. 破壊的操作の禁止（既存ルールの再確認）
- [x] 稼働 Vault へのフォルダ単位 `rm` / `mv` は Claude 独断禁止 → ユーザー判断（CLAUDE.local.md 反映済）
- [x] Claude は `Write` / `Edit`（追記・上書き）まで。リネーム/削除は提案で止める（反映済）

### B. スナップショット（点検で判明した「残り」）
- [x] 大整理前の `tar` スナップショット推奨はルール化済
- [ ] **未確定→本方針で確定**: 定期スナップショットの**頻度と保存先**が未規定だった
  - 確定: 大整理前は必須（既存）＋ **週次の軽量 tar を推奨**（任意・ユーザー実行）
  - 保存先: `/root/company/_backups/obsidian-sync-vault-YYYYMMDD.tar.gz`
  - コマンド例（ユーザー実行・Claude は提案のみ）:
    `tar czf /root/company/_backups/obsidian-sync-vault-$(date +%Y%m%d).tar.gz -C /root/company obsidian-sync-vault`
- [ ] **保持世代数**が未規定だった → 確定: 直近 4 世代を目安に手動間引き（自動削除はしない）

### C. GitHub 管理 Vault による冗長化（既存運用の位置づけ確定）
- [x] sync-vault → obsidian-vault ミラー → commit/push が履歴冗長化を兼ねる（GitHub反映ルール）
- [x] 責務分離: sync-vault は git 触らず / obsidian-vault は ob sync 触らず（反映済）
- 確定: **GitHub 管理 Vault への push 完了をもって「履歴バックアップ済」とみなす**（[[GitHub反映ルール]] の push 必須化と整合）

### D. ob sync 失敗時（既存ルール再確認）
- [x] `Fully synced` 確認後に次工程（反映済）
- [x] エラー時も Vault 側ファイルは元に戻さない（反映済）

## 点検結果サマリー（残り＝確定したもの）

| 項目 | 点検前 | 本方針で確定 |
|---|---|---|
| 破壊的操作の扱い | ルール済 | 変更なし |
| スナップショット頻度 | 大整理前のみ規定 | **＋週次軽量 tar 推奨** を追加確定 |
| スナップショット保持世代 | 未規定 | **直近4世代・手動間引き** に確定 |
| 履歴冗長化の定義 | 暗黙 | **GitHub push 完了＝履歴バックアップ済** に確定 |
| 自動復旧 | しない（規定済） | 変更なし |

## 未対応点 / 仮説
- 週次 tar は cron 自動化も可能だが、自動化は別途ユーザー判断（本方針は「推奨・手動」で確定。仮説: 自動化はバックアップ消失検知とセットで導入すべき）
- 稼働 Vault の git 化は ob sync との競合懸念があり本方針では採用しない（責務分離維持）
- 本ファイルは GitHub 管理 Vault 側の集約。稼働 Vault 側 `03_prompts/Vault同期ルール.md` への反映はユーザー判断（Claude は sync-vault を破壊的に触らない）

## 次の一手
1. ユーザーが「週次 tar 推奨・4世代保持」を運用に取り込むか判断
2. 取り込む場合、必要なら CLAUDE.local.md のバックアップ節へ1行追記（頻度・世代）
3. 稼働 Vault 側 Vault同期ルール.md への同期はユーザー実行

## 関連
- [[Vault同期ルール]] / [[GitHub反映ルール]] / [[Claude-Code標準運用]]
- `/root/company/CLAUDE.local.md` § Obsidian Vault のバックアップ方針（正本ルール）
- 出典候補: 00_inbox/chatgpt-import/05_次にやるToDo候補.md
