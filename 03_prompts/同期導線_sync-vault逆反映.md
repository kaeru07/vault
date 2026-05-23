---
title: 同期導線 — obsidian-vault → sync-vault 逆反映ルール（Issue #56）
type: workflow-ops
issue: kaeru07/vault#56
created: 2026-05-23
updated: 2026-05-23
status: active
relatedIssues: [kaeru07/vault#54, kaeru07/vault#55]
---

# 同期導線 — obsidian-vault → sync-vault 逆反映ルール

> Issue #56。ChatGPT が GitHub 上で直接 commit したファイル群が iPhone Obsidian から見えない問題の構造的解決ルール。

## 1. 問題の構造（Issue #56 調査結果）

| Vault | パス | 役割 | 同期 |
|---|---|---|---|
| **稼働 Vault** | `/root/company/obsidian-sync-vault` | iPhone Obsidian と同期 | ob sync（クラウド経由） |
| **GitHub 管理 Vault** | `/root/company/obsidian-vault` | GitHub remote `kaeru07/vault` | git push/pull |

**公式運用（CLAUDE.local.md § GitHub 管理 Vault 反映ルール）**:
- sync-vault を編集 → ob sync → rsync で obsidian-vault へミラー → git push
- **方向は一方向（sync-vault → obsidian-vault）**

**問題**:
- ChatGPT が GitHub 上で直接 commit / 編集すると obsidian-vault のみに反映
- iPhone Obsidian は sync-vault 経由なので **GitHub 直接 commit ファイルが見えない**
- 例: `00_inbox/Vaultの見方_...md`（ChatGPT 起票・7795 byte / 2026-05-22）が sync-vault に不在だった

## 2. 逆反映ルール（手動運用・本サイクルで初運用）

ChatGPT or Claude が **obsidian-vault に直接 commit** した場合、以下を実施:

1. obsidian-vault 側のファイル / 更新を特定（git log + diff）
2. sync-vault 側に同名ファイルがあるか確認
3. **不在ファイル**: obsidian-vault → sync-vault へ `cp` で追加（破壊的でない）
4. **既存ファイル**: 内容比較 → 古い方を新しい方で上書き（変更時刻 / 行数 / 内容で判定）
5. ob sync を実行 → iPhone に反映
6. obsidian-vault 側もミラー再実行は不要（obsidian-vault が原本のため）

## 3. 自動化の選択肢（将来検討）

| 案 | 内容 | リスク |
|---|---|---|
| A. 手動運用（現状） | ChatGPT 直接 commit 時に Claude が逆反映 | 反映漏れリスク |
| B. cron 双方向 rsync | 1 時間ごとに sync-vault ↔ obsidian-vault を同期 | コンフリクト時の自動解決ロジックが必要 |
| C. GitHub webhook | obsidian-vault に push があったら sync-vault に rsync + ob sync | webhook 受信サーバが必要 |

→ 現状は A 手動運用。B/C は実装コスト見合いで人間判断。

## 4. iPhone 検索容易性（英数字エイリアス）

日本語ファイル名（`00_index.md` / `Vaultの見方_...md`）は iPhone Obsidian 検索でヒットしにくい。
英数字入口として **`00_START_HERE.md`** を sync-vault と obsidian-vault 両方に配置。iPhone は「START」「HERE」「00_START」で検索可能。

本ファイルから `[[link]]` で日本語ファイルへ辿る運用とする。

## 5. 本サイクルで実施した逆反映（Issue #56 対応記録）

| 対象 | sync-vault 側状態 | 対応 |
|---|---|---|
| `00_index.md` | 古い版（May 14） | obsidian-vault 最新版で上書き |
| `00_inbox/Vaultの見方_...md` | 不在 | obsidian-vault からコピー追加 |
| `20_reviews/ChatGPT承認待ち.md` | 不在 | obsidian-vault からコピー追加 |
| `20_reviews/README.md` | 古い版 | obsidian-vault 最新版で上書き |
| `20_reviews/candidate-001_ChatGPT承認パック.md` | 古い or 不在 | obsidian-vault 最新版で上書き |
| `00_START_HERE.md` | なし | 英数字入口として新規作成（両 Vault に配置） |

## 6. 完了条件（Issue #56）と現状

| 完了条件 | 現状 | 達成手段 |
|---|---|---|
| iPhone Obsidian で見るべき入口ファイルが明確 | ✅ | 00_START_HERE.md（英数字入口） |
| GitHub 管理 Vault と iPhone Obsidian 側の差分原因が説明 | ✅ | 本ファイル §1 + 5 ファイル状態表 §5 |
| 必要な入口ファイルが iPhone 側にも見える状態 | ✅ | 5 ファイル sync-vault に反映 + ob sync 実行 |
| 日本語ファイル名が見つからない場合の代替入口 | ✅ | 00_START_HERE.md（英数字） |
| 00_index.md / 見方ガイド / 承認待ち / scenarios の導線整理 | ✅ | 00_START_HERE.md から [[link]] で全て辿れる |
| commit/push 済 | 本ファイル commit/push 済（後続） | — |

## 関連

- [[GitHub反映ルール]]（公式運用ルール）
- [[Vault同期ルール]]（ob sync 手順）
- [[../00_START_HERE]]（iPhone 入口）
- [[../00_index]]（Vault 全体索引）
- Issue: kaeru07/vault#56（関連 #54 / #55）
