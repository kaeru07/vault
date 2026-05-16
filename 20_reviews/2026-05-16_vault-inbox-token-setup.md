---
date: 2026-05-16
task: VAULT_INBOX_TOKEN 本番設定 + 再起動 + 疎通確認
runId: 20260516-193605
targetApp: progress
monetizationImpact: low
theme: [workflow, obsidian]
relatedRunIds: [20260516-190343, 20260516-182442]
commitHashes: [296acb2]
---

# 2026-05-16 VAULT_INBOX_TOKEN 本番設定 + 疎通確認

> token 値はこのレビューにも progress にも一切記録しない（.env.local にのみ存在・git ignore 済）。

---

## 1. 作業目的

- なぜ: `POST /api/vault-inbox` を本番で認証付き稼働させる
- 背景: 実装・強化は完了済だが本番3010 は token 未設定で 503（fail-safe）だった
- 期待効果: 本番で ChatGPT 等から認証付き投函が可能になる

---

## 2. 実施内容

1. `.env.local` 確認（PROGRESS_DATA_PATH のみ・VAULT_INBOX_TOKEN なし）
2. `openssl rand -hex 32` を**変数経由**で生成（echo せず）、末尾改行を保証して `.env.local` に1行追記。重複なし・値長 64hex
3. `git check-ignore .env.local` で除外確認済（git 非追跡）
4. `pm2 list` → `progress`(id 2) 確認 → `pm2 restart progress`（pid 351235 / ↺1 / online）。port 3010 応答 200
5. token なし POST → **401**
6. token あり（`.env.local` から `cut -d= -f2-` で変数読込）POST → **ok:true 保存**
7. 投函ログ確認（token・title全文・body本文なし）

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/ny01/progress/.env.local | VAULT_INBOX_TOKEN 追記（**値非記録**・git ignore 済・1行のみ） |
| obsidian-vault/00_inbox/20260516103250_Vault投函API token設定確認.md | 疎通確認の投函（API 経由・commit `296acb2` で push 済） |

---

## 4. 検証結果（最終報告）

- **設定対象**: `/root/company/apps/ny01/progress/.env.local`
- **.env.local更新有無**: 更新あり（VAULT_INBOX_TOKEN 新規1行）
- **token表示なし確認**: チャット/git/ログ/レビュー いずれにも値なし。確認は「行数1・値長64hex」のみ
- **progress再起動結果**: `pm2 restart progress` 成功（pid 351235 online、port 3010 → 200）
- **tokenなしcurl結果**: `401` `{"error":"Authorization: Bearer <token> が必要です"}`
- **tokenありcurl結果**: `{"ok":true,"git":{"committed":true,"pushed":true}}`
- **保存されたMarkdown**: `/root/company/obsidian-vault/00_inbox/20260516103250_Vault投函API token設定確認.md`（192 bytes）
- **git commit/push結果**: API が自動 commit `296acb2` → kaeru07/vault へ push 済（`main...origin/main` 同期・未push なし）
- **progress正本JSON未変更の確認**: project-tasks/work-queue/execution-runs/app-progress.json の **md5 を再起動前後で取得し完全一致**
- 投函ログ `data/real/vault-inbox-log.ndjson`: `auth_fail`(401) + `saved`(200, token無/titleLen20/bodyLen36/branch main/gitPushed true)

---

## 5. 未対応

- `vault-inbox-log.ndjson` のローテーション（無制限追記）
- API 自動 push 経路と既存 sync-vault→mirror 運用の二重化整合
- /api/vault-inbox → 先行 /inbox 取り込みの結合確認
- 再起動で本番反映された codex/inbox 機能の本番動作確認

---

## 6. 危険ポイント

- **再起動により vault-inbox/inbox/codex 等これまで未反映だった機能が本番3010に一括反映**された（意図通りだが本番挙動の観察が必要）
- 確認投函MD 1件が kaeru07/vault に push 済（正規の疎通確認・残置）
- token は `.env.local` にのみ存在。漏洩経路（バックアップ・ログ）に注意
- API 自動 push と mirror 運用の二重経路（継続課題）

---

## 7. 次にやるべきこと

- ログローテーション実装
- obsidian-vault 直接 push と mirror 運用の整合方針決定
- /api/vault-inbox → /inbox 結合動作確認
- 本番反映された codex/inbox 機能の動作観察

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress (VAULT_INBOX_TOKEN 本番設定)
作業: token設定 + pm2 restart + 疎通確認
runId: 20260516-193605
日付: 2026-05-16
GitHub: API自動 commit 296acb2 (kaeru07/vault)

## 作業目的
- POST /api/vault-inbox を本番で認証付き稼働させる

## 実施内容
- openssl rand -hex 32 を変数経由で .env.local に追記(値非表示・git ignore済)
- pm2 restart progress
- tokenなし401 / tokenあり保存成功 を確認

## 検証結果
- 再起動 pid351235 online port3010=200
- tokenなしPOST→401 / tokenありPOST→ok:true 保存(296acb2 push)
- 保存先 /root/company/obsidian-vault/00_inbox/20260516103250_*.md
- 投函ログ token無/titleLen/bodyLenのみ
- 正本4JSON md5前後完全一致=無変更

## 確認したい観点
- token を .env.local のみで保持する運用の妥当性
- 再起動で複数未反映機能が一括本番化した点のリスク
- 確認投函MDの残置可否
- API自動pushとmirror運用の二重経路整合
````

---

## 関連

- 前段: [[2026-05-16_vault-inbox-hardening]] / [[2026-05-16_vault-inbox-api]]
- progress runId: 20260516-193605
- 関連アプリ: [[../02_apps/progress]]
