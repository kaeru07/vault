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
- [ ] `git log --oneline -5` で push 対象 commit が想定通り
- [ ] `git remote -v` で push 先 remote が想定通り

push 後は `git status`（clean か）/ `git log --oneline -3`（local と remote の HEAD が揃ったか）を必ず確認する。

---

## commit 後の push 必須化（2026-05-14 追加）

**ルール**: ファイル変更を伴う作業で commit を作成した場合、原則として同じ作業内で `git push origin main` まで実行する。**push 未実施の状態は完了扱いにしない**。

**push 完了 = 同期完了**: Obsidian Vault / progress レビュー / GitHub mirror / ChatGPT レビューは GitHub push を前提に連携している。push されていない状態は ChatGPT 側からは「存在しない」のと同じ。

**push を停止する条件**:
- `.env` / `secrets` / `credential` / `token` / `private key` / API キー実値 / SSH 鍵本文（`-----BEGIN`）/ 本番 DB 接続文字列が含まれる
- ユーザーが明示的に「push するな」「GitHub 反映不要」と指示
- merge コンフリクトが発生し自動解決すべきでない
- main への force push が必要（原則禁止）

上記以外の通常修正では **push まで自動実施**する。

**push できなかった場合の最終報告必須項目**:
- push 失敗理由（permission denied / 認証 / ネットワーク / コンフリクト / null bytes 等）
- 未 push commit の hash（複数あれば全件）
- ユーザー手動実行コマンド（`cd <repo> && git push origin <branch>`）
- 再現条件

**Why**: 2026-05-14 にグローバル deny で `git push` が阻止され、commit 済みだが GitHub に反映されない事故が発生。push 完了を完了条件に含めることで「commit したのに ChatGPT 側に届いていない」状態を構造的に防ぐ。

詳細は `/root/company/CLAUDE.local.md` § commit 後の push 必須化 を参照。

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
