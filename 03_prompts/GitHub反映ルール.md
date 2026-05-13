# GitHub 管理 Vault 反映ルール

> Claude Code が Obsidian Vault を編集した後、**GitHub へ commit/push して ChatGPT・Codex から参照可能にする**ための運用ページ。
> Claude Code 側の正本は `/root/company/CLAUDE.local.md` § GitHub 管理 Vault 反映ルール。

---

## Vault 構成（確定 2026-05-13）

| パス | 役割 | 同期手段 | 直接編集 |
|---|---|---|---|
| `/root/company/obsidian-sync-vault/` | **稼働 Vault**（日々の編集・iPhone） | `ob sync` クラウド bidirectional | ○ |
| `/root/company/obsidian-vault/` | **GitHub 管理 Vault**（共有・履歴・ChatGPT） | git push to `vault.git` | × （ミラー先のため） |

責務分離: sync-vault は git を触らない / obsidian-vault は ob sync を触らない。

---

## 標準フロー（Vault 編集ありの作業）

1. `obsidian-sync-vault` 配下を編集
2. **機密パターン事前チェック**（`api[_-]?key` / `sk-` / `Bearer ` / `password` / `secret` / `token` / `-----BEGIN`）
3. **ob sync** 実行 → `Fully synced` 確認（iPhone 反映）
4. **sync-vault → obsidian-vault ミラー**:
   ```bash
   rsync -av \
     --exclude='.git/' \
     --exclude='.obsidian/' \
     --exclude='attachments/private/' \
     --exclude='.DS_Store' \
     --exclude='.claude/' \
     /root/company/obsidian-sync-vault/ /root/company/obsidian-vault/
   ```
5. **obsidian-vault でも機密スキャン**（重複確認）
6. **git status / diff** 確認（変更ファイル名と件数）
7. **git add → commit → push**:
   - commit message: 内容が分かる短い形式（日英混在可）
   - 例: `update apps: progress.md frontmatter追加 (runId: XXXXXXXX-XXXXXX)`
8. **progress POST**（既存ルール / changedFiles は論理変更のみ）
9. **最終応答**: 完了マーカー 6 項目（GitHub反映を含む）

---

## コミット粒度

- **作業単位で 1 commit**（複数作業を 1 commit にまとめない）
- main を壊す可能性が高い変更は branch を切る（現状 main 直 push）
- `_backups/` / `.claude/` / `.obsidian/` は .gitignore 済み

---

## push 前チェックリスト（必須）

- [ ] 機密パターンスキャンが全ヒット = ルール本文中の例示のみ（実値なし）
- [ ] `.env` / `.env.*` / `*.key` / `*.pem` / `*.sqlite` / `*.db` が含まれていない
- [ ] `attachments/private/` 配下が含まれていない
- [ ] `git status` で意図しないファイルが出ていない

---

## スキップ可

- 単純な事実確認・1 行質問・了解応答（POST 省略可と同じ条件）
- Vault 編集を伴わない応答（編集ゼロ = GitHub 反映する内容なし）
- ユーザーが「GitHub 連携不要」「push するな」と明示

---

## 失敗時の扱い

- push 失敗（認証 / ネットワーク）→ ユーザーへ報告。自動復旧は試みない
- merge コンフリクト → 自動解決せず、ユーザー判断に回す
- ミラーで意図しない上書きが起きた → 直前のスナップショット（`_backups/obsidian-vault-*.tar.gz`）から復元提案

---

## なぜこのルールがあるか

- ChatGPT は GitHub URL 経由でしか Markdown を参照できない
- iPhone Obsidian は `ob sync` で繋がるが、ChatGPT / Codex / 第三者レビューには共有しにくい
- 履歴を git で残せば「いつ何を判断したか」を Activity Mining で追跡可能になる
- sync-vault と obsidian-vault を分離することで `ob sync` と `git` の同時運用の競合リスクを最小化

---

## 完了マーカー 6 項目（更新版）

```
✓ 完了状態: <一行サマリー>
✓ ExecutionRun POST: runId XXXXXXXX-XXXXXX
✓ Obsidian追記: <ファイル名> / 行数: N / n/a
✓ ob sync: Fully synced / n/a
✓ GitHub反映: commit <短ハッシュ> / pushed / n/a
✓ iPhone確認ページ: <ページ名と確認観点>
```

---

## 関連

- [[Vault同期ルール]] — `ob sync` の手順
- [[Claude-Code標準運用]] — 規模・role・検証・連携ルール
- [[../00_index]] — Vault 入口
- `/root/company/CLAUDE.local.md` — Claude Code 側の正本
