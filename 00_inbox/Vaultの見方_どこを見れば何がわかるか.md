# Vaultの見方：どこを見れば何がわかるか

> **収益化フロー（candidate / idea_pool / research / epics / logs / vloop）の判断のための見方ガイド**。
> Vault 全体の構造索引は [[../00_index]]（フォルダ別・テンプレ・運用 QR）。本ファイルは収益化自動化フローに沿った「判断するための見方」に特化。
> Issue #55 で本ファイルを**収益化フローの正規見方ガイド**として確定（配置は `00_inbox/` のまま維持）。

このファイルは、GitHub管理Vaultを初めて見る人向けの入口です。

目的は、ファイル名やフォルダ名を見ても迷わず、今どこを確認すればよいか分かるようにすることです。

---

## ⚡ 1 分で見る（vloop11 / 2026-05-25 追加）

> [!important] 急いでいる人向けの短い索引（詳細は本ページ後半を参照）
> 用途別に「ここを開く」だけを 1 行で示す。各リンクから 1-2 タップで目的のページへ。

| やりたいこと | 1 タップで開く |
|---|---|
| 今、自分が判断するものは？ | [[../20_reviews/ChatGPT承認待ち]] |
| 全 ToDo の正本を見る | [[../20_reviews/案件別ToDo一覧]] |
| vloop が次に実行する対象 | [[../20_reviews/vloop_queue]] |
| 完了 ToDo の履歴 | [[../20_reviews/完了ToDoログ]] |
| 案件ごとのゴール / 詰まり | [[../20_reviews/案件別ゴール進捗]] |
| 全候補（candidate）一覧 | [[../05_monetization/scenarios/README]] |
| Epic A/B/C/D の進捗 | [[../05_monetization/epics]] |
| iPhone 入口 | [[../00_START_HERE]] |

---

## 🎯 最新の候補状況（2026-05-25 時点 / vloop10 末）

| 候補 | 状態 | 判断材料 | 状況 |
|---|---|---|---|
| candidate-001（麻雀何切る公開）| pending_approval | [[../20_reviews/candidate-001_ChatGPT承認パック]] §1-§14 | ChatGPT 方向性レビュー待ち |
| candidate-005（トークン速度ベンチ）| candidate | [[../20_reviews/candidate-005_ChatGPT承認パック]] §1-§14 | ChatGPT 方向性レビュー待ち |
| candidate-006（LLM Chooser）| candidate | [[../20_reviews/candidate-006_ChatGPT承認パック]] §1-§14 + [[../06_research/2026-05-25_llm-chooser-判定基準客観化方針]] | ChatGPT 方向性レビュー待ち（N-03 客観化方針 3 層構造）|
| candidate-007（Vault Search Cheatsheet）| candidate | [[../20_reviews/candidate-007_ChatGPT承認パック]] §1-§15 + [[../06_research/2026-05-25_vault-search-cheatsheet-統合方針比較]] | ChatGPT 方向性レビュー待ち（N-04 統合方針 A/B/C / Claude 推奨案 B）|

> 用語注: candidate = 有力候補 / pending_approval = 承認待ち / 判断材料 = 承認パック §1-§14

---

## 📋 分割方針（次サイクル候補・破壊的削除なし）

> [!note] 本ガイドは現在 375 行で長すぎる（Issue #59 本文指摘）。
> ただし破壊的削除は次サイクル以降のユーザー判断とし、本サイクルでは**冒頭に短い索引を追加**するに留める。

分割案（実施は ChatGPT 方向性レビュー / ユーザー判断後）:
- **案 A**: 本ガイドはそのまま、`candidate-007（Vault Search Cheatsheet）` 公開後にリンクで誘導
- **案 B（推奨 / N-04 統合方針 B と連動）**: 本ガイドを「索引のみ」に短縮し、検索パターン詳細は candidate-007 HTML 版へ移管
- **案 C**: 本ガイドは個人運用ノウハウ専用、candidate-007 は公開向け検索チートシート専用で完全分離

詳細比較: [[../06_research/2026-05-25_vault-search-cheatsheet-統合方針比較]]

## パスの実在区分（Issue #54 / #55）

- ✅ 実在 = GitHub 管理 Vault に実ファイルあり・push 済み
- ⏳ 設計のみ = 設計ドキュメントは実在するが、コマンド/自動処理の実体は未実装（research-run / idea-run / cron が該当）

---

## まず見る順番

### 1. 今、自分が判断する候補を見る

場所: `20_reviews/ChatGPT承認待ち.md` ✅ 実在

分かること:

- ChatGPT上で approve / hold / reject すべき候補
- 今、人間が判断するべきもの
- 承認後に何が起きるか

注意（Issue #54 調査済み）:

- 本ファイルは**実在し origin/main に push 済み**（commit a06ab2e で作成・以降更新）
- ファイル名が日本語のため GitHub raw URL では `%` エンコードされる。**パスを直接構築せず `20_reviews/` ディレクトリ一覧から開く**
- candidate-001 の詳細判断材料は `20_reviews/candidate-001_ChatGPT承認パック.md`（§1-§14 完備）✅ 実在
- 詳細は `20_reviews/README.md` §ChatGPT 承認入口

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

### research-run ⏳ 設計のみ（未実装）

意味:

情報収集を行う処理。HN / Reddit / iTunesなどから元ネタを集める。

**コマンド本体は未実装**。現在は Claude が手動で同等処理を実行（実装は人間承認後・別アプリリポジトリ）。

---

### idea-run ⏳ 設計のみ（未実装）

意味:

収集した情報から案を作る処理。idea_poolに保存する。

**コマンド本体は未実装**。現在は Claude が手動運用。

---

### vloop

意味:

Claude CodeがVaultのToDoを読み、Epic単位でまとめて進めるコマンド。

小Issueを1件ずつ進めて止まるのではなく、Epic完了を優先する。

---

## 今のおすすめ確認順（2026-05-25 時点 / vloop11 更新）

1. [[../20_reviews/案件別ToDo一覧]] ✅ ToDo の正本（A 実行 / B 確認待ち / C 承認待ち / D 完了候補）
2. [[../20_reviews/vloop_queue]] ✅ vloop 実行対象（`[x]` のみ自動実行）
3. [[../20_reviews/ChatGPT承認待ち]] ✅ 承認待ち入口
4. [[../20_reviews/candidate-001_ChatGPT承認パック]] / [[../20_reviews/candidate-005_ChatGPT承認パック]] / [[../20_reviews/candidate-006_ChatGPT承認パック]] / [[../20_reviews/candidate-007_ChatGPT承認パック]]（4 候補の判断材料）
5. [[../05_monetization/epics]]（Epic A/B/C/D の全体進捗）
6. [[../06_research/2026-05-22_上位5案追加調査]]（候補選定の根拠）

> 2026-05-22 時点 → 2026-05-25 時点で更新。candidate-001 単独運用から candidate-001/005/006/007 並走運用へ。
> [[../20_reviews/案件別ToDo一覧]] が ToDo 運用の入口に昇格（Issue #80 / vloop9）。

## 旧おすすめ確認順（2026-05-22 時点 / 参照のみ）

1. `20_reviews/ChatGPT承認待ち.md` ✅ 実在・整備済（当時の判断対象 = candidate-001）
2. `20_reviews/candidate-001_ChatGPT承認パック.md`（candidate-001 の全判断材料・§1-§14）
3. `05_monetization/epics.md`（Epic A/B/C の全体進捗）
4. `06_research/2026-05-22_上位5案追加調査.md`（候補選定の根拠）
5. `05_monetization/idea_pool/`（自動生成案の倉庫）

> Issue #54 で `20_reviews/ChatGPT承認待ち.md` の実在を確認済み。最初にそこを見てよい。

---

## 注意点

- Issueコメントに書かれているだけでは、ファイルが存在するとは限らない
- 実在確認していないファイルを正規入口として扱わない
- GitHub管理Vaultを正本とする
- obsidian-sync-vaultだけにある内容は、GitHubへ反映されていない可能性がある
- 承認判断はChatGPT/ユーザー側で行う
- Claude/vloopは判断材料を作るが、勝手にapprovedへ変更しない
