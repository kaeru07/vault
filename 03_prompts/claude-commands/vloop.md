# /vloop — Epic完了優先でVault ToDoを連続処理する

GitHub管理Vault（`/root/company/obsidian-vault`、remote: `git@github.com:kaeru07/vault.git`）をpullし、未完了ToDoを処理する。

重要: `/vloop` は小さいIssueを1件ずつ消化して止まるためのコマンドではない。親Epicの完了条件まで、可能な範囲でまとめて進める。

- `/vrun`: 1件だけ実行する
- `/vloop`: Epic単位で複数作業を連続実行する
- 4時間は目安であり、停止条件ではない
- 最大10件は上限だが、同一Epic内の関連作業は1つの作業群として扱う

---

## 最重要ルール: Epic完了優先

Issue #50により、以下を標準運用とする。

### 旧運用で起きていた問題

- Epic化したはずなのに小Issueが増え続けた
- 設計確認だけで止まることがあった
- 「安全にここまで」で止まりやすかった
- 「どこまで絶対続けるか」が弱かった
- レビューごとに次の小ToDoが増えて、実行が分断された

### 新ルール

- vloopは小ToDo消化ではなく、Epic完了を優先する
- 同じ目的の作業は新Issueを増やさず、既存Epic内で進める
- 途中Phase完了だけで止まらない
- 追加調査できるなら続行する
- candidate判断できるなら続行する
- レビュー用まとめまで作る
- 「次の小Issue作成」で止まらない
- 5〜10件相当の関連作業を1回でまとめて処理する
- 設計だけで終わらせず、実行できる範囲は実行する

### 止まってはいけない場合

- 小さいPhaseが終わっただけ
- 追加調査が残っている
- candidate判断が残っている
- レビュー用まとめが未作成
- 既存設計へのリンク確認だけ
- 「次のIssueを作ればよい」と判断しただけ

### 止まってよい場合

- 破壊的変更が必要
- 認証情報、課金、外部公開など人間判断が必要
- 明確な規約違反リスクがある
- 実行環境がなく物理的に進められない
- Epicの完了条件を満たした
- git pull / commit / push が失敗した
- Claude Codeの利用制限が近い

---

## 確認禁止ルール

- 原則、ユーザーに確認質問を投げない
- 「このまま進めますか？」は禁止
- 「次のToDoに進みますか？」は禁止
- 「AとBどちらにしますか？」は禁止
- 判断に迷う場合は、Vault、Issue本文、既存ルールから妥当な仮説を置いて進める
- 仮説は最終報告の「仮説」「未対応点」「次の一手」に記録する

---

## 重要ルール

- git pullせずに開始しない
- pushできない場合は完了扱いにしない
- 未確認項目を「確認済み」と書かない
- candidate / pending_approval の収益化シナリオをAI判断で approved にしない
- approvedでない収益化シナリオをprogressへ送らない
- 外部公開、課金設定、広告アカウント操作はしない
- 秘密情報、APIキー、トークン操作はしない
- データ削除、破壊的変更はしない
- 収益化インパクトを最優先する
- Claude実行対象外Issueを作業対象にしない

---

## 実行フロー

### Step 1: Vaultを最新化

- `/root/company/obsidian-vault`へ移動
- `git status`
- `git pull origin main`
- pull失敗時は停止条件

### Step 2: 未完了ToDoを確認

- GitHub Issue一覧を確認
- `00_inbox/`、`05_monetization/`、`20_reviews/_review_queue.md`も確認
- ただし大量Issueを機械的に全部読み込まない
- 候補一覧から、収益化インパクトとEpic優先度で処理対象を選ぶ

### Step 3: Epicを選ぶ

優先順位:

1. 収益化インパクトが高いEpic
2. すでに実データ・成果物があり、次へ進められるEpic
3. 完了条件が明確なEpic
4. 安全に実行できるEpic

同じEpic内の関連作業は、新Issueを増やさずまとめて進める。

### Step 4: Epic内で続行する

1つの小Phaseだけで終わらない。

例:

- 情報収集だけで止まらない
- 30案生成だけで止まらない
- 上位5件抽出だけで止まらない
- 追加調査だけで止まらない
- candidate化判断まで進める
- 最後にレビュー用まとめを作る

### Step 5: 変更確認

- `git status`
- `git diff --stat`
- 機密情報混入チェック
- 未確認項目の確認済み扱い禁止

### Step 6: commit / push

Vault変更があれば必ずcommit / pushする。

commit message例:

`vloop: Epic A 情報収集からcandidate判断まで実行`

push成功まで完了扱いにしない。

### Step 7: Issueコメント

GitHub Issueがある場合は、実施内容、commit hash、未対応点をコメントする。

Issueを自動closeしない。close判断はユーザー側。

### Step 8: 一括サマリー

ループ終了時は必ず一括サマリーを作成し、commit / pushする。

保存先:

- `03_prompts/claude-commands/logs/vloop_YYYY-MM-DD_HHMM.md`

必須項目:

- 実行日時
- 実行件数
- 対象Epic
- できるようになったこと
- 変更ファイル
- commit hash
- 停止理由
- 未対応点
- 次の一手
- ChatGPTレビュー依頼文

### Step 9: 終了時必須出力（Issue #66）

一括サマリーに加えて、Issue 単位の状態分類を必ず出す。詳細は [[../../20_reviews/Issue完了判定ルール]]。

必須項目:

- 今回の対象Issue
- 処理済みIssue（状態分類込み）
- **未処理Issue一覧（省略禁止）**
- 各Issueの状態: done / user_check / open / merged / obsolete / blocked
- レビュー状態: reviewed_ok / reviewed_followup / user_check / not_reviewed
- 停止理由（正当な停止条件のどれか）
- 次に処理すべきIssue
- 停止理由の正当性判定（正当 / 不正）

不正な停止例（してはいけない）:

- 1件だけ終わった
- 次Issueを作った
- 設計だけ終わった
- **コメントだけ付けた**
- 完了っぽいと判断した
- レビュー対象を明記せず終了
- 未処理Issueを一覧化せず終了

「コメント済み = done」ではない。コメント済みでも user_check / open のままがある。状態分類は本ファイル §状態分類 + [[../../20_reviews/Issue完了判定ルール]] §1 を参照。

---

## 最終報告フォーマット

コードブロックで囲まず、プレーンテキストで出す。

実行件数:
対象Epic:
できるようになったこと:
変更ファイル:
commit hash:
push:
一括サマリー:
1枚図サマリー:
成果物紹介:
仮説:
未対応点:
停止理由:
次の一手:
ChatGPTレビュー依頼文:

---

## 1枚図サマリーのルール

レビュー用に、できるようになったことを1枚図で示す。

固有名詞には日本語注釈を付ける。

例:

- research-run = 情報収集を自動で行う処理
- idea-run = 収集した情報から案を作る処理
- cron = 決まった時間に自動実行する仕組み
- candidate = 承認前の有力候補
- vloop = Claude側でEpicをまとめて進める実行コマンド

---

## 関連

- [[../Claude-Code標準運用]]
- [[../../05_monetization/ChatGPT承認ゲート標準]]
- [[../../05_monetization/ChatGPT承認コマンド標準]]
- [[../../20_reviews/ChatGPT承認待ち]]
- Issue #50: vloopを小ToDo消化ではなくEpic完了まで進める運用に変更する
