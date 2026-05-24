# 案件別ToDo運用ルール

#運用ルール #ToDo #Vault #Obsidian #vloop

> [!important]
> 今後のToDoは、GitHub Issueだけで管理しない。
> ユーザーが見る正本はVault内の「案件ごとのToDo一覧」とする。

---

## 目的

会話で出た作業・改善・案・承認待ちが、Issueやチャットに散らばって見落とされるのを防ぐ。

今後は、ToDoを必ず案件ごとにVaultへ整理する。

---

## 基本方針

```text
会話で出た作業
↓
案件を判定
↓
Vaultの案件ページへToDo追記
↓
必要ならIssueを内部作業用に作成
↓
vloop開始時はVaultの案件別ToDoを読む
↓
終了時にVaultへ進捗更新
```

Issueは内部作業用。Vaultがユーザー向け正本。

---

## ToDoの置き場所

| 案件 | 主な保存先 |
|---|---|
| Vault運用 | `20_reviews/案件別ToDo一覧.md` / `20_reviews/次に実体化するToDo.md` |
| 麻雀アプリ | `02_apps/mahjong.md` |
| トークン速度ツール | `05_monetization/案の情報源と採用理由.md` / 専用ページ作成後はそちら |
| 複数案試作 | `05_monetization/試作ループ検証.md` |
| 収益化案 | `05_monetization/案の情報源と採用理由.md` |
| レビュー・承認 | `20_reviews/ChatGPT承認待ち.md` / `20_reviews/レビュー記録.md` |

---

## ToDoの状態

| 表示名 | 内部名 | 意味 |
|---|---|---|
| 未対応 | open | まだ作業していない |
| ToDo化のみ | planned_only | 予定はあるが実体ファイルや成果物はない |
| 作業中 | in_progress | 途中 |
| 成果物あり | artifact_exists | ファイルや試作物はあるが完了確認前 |
| あなたの確認待ち | user_check | ユーザー確認が必要 |
| 完了 | done | 成果物・確認・反映まで済み |
| 統合済み | merged | 別ToDoに統合済み |
| 停止中 | blocked | 何かが詰まっている |

---

## 案件別ToDoに必ず書く項目

各ToDoには最低限これを書く。

- 案件名
- ToDo名
- 状態
- 何をするか
- なぜ必要か
- どこを見れば分かるか
- 関連ファイル
- 関連Issue（ある場合のみ）
- 次にvloopがやること
- ユーザー確認が必要か

---

## vloop開始時ルール

vloopはIssue一覧からではなく、まずVaultを見る。

読む順番:

1. `00_START_HERE.md`
2. `20_reviews/案件別ToDo一覧.md`
3. `20_reviews/次に実体化するToDo.md`
4. `20_reviews/ChatGPT承認待ち.md`
5. 関連する案件ページ（例: `02_apps/mahjong.md`）

---

## vloop終了時ルール

終了時はVaultへ必ず更新する。

書くこと:

- 今回処理したToDo
- 状態変更
- できた成果物
- まだ残っているToDo
- 次に続き対応するToDo
- 新しく発生したToDo
- IssueだけでVault未反映のものがないか

---

## 禁止事項

- Issueだけ作って終わらせない
- チャットだけで覚えた扱いにしない
- planned_onlyをdone扱いしない
- 案件ページにないToDoをvloopで無視しない
- ユーザーが見ていないIssueを正本扱いしない
- 中途半端なToDoを次サイクルに載せず放置しない
