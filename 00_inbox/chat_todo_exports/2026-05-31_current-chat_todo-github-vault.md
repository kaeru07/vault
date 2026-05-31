# Chat Todo Export

source_chat:
- title: ChatGPT各チャットの残ToDoをGitHub Vaultへ集約し、CLI側で一括整理する運用
- date: 2026-05-31
- status: 未整理
- source: ChatGPT current conversation

## 残ToDo

### TODO-001: 各チャットから残ToDoをMarkdownで抽出してVaultに保存する運用を作る
category: vault
priority: high
status: pending
reason:
- ChatGPT内のチャットが散らばっており、どのチャットに未完了タスクが残っているか把握しづらい。
- 各チャットで残ToDoを抽出し、GitHub管理Vaultに集約したい。

done_condition:
- 各チャットに貼る「残ToDo抽出プロンプト」が確定している。
- 出力形式が統一されている。
- 保存先ディレクトリが決まっている。
- 少なくとも数チャット分のToDo MarkdownがVaultへ保存されている。

suggested_executor:
- human
- chatgpt

related_files:
- 00_inbox/chat_todo_exports/

notes:
- 各チャットでは抽出だけを行い、統合・重複排除・優先順位付けはCLI側に任せる方針。

---

### TODO-002: CLI側でchat_todo_exportsを一括整理する仕組みを作る
category: vault
priority: high
status: pending
reason:
- 各チャットから個別にToDoを出すだけでは、重複や粒度ばらつきが残る。
- Claude Code / Codex にVault内のMarkdownを横断整理させ、見やすい残ToDo一覧へ変換したい。

done_condition:
- 00_inbox/chat_todo_exports/**/*.md を読み込める。
- 重複ToDoを統合できる。
- Epic単位に整理できる。
- 収益化インパクト順に並べられる。
- 以下の出力が生成される。
  - 00_inbox/chat_todo_exports/_merged/remaining_todos.md
  - 00_inbox/chat_todo_exports/_merged/epic_summary.md
  - 00_inbox/chat_todo_exports/_merged/remaining_todos.json

suggested_executor:
- claude_code
- codex

related_files:
- 00_inbox/chat_todo_exports/
- 00_inbox/chat_todo_exports/_merged/

notes:
- 元ファイルは削除せず、統合結果だけを _merged に出す。
- 推測で完了扱いしない。

---

### TODO-003: 残ToDo抽出用プロンプトをVaultにテンプレート化する
category: vault
priority: medium
status: pending
reason:
- 各チャットに毎回同じ形式で投げられるようにしたい。
- iPhoneからコピペしやすくする必要がある。

done_condition:
- テンプレートMarkdownとして保存されている。
- 入れ子コードフェンスなしで、iPhoneからコピーしやすい形式になっている。
- Epic単位、収益化優先、推測完了禁止、他チャット対応済み疑いの欄が含まれている。

suggested_executor:
- chatgpt
- claude_code

related_files:
- 04-SYSTEM/templates/
- 00_inbox/chat_todo_exports/

notes:
- 今後の各チャット棚卸し標準プロンプトとして使う。

---

### TODO-004: CLI整理用プロンプトをClaude Code/Codex向けに確定する
category: vault
priority: medium
status: pending
reason:
- chat_todo_exports をCLI側で整理するための実行指示が必要。
- Claude Code/Codexに任せる範囲、禁止範囲、出力形式を明確にしたい。

done_condition:
- CLIに貼る整理プロンプトが完成している。
- 対象ディレクトリ、出力先、禁止事項、最終報告形式が明記されている。
- 実行後に読み込んだファイル数、統合前ToDo数、統合後ToDo数、優先Epicトップ5が報告される。

suggested_executor:
- chatgpt
- claude_code
- codex

related_files:
- 00_inbox/chat_todo_exports/

notes:
- まずは手動実行でよい。自動化は後回し。

---

## 完了っぽいが要確認

- GitHub VaultへChatGPTから新規Markdownファイルを書き込めることは、このファイル作成で確認予定。

## 他チャットで対応済みかもしれないもの

- ChatGPTチャット棚卸しプロンプト
- GitHub管理Vaultを正本にする運用
- progressアプリではなくVault経由で管理する方針

## 捨ててよさそうなもの

- 各チャットから直接progressアプリへ登録する運用
- 細かいToDoを大量に作る運用

## CLI側に次に投げるプロンプト案

目的:
Vault内の 00_inbox/chat_todo_exports/ にある各チャット由来のToDo Markdownを一括整理し、残ToDoを見やすく統合してください。

対象:
- 00_inbox/chat_todo_exports/**/*.md

出力先:
- 00_inbox/chat_todo_exports/_merged/remaining_todos.md
- 00_inbox/chat_todo_exports/_merged/epic_summary.md
- 00_inbox/chat_todo_exports/_merged/remaining_todos.json

やること:
1. 各Markdownから残ToDoを抽出
2. 重複ToDoを統合
3. 同じ目的のものをEpic単位にまとめる
4. 収益化インパクト順に並べる
5. Claude/Codexが次に実行できる粒度に整える
6. 完了済みっぽいが未確認のものは別枠にする
7. 不明点は止まらず「要確認」として残す

禁止:
- 元ファイルを削除しない
- 推測で完了扱いしない
- 細かい作業を大量に増やさない
- 既存Vault構成を大きく変更しない

最終報告:
- 読み込んだファイル数
- 統合前ToDo数
- 統合後ToDo数
- 優先Epicトップ5
- 要確認項目
- 次にClaude/Codexへ投げるべき作業
