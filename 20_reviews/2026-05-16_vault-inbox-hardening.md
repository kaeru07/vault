---
date: 2026-05-16
task: vault-inbox API 強化 (環境変数化 / push branch可変 / 投函ログ)
runId: 20260516-190343
targetApp: progress
monetizationImpact: low
theme: [workflow, obsidian]
relatedRunIds: [20260516-182442]
commitHashes: [7ab08fe]
repo: https://github.com/kaeru07/ny01
---

# 2026-05-16 vault-inbox API 強化

> 外部公開・長期運用に備えた安全性/保守性/ログ性の強化。新規 API ではなく既存 route.ts の改修。

---

## 1. 作業目的

- なぜ: `POST /api/vault-inbox` を外部公開・長期運用に耐える形へ
- 背景: ハードコードの VAULT_ROOT / main 固定 / ログ無し が運用上の弱点
- 期待効果: 環境変数で配置とブランチを切替可能、投函の成否を追跡可能

---

## 2. 実施内容

- `VAULT_ROOT` 環境変数化（未設定時 `/root/company/obsidian-vault`）— 関数 `vaultRoot()` で都度解決
- `VAULT_GIT_BRANCH` 環境変数化（未設定時 `main`）— `git push origin <branch>` の main 固定を撤廃
- 投函ログ `vault-inbox-log.ndjson` を `getDataPath()`（PROGRESS_DATA_PATH 優先）に追記
  - 記録項目: ts / result / httpStatus / file(slug化済) / type / titleLen / bodyLen / branch / gitCommitted / gitPushed / gitFailed
  - **title 全文・body 本文・token は記録しない**。git stderr 全文も残さない（gitFailed:boolean のみ）
- ログ失敗は API 本体を壊さない（try/catch で握りつぶし・安全側）
- レスポンスは従来通り `{ok,file,path,relPath,created,git}`。token・秘密は出力しない（ssh push のため git.error に token は含まれない）

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/ny01/progress/app/api/vault-inbox/route.ts | VAULT_ROOT/VAULT_GIT_BRANCH 環境変数化、push branch 可変、appendInboxLog 追加、各 return にログ |

ny01 push: commit `7ab08fe`（**コードのみ**・data/real 除外・PUBLIC リポ）。

---

## 4. 検証結果（curl確認結果）

- lint: ✔ No ESLint warnings or errors / build: pass（/api/vault-inbox 認識）
- 隔離環境（別ポート3037 + 一時 PROGRESS_DATA_PATH + 一時 VAULT_ROOT=非gitdir + VAULT_INBOX_TOKEN 環境変数 + VAULT_GIT_BRANCH=release）:
  1. token なし → **401**
  2. token 不一致 → **401**
  3. token 一致 → **Markdown 保存 ok**（VAULT_ROOT が非 git のため git は `fatal: not a git repository` で失敗するが **ok:true ＝ git 失敗時も保存成功扱い**）
  4. 保存先＝一時 VAULT_ROOT/00_inbox、**本物 obsidian-vault は非汚染**（直近に新規混入なし）
  5. ログ ndjson: auth_fail 2件(401) + saved 1件。`branch:"release"` ＝ **VAULT_GIT_BRANCH 反映（main 固定撤廃を実証）**。token なし・title 全文なし（titleLen のみ）・body 本文なし（bodyLen のみ）
- レスポンスに token・秘密情報なし（git.error は `not a git repository` のみ、ssh のため token 非含有）
- 正本4JSON（project-tasks/work-queue/execution-runs/app-progress）**md5 前後完全一致＝無変更**

## 作成/更新されたログ

- `<PROGRESS_DATA_PATH>/vault-inbox-log.ndjson`（検証では一時ディレクトリ。本番は data/real）
- 例: `{"ts":"...","result":"saved","httpStatus":200,"file":"<ts>_<slug>.md","type":"todo","titleLen":8,"bodyLen":28,"branch":"release","gitCommitted":false,"gitPushed":false,"gitFailed":true}`

## git commit/push 結果

- ny01: commit `7ab08fe` → push 成功（kaeru07/ny01 main、コードのみ）
- 検証は一時 VAULT_ROOT を使い**本物 vault / kaeru07/vault へは一切 push していない**（前回の反省を反映）

## progress正本JSON未変更の確認

`md5sum` を検証前後で取得し project-tasks/work-queue/execution-runs/app-progress.json の全ハッシュ一致を確認。

---

## 5. 未対応

- 本番3010 は VAULT_INBOX_TOKEN 未設定＋未再起動のため本番では 503（fail-safe）
- ログのローテーション（無制限追記）
- 既存 /inbox 取り込みとの結合確認

---

## 6. 危険ポイント

- API が `VAULT_ROOT` の git リポへ自動 push（既定 obsidian-vault main）。既存 sync-vault→mirror 運用と二重経路の整合は未決
- kaeru07/ny01 は PUBLIC（コードのみ push でデータ非公開を維持）
- ログは無制限追記（長期運用でローテーション要検討）
- 検証一時ディレクトリが rm -rf ポリシー拒否で残置の可能性（/tmp 配下・実害なし）

---

## 7. 次にやるべきこと

- VAULT_INBOX_TOKEN 設定 + 本番3010 再起動で有効化（ユーザー判断）
- vault-inbox-log.ndjson のローテーション/上限
- obsidian-vault 直接 push と mirror 運用の整合方針決定
- /api/vault-inbox → /inbox 結合動作確認

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress (POST /api/vault-inbox 強化)
作業: 環境変数化 / push branch可変 / 投函ログ
runId: 20260516-190343
日付: 2026-05-16
GitHub: ny01 commit 7ab08fe (PUBLIC/コードのみ)

## 作業目的
- vault-inbox を外部公開・長期運用向けに安全性/保守性/ログ性強化

## 実施内容
- VAULT_ROOT / VAULT_GIT_BRANCH 環境変数化、push main固定撤廃
- vault-inbox-log.ndjson 簡易ログ(title全文/body/token非記録)
- レスポンス/ログに秘密を出さない

## 検証結果
- lint ✔ / build pass
- 隔離(一時VAULT_ROOT=非gitdir): tokenなし401/不一致401/一致でMD保存ok(git失敗時も成功扱い)
- branch=release がログ反映=main固定撤廃を実証
- 本物vault非汚染 / 正本4JSON md5前後一致=無変更
- レスポンスに秘密なし(ssh pushでtoken非含有)

## 確認したい観点
- 環境変数を関数で都度解決する設計の妥当性
- ログ項目が秘密漏洩しないか(titleLen/bodyLen/gitFailedのみ)
- git失敗時も保存成功扱いの挙動
- 自動push経路とmirror運用の二重化整合方針
- ログ無制限追記のローテーション要否
````

---

## 関連

- 前段: [[2026-05-16_vault-inbox-api]]（API 新規追加）
- progress runId: 本記録の POST で採番
- 関連アプリ: [[../02_apps/progress]]
