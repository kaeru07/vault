---
title: START HERE — iPhone Obsidian からまず開くページ
type: vault-entry-en
issue: kaeru07/vault#56
created: 2026-05-23
updated: 2026-05-23
status: active（iPhone 検索容易性のための英数字入口）
---

# 00_START_HERE

> **iPhone Obsidian で迷ったらここから開く**。英数字ファイル名のため iPhone 検索でも見つけやすい。
> 日本語ファイル名（00_index.md / Vaultの見方_...md など）は iPhone Obsidian の検索でヒットしにくいため、本ファイルを英数字エイリアスとして配置。

---

## iPhone Obsidian で見るべき順番

### 1. Vault の全体構造を見る
[[00_index]] — Vault 全体の構造索引（フォルダ別・テンプレ・運用 QR）

### 2. 収益化フローの判断手順を見る
[[00_inbox/Vaultの見方_どこを見れば何がわかるか]] — candidate / idea_pool / research / epics / logs / vloop の見方

### 3. ChatGPT 承認待ちを見る（今の判断対象）
[[20_reviews/ChatGPT承認待ち]] — candidate-001 等の方向性承認キュー

### 4. candidate-001 の判断材料を見る
[[20_reviews/candidate-001_ChatGPT承認パック]] — §1-§14 完備の承認判断パック

### 5. Epic 全体進捗を見る
[[05_monetization/epics]] — Epic A/B/C のステータスと残作業

---

## iPhone 検索のコツ（Issue #56）

iPhone Obsidian の検索キーワード:

- 全体索引: `00_index` または `index`
- 見方ガイド: `Vaultの見方` または `00_inbox`
- 承認待ち: `承認待ち` または `ChatGPT` または `20_reviews`
- candidate 詳細: `candidate-001` または `承認パック`
- Epic 進捗: `epics`

> 日本語ファイル名は検索でヒットしないことがある。その場合は本ファイル（00_START_HERE）から `[[link]]` で辿るのが確実。

---

## なぜこのファイルが必要か（Issue #56 背景）

GitHub 管理 Vault（`obsidian-vault`）と稼働 Vault（`obsidian-sync-vault` / iPhone と同期）は **別物**。

- ChatGPT が GitHub 上で直接 commit したファイルは `obsidian-vault` に入る
- `obsidian-sync-vault` には自動反映されない
- iPhone Obsidian は `obsidian-sync-vault` 経由で見るため、`obsidian-vault` 直接 commit ファイルは iPhone から見えない

→ 本サイクル（Issue #56 対応）で、不在だった入口ファイルを sync-vault にもコピー + 英数字入口（本ファイル）を追加して iPhone から辿れるようにした。

---

## 同期導線（運用方針）

- **稼働 Vault**: `/root/company/obsidian-sync-vault`（ob sync で iPhone と同期）
- **GitHub 管理 Vault**: `/root/company/obsidian-vault`（git remote: vault.git）
- **公式運用**: sync-vault → obsidian-vault に rsync ミラー（一方向）
- **逆方向（obsidian-vault → sync-vault）**: ChatGPT が GitHub 直接 commit したファイルがあるときは個別コピー（Issue #56 で対応）
- 詳細: [[03_prompts/GitHub反映ルール]] / `/root/company/CLAUDE.local.md` § GitHub 管理 Vault 反映ルール

---

## 関連

- [[00_index]]（Vault 全体構造索引）
- [[00_inbox/Vaultの見方_どこを見れば何がわかるか]]（収益化フロー見方ガイド）
- [[20_reviews/README]]（ChatGPT 承認入口）
- Issue: kaeru07/vault#56
