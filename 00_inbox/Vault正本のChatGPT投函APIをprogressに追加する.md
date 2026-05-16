目的:
ToDoや構想の蓄積正本はObsidian/Vaultに置き、progressは実行・可視化・queue管理の操作盤として使う。
そのために、progressへ「Vaultへ投函するだけのAPI」を追加する。

対象アプリ:
- /root/company/apps/ny01/progress

保存先Vault:
- /root/company/obsidian-vault

追加するAPI:
- POST /api/vault-inbox

このAPIの役割:
- progressのToDo正本JSONへ直接追加しない
- 受け取った内容をVault Markdownとして保存する
- 保存先は /root/company/obsidian-vault/00_inbox/
- 可能なら保存後に git commit / push する
- progressは将来的にVault ToDoを読む操作盤にする

受け取るJSON:
{
  "title": "タイトル",
  "body": "本文",
  "type": "todo"
}

必須認証:
- 環境変数 VAULT_INBOX_TOKEN を使う
- Authorization: Bearer <token> が一致しない場合は401を返す
- 認証なしでは絶対に保存しない

やってほしいこと:
1. app/api/vault-inbox/route.ts を追加する
2. POSTを受け取る
3. title/body/typeをバリデーションする
4. titleから安全なMarkdownファイル名を作る
5. /root/company/obsidian-vault/00_inbox/ にMarkdown保存する
6. Markdownには frontmatter を付ける
7. 保存後、可能なら obsidian-vault 側で git add / commit / push する
8. git pushに失敗してもMarkdown保存は成功扱いにし、失敗理由をレスポンスに含める
9. curlでローカル動作確認する

Markdown形式:
---
title: タイトル
type: todo
source: chatgpt
status: inbox
created: ISO日時
---

本文

禁止:
- project-tasks.json / work-queue.json / execution-runs.json / app-progress.json は触らない
- progressの既存ToDo管理を壊さない
- obsidian-sync-vault は触らない
- 認証なしAPIにしない
- bodyやtitleをシェルコマンドに混ぜない
- Vault全体の整理やリネームはしない
- 既存ファイルを勝手に上書きしない

完了条件:
- POST /api/vault-inbox が追加されている
- token不一致なら401
- 正常時に 00_inbox/ にMarkdownが作成される
- 同名ファイルがある場合はタイムスタンプ等で衝突回避する
- git commit / push が試行される
- curlで動作確認済み
- progress正本JSONは未変更

テスト例:
curl -X POST http://localhost:3010/api/vault-inbox 
  -H "Content-Type: application/json" 
  -H "Authorization: Bearer $VAULT_INBOX_TOKEN" 
  -d '{"title":"Vault投函APIテスト","body":"ChatGPTからVaultへToDoを投函するテスト","type":"todo"}'

最終報告:
変更ファイル:
追加API:
保存先:
作成されたMarkdown:
curl結果:
git commit/push結果:
progress正本JSONを触っていない確認:
危険ポイント:
次にやるべきこと: