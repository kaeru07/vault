# Vaultの見方：どこを見れば何がわかるか

このファイルは、GitHub管理Vaultを初めて見る人向けの入口です。

目的は、ファイル名やフォルダ名を見ても迷わず、今どこを確認すればよいか分かるようにすることです。

---

## まず見る順番

### 1. 今、自分が判断する候補を見る

場所:

`20_reviews/ChatGPT承認待ち.md`

分かること:

- ChatGPT上で approve / hold / reject すべき候補
- 今、人間が判断するべきもの
- 承認後に何が起きるか

注意:

- 2026-05時点でこのファイルは存在確認が必要
- 見つからない場合は Issue #54 の調査対象
- Issueコメントだけで「存在する」と扱わない

---

### 2. 有力候補の詳細を見る

場所:

`05_monetization/scenarios/`

分かること:

- candidateの詳細
- 誰向けの案か
- 収益化手段
- リスク
- 進める理由
- 保留・却下理由

例:

`candidate-001`

意味:

- 承認前の有力候補
- 現在の最有力は「何切る特化AI解説Web」

---

### 3. 自動生成された案の一覧を見る

場所:

`05_monetization/idea_pool/`

分かること:

- 情報収集から作られた案
- 元になったトレンド
- 粗いスコア
- どの情報源から来たか

使い方:

- 全部を読む場所ではない
- 上位抽出や比較の元データとして使う
- candidateに上がる前の案倉庫

---

### 4. 元ネタ・市場調査を見る

場所:

`06_research/`

分かること:

- 案の根拠になった情報
- 市場性の確認結果
- 競合・需要・トレンド
- なぜcandidate化/holdになったか

特に見る場所:

`06_research/daily/`

- 毎日の収集データ
- HN / Reddit / iTunesなどから集めた情報

`06_research/logs/`

- 情報収集が成功したか
- どの情報源が失敗したか
- 実行件数やエラー

`06_research/2026-05-22_上位5案追加調査.md`

- 上位5案の比較
- 収益化スコアリング
- candidate判断の根拠

---

### 5. 全体進捗を見る

場所:

`05_monetization/epics.md`

分かること:

- Epic A: 情報収集基盤
- Epic B: 案生成・candidate判断
- Epic C: 承認判断
- どこまで進んだか
- 次に何が残っているか

使い方:

- 全体像を確認する場所
- 細かい作業ではなく、親テーマ単位で見る

---

### 6. Claude/vloopの動き方を見る

場所:

`03_prompts/claude-commands/vloop.md`

分かること:

- Claude Codeにどう作業させるか
- 途中で止めないルール
- Epic完了優先ルール
- 小Issue追加で止めないルール

見るタイミング:

- Claudeがまた1件ずつしか進めないとき
- 確認が多すぎるとき
- vloopの動きがブレたとき

---

### 7. Claude Code全体の標準ルールを見る

場所:

`03_prompts/Claude-Code標準運用.md`

分かること:

- Claude Code全般の作業ルール
- 検証ルール
- progress連携
- Obsidian追記
- GitHub反映
- レビュー形式

見るタイミング:

- 運用全体がおかしいと感じたとき
- Claudeへの標準指示を確認したいとき

---

## 目的別の見る場所

### 今、何を判断すればいい？

見る場所:

1. `20_reviews/ChatGPT承認待ち.md`
2. `05_monetization/scenarios/`
3. `06_research/2026-05-22_上位5案追加調査.md`

---

### なぜその案が有力なの？

見る場所:

1. `06_research/2026-05-22_上位5案追加調査.md`
2. `05_monetization/idea_pool/`
3. `06_research/daily/`

---

### 案はどこにある？

見る場所:

`05_monetization/idea_pool/`

意味:

- 自動生成された案の倉庫
- candidateになる前の原石

---

### 有力候補はどこ？

見る場所:

`05_monetization/scenarios/`

意味:

- candidateとして整理された案
- 承認前・保留中・却下候補など

---

### 元ネタはどこ？

見る場所:

`06_research/daily/`

意味:

- 情報収集で集めた原材料
- HN / Reddit / iTunesなど

---

### 自動化が動いたか知りたい

見る場所:

`06_research/logs/`

意味:

- research-run = 情報収集処理
- idea-run = 案生成処理
- 成功/失敗ログ

---

### 全体でどこまで進んだ？

見る場所:

`05_monetization/epics.md`

意味:

- Epic単位の進捗
- 次の山
- 残作業

---

## よく出る用語

### candidate

意味:

承認前の有力候補。

まだ実装決定ではない。
ChatGPTまたは人間が approve / hold / reject する。

---

### approve

意味:

進める判断。

承認後に、実装ToDoやprogress投入へ進む。

---

### hold

意味:

保留。

今は進めないが、調査や条件が揃えば再検討する。

---

### reject

意味:

却下。

現時点では進めない。

---

### idea_pool

意味:

自動生成された案の倉庫。

全部を読むのではなく、上位抽出や比較に使う。

---

### research-run

意味:

情報収集を行う処理。

HN / Reddit / iTunesなどから元ネタを集める。

---

### idea-run

意味:

収集した情報から案を作る処理。

idea_poolに保存する。

---

### vloop

意味:

Claude CodeがVaultのToDoを読み、Epic単位でまとめて進めるコマンド。

小Issueを1件ずつ進めて止まるのではなく、Epic完了を優先する。

---

## 今のおすすめ確認順

2026-05時点では、以下の順に見る。

1. `05_monetization/epics.md`
2. `05_monetization/scenarios/`
3. `06_research/2026-05-22_上位5案追加調査.md`
4. `05_monetization/idea_pool/`
5. `06_research/logs/`

ただし、`20_reviews/ChatGPT承認待ち.md` が作成・整備された後は、最初にそこを見る。

---

## 注意点

- Issueコメントに書かれているだけでは、ファイルが存在するとは限らない
- 実在確認していないファイルを正規入口として扱わない
- GitHub管理Vaultを正本とする
- obsidian-sync-vaultだけにある内容は、GitHubへ反映されていない可能性がある
- 承認判断はChatGPT/ユーザー側で行う
- Claude/vloopは判断材料を作るが、勝手にapprovedへ変更しない
