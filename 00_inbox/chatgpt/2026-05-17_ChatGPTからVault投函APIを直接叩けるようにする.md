---
title: "ChatGPTからVault投函APIを直接叩けるようにする"
type: "todo"
source: "chatgpt-direct-github"
status: "inbox"
created: "2026-05-17"
priority: "high"
target: "progress / Cloudflare Tunnel / vault-inbox API"
---

## 背景
現在、Vaultへの投函は2系統で検証した。

1. ChatGPTからGitHub connector経由で kaeru07/vault に直接Markdownを書き込む方式
2. Claude CodeがVPS内で progress の POST /api/vault-inbox をcurlし、VPS内の obsidian-vault に保存してGitHubへpushする方式

ただし、まだChatGPTから直接 progress の /api/vault-inbox を叩けてはいない。
これができると、ChatGPTに書いた内容を直接VPS側のVault保存フローへ流せるようになり、できることが大きく増える。

## 目的
ChatGPTから progress の POST /api/vault-inbox を直接叩けるようにする。
ただし progress全体は公開せず、Vault投函APIだけを外部から届くようにする。

## やりたいこと
- Cloudflare Tunnelなどを使って /api/vault-inbox だけ外部公開する
- tokenなしアクセスは401になることを確認する
- tokenありアクセスはVault Markdown保存に成功することを確認する
- 保存後に kaeru07/vault へgit pushされることを確認する
- progress全体の他パスは公開しない、または404にする

## 前提
- POST /api/vault-inbox は実装済み
- VAULT_INBOX_TOKEN は設定済み
- VPS内curlではVault保存とgit pushが成功済み
- 保存先Vaultは /root/company/obsidian-vault

## Claude Codeに依頼する内容
1. 現在のprogress起動ポートを確認する
2. /api/vault-inbox が localhost で動くことを再確認する
3. cloudflared が入っているか確認する
4. 未導入なら導入手順を提示する
5. Cloudflare Tunnelで /api/vault-inbox だけ外部公開する構成案を作る
6. progress全体を公開しない設定にする
7. 外部URLから tokenなしは401、tokenありは保存成功になるか確認する
8. 成功したら、ChatGPTから叩くためのURL・ヘッダー・JSON形式を報告する
9. token値そのものは絶対に報告しない

## 禁止
- progress全体を無防備に公開しない
- tokenをログや報告に出さない
- .env.local をGitにcommitしない
- project-tasks.json / work-queue.json / execution-runs.json / app-progress.json は触らない
- obsidian-sync-vault は触らない

## 完了条件
- 外部URLから /api/vault-inbox に到達できる
- tokenなしは401
- tokenありはVault Markdown保存成功
- GitHub kaeru07/vault へpush成功
- progress全体の他パスは外部から見えない、または見えない設計になっている

## 次の一手
このToDoをClaude Codeに読ませ、Cloudflare Tunnel公開の安全な実施案と必要作業を出してもらう。
