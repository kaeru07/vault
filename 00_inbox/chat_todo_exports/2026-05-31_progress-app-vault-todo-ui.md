# Chat Todo Export

source_chat:
- title: Vaultを原本にしてprogressアプリへ残ToDo整理専用ページを追加する要望
- date: 2026-05-31
- status: 未整理
- source: ChatGPT current conversation

## 方針

- Vaultを原本とする。
- progressアプリは閲覧・整理・操作しやすくするためのUIとして扱う。
- progressアプリ側で正本を持たせすぎない。
- CLI側でVault内のToDo Markdownを整理し、その結果をprogressアプリの専用ページで見やすく表示する。

## 残ToDo

### TODO-001: progressアプリにVault由来の残ToDo専用ページを追加する
category: app
priority: high
status: pending
reason:
- Vaultに残ToDoを集約しても、Markdownだけでは全体像が見づらくなる可能性がある。
- ユーザーがiPhoneやブラウザから見やすく確認できる専用UIが必要。
- 原本はVaultに残しつつ、progressアプリで見やすく整理表示したい。

done_condition:
- progressアプリに残ToDo専用ページが追加されている。
- Vault由来の統合ToDoを読み込んで表示できる。
- Epic単位、優先度、状態、収益化インパクト、次アクションで見やすく整理されている。
- スマホ表示でも見やすい。
- Vault原本へのリンクまたは参照パスが表示される。

suggested_executor:
- claude_code
- codex

related_files:
- 00_inbox/chat_todo_exports/_merged/remaining_todos.md
- 00_inbox/chat_todo_exports/_merged/remaining_todos.json
- 00_inbox/chat_todo_exports/_merged/epic_summary.md
- progress app

notes:
- progressアプリは正本ではなくビューア兼整理UI。
- VaultのMarkdown/JSONを原本として扱う。

---

### TODO-002: CLI整理処理の出力にprogressアプリ用JSONを追加する
category: vault
priority: high
status: pending
reason:
- progressアプリで見やすく表示するには、Markdownだけでなく構造化JSONが必要。
- remaining_todos.json をprogress専用ページが読み込める形式に整える必要がある。

done_condition:
- CLI整理後に progressアプリ用JSON が生成される。
- 各ToDoに以下の項目が含まれる。
  - id
  - title
  - epic
  - category
  - priority
  - status
  - monetization_impact
  - source_chat
  - source_file
  - done_condition
  - suggested_executor
  - next_action
  - updated_at
- JSONの形式がprogressアプリ側で扱いやすい。

suggested_executor:
- claude_code
- codex

related_files:
- 00_inbox/chat_todo_exports/_merged/remaining_todos.json

notes:
- progressアプリ側にDB登録するか、JSON読み込みにするかは既存構成を確認して決める。
- 最初はJSON読み込みでもよい。

---

### TODO-003: progressアプリ専用ページの表示要件を決める
category: app
priority: medium
status: pending
reason:
- 単なる一覧ではなく、ユーザーが次に何をすべきか一目で分かるUIが必要。

done_condition:
- 専用ページに以下の表示がある。
  - 現在地サマリー
  - 優先Epicトップ5
  - 今週やるべきToDo
  - 収益化インパクトが高いToDo
  - 停止中/要確認ToDo
  - 完了疑い/未検証ToDo
  - Vault原本リンクまたはファイルパス
- フィルターまたはタブで category / priority / status を切り替えられる。
- iPhoneで縦スクロールしやすい。

suggested_executor:
- claude_code
- codex

related_files:
- progress app

notes:
- UIはカード型を優先。
- 細かい表よりも、優先度順のカードとEpicまとまりを重視する。

---

## CLI側に次に投げるプロンプト案

目的:
Vaultを原本として、00_inbox/chat_todo_exports/ に蓄積された各チャット由来の残ToDoを一括整理し、その結果をprogressアプリで見やすく確認できる専用ページとして実装してください。

重要方針:
- Vaultを正本・原本とする。
- progressアプリは閲覧・整理・判断補助UIとして扱う。
- progressアプリ側だけにしか存在しないToDoを作らない。
- Vault原本との対応関係が分かるようにする。

対象:
- Vault: 00_inbox/chat_todo_exports/**/*.md
- Vault出力先:
  - 00_inbox/chat_todo_exports/_merged/remaining_todos.md
  - 00_inbox/chat_todo_exports/_merged/epic_summary.md
  - 00_inbox/chat_todo_exports/_merged/remaining_todos.json
- progressアプリ: 既存構成を調査して、残ToDo専用ページを追加する

やること:
1. Vault内の chat_todo_exports を読み込む
2. 残ToDoを抽出する
3. 重複を統合する
4. Epic単位にまとめる
5. 収益化インパクト順に並べる
6. remaining_todos.md / epic_summary.md / remaining_todos.json を生成する
7. progressアプリに「Vault残ToDo」専用ページを追加する
8. 専用ページで remaining_todos.json を読み込み、ユーザーが見やすいUIで表示する

progressアプリ専用ページの要件:
- ページ名は仮で「Vault残ToDo」または「残ToDo整理」
- 現在地サマリーを上部に表示
- 優先Epicトップ5を表示
- 収益化インパクト high のToDoを目立たせる
- status別に pending / in_progress / blocked / needs_review / done_candidate を分ける
- category / priority / status のフィルターを用意
- 各ToDoカードに source_file を表示し、Vault原本を追跡できるようにする
- iPhoneで見やすい縦長カードUIにする

禁止:
- Vault原本を勝手に削除しない
- 推測で完了扱いしない
- progressアプリ側だけでToDoを正本化しない
- 細かいToDoを大量に増やさない
- 既存progressアプリの主要機能を壊さない

検証:
- build成功
- lintまたはtypecheck確認
- 専用ページが表示できる
- JSONが空/壊れている場合も画面が落ちない
- スマホ幅で表示崩れがない

最終報告:
- 読み込んだVaultファイル数
- 統合前ToDo数
- 統合後ToDo数
- 生成したVaultファイル
- 追加/変更したprogressアプリのファイル
- 画面URLまたはローカル確認URL
- 未対応点
- 次にやるべきこと

## 完了っぽいが要確認

- まだ実装は未実施。
- このファイルは方針とCLI実装プロンプトの保存用。

## 他チャットで対応済みかもしれないもの

- progressアプリへのToDo取り込み機能
- Goal Planner / WorkQueue / project-tasks.json 連携
- Vaultを正本にする方針

## 捨ててよさそうなもの

- progressアプリ単体を正本にする運用
- ChatGPTから直接progressへ登録する運用
