---
date: 2026-05-16
task: POST /api/vault-inbox 追加 + ny01 コード push
runId: 20260516-182442
targetApp: progress
monetizationImpact: low
theme: [workflow, obsidian]
relatedRunIds: [20260516-171158, 20260516-020102, 20260515-221729]
commitHashes: [338e5f1, 656d789]
---

# 2026-05-16 POST /api/vault-inbox 追加 + ny01 コード push

> commitHashes: 338e5f1 = ny01(progress)コード push / 656d789 = obsidian-vault の検証テストMD除去。

---

## 1. 作業目的

- なぜ: 投函 `Vault正本のChatGPT投函APIをprogressに追加するの.md` の指示に従い、ToDo/構想の蓄積正本を Vault に置く投函 API を progress に追加
- 背景: progress は実行・可視化・queue 操作盤、蓄積正本は Obsidian/Vault という役割分担方針
- 期待効果: ChatGPT 等から `POST /api/vault-inbox` で Vault(00_inbox) へ直接投函でき、既存 /inbox 機能が拾える

---

## 2. 実施内容

- `app/api/vault-inbox/route.ts` 新規（POST のみ）
  - Bearer 認証: `process.env.VAULT_INBOX_TOKEN` と `crypto.timingSafeEqual`。未設定→503（絶対保存しない）、欠如/不一致→401
  - 検証: title 必須(<=200) / body 必須(<=20000) / type∈{todo,idea,note,memo}
  - ファイル名: NFC→制御文字(codePoint<=0x1f,0x7f)除去→パス予約文字置換→連続ドット無効化→先頭.-_除去→80字。`YYYYMMDDHHMMSS_slug.md`。`path.resolve` で 00_inbox 配下を dirname 厳密一致で固定（トラバーサル防御）。`wx` で上書き禁止・衝突は連番
  - frontmatter: title/type/source:chatgpt/status:inbox/created
  - git: `execFile`（シェル非経由）で `-C VAULT_ROOT` add/commit/push 試行。失敗は catch して `git.error` に格納、MD 保存は成功扱い
- ユーザー指示で ny01(kaeru07/ny01, PUBLIC) へ **コードのみ** push（data/real/*.json・tsbuildinfo は除外）

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/ny01/progress/app/api/vault-inbox/route.ts | 新規（本タスクの主成果） |
| ny01 push (338e5f1) | 今セッション分の progress コード42件（vault-inbox/inbox/codex/goals/experiments 等）。**データ除外** |
| obsidian-vault (656d789) | 検証で生成したテスト投函MD2件を除去 |

progress 正本JSON（project-tasks/work-queue/execution-runs/app-progress）は**md5 前後一致で無変更**を実証。

---

## 4. 検証結果

- lint: ✔ No ESLint warnings or errors（制御文字 regex は codePoint 方式で回避）
- build: pass（/api/vault-inbox 認識）
- 手動（隔離: 別ポート3036 + 一時 PROGRESS_DATA_PATH + VAULT_INBOX_TOKEN を環境変数で一時付与・.env 非変更）:
  - 認証なし → 401 / token 不一致 → 401
  - 正常系（token 一致）→ ok / 00_inbox に MD 作成 / git committed=true pushed=true
  - title 欠落 → 400
  - パストラバーサル `../../etc/passwd evil` → `..._. etc passwd evil.md` に無害化、00_inbox 配下固定
  - progress 正本4JSON md5 前後一致 = 無変更
- 機密スキャン: data/real・staged コード とも実値ヒットなし。token は process.env 参照のみ
- ob sync / vault push: 本記録で実施

---

## 5. 未対応

- 本番3010 は `VAULT_INBOX_TOKEN` 未設定 + 未再起動のため本番では 503（設計通り fail-safe）。有効化にはトークン設定と再起動（ユーザー判断）
- API 自動 push 経路と既存 sync-vault→mirror→push 運用の二重書き込み整合（運用設計）
- 投函MD を先行実装の `/inbox` 機能が結合で拾えるかの確認

---

## 6. 危険ポイント

- **kaeru07/ny01 は PUBLIC**。ユーザー確認の上「コードのみ push」を選択し data/real(作業ログ正本) は公開回避
- 検証で実際に kaeru07/vault へテスト投函2件が push され、その後除去 commit(656d789) で掃除（履歴には残存）
- API が obsidian-vault main へ自動 git push する。CLAUDE.local.md の「obsidian-vault 直接編集禁止/mirror運用」と二重経路になる点は要運用判断
- pending: テスト用一時 dir は rm -rf ポリシー拒否で残置の可能性（/tmp 配下・実害なし）

---

## 7. 次にやるべきこと

- ユーザー: `VAULT_INBOX_TOKEN` を安全に設定 → 本番3010 再起動して有効化するか
- obsidian-vault 直接 push 経路 と mirror 経路の整合方針決定（二重書き込み回避ルール）
- /api/vault-inbox → /inbox 取り込みの結合動作確認
- レート制限・サイズ上限の運用値見直し

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress (POST /api/vault-inbox)
作業: Vault正本へのChatGPT投函API追加 + ny01コードpush
runId: 20260516-182442
日付: 2026-05-16
GitHub: ny01 commit 338e5f1 (PUBLIC/コードのみ) / vault 656d789(テスト掃除)

## 作業目的
- 投函指示に従い progress に Vault投函API追加。蓄積正本はVault、progressは操作盤

## 実施内容
- app/api/vault-inbox/route.ts: Bearer認証(VAULT_INBOX_TOKEN/timingSafe/未設定503)、title/body/type検証、トラバーサル防御、obsidian-vault/00_inboxへfrontmatter付MD保存(wx+連番)、execFileでgit add/commit/push試行(失敗してもMD成功)
- ny01(PUBLIC)へコードのみpush(data/real除外)

## 検証結果
- lint ✔ / build pass
- 隔離: 認証なし401/不一致401/正常ok+MD+gitpush/title欠落400/トラバーサル無害化
- 正本4JSON md5前後一致=無変更
- 検証テストMD2件はvaultからgit rm+push(656d789)

## 禁止事項遵守
- project-tasks/work-queue/execution-runs/app-progress.json非変更(md5実証)
- 既存コード非変更(新規route.tsのみ)/obsidian-sync-vault非変更/.env非変更
- 認証なし保存不可/title,bodyをシェルに混ぜない(execFile)/上書きしない(wx)

## 危険ポイント
- ny01 PUBLIC→コードのみpush(データ公開回避)
- API自動pushとmirror運用の二重経路
- 本番はtoken未設定で503(fail-safe)

## 確認したい観点
- 認証(timingSafe/未設定503)の妥当性
- ファイル名サニタイズ/トラバーサル防御の網羅性
- execFileによるコマンドインジェクション対策の妥当性
- obsidian-vault直接push経路とmirror運用の二重化リスクと整合方針
- PUBLICリポへのコードのみpost判断
````

---

## 関連

- progress runId: 20260516-182442（正本）
- 関連 run: 20260516-171158(/inbox) / 20260516-020102(codex-sdk) / 20260515-221729(codex連携)
- 投函元: 00_inbox/Vault正本のChatGPT投函APIをprogressに追加するの.md
- 関連アプリ: [[../02_apps/progress]]
