# /vloop — Vault ToDo を pull して連続処理する（最大10件 / 4時間は目安）

GitHub 管理 Vault（`/root/company/obsidian-vault`、remote: `git@github.com:kaeru07/vault.git`）の
未完了 ToDo を **pull して読み込み、1 件ずつ順番に処理**し、
**最大 10 件まで（4 時間は目安）** 連続実行する。各 ToDo 完了ごとに必ず commit / push し報告を残す。

- `/vrun` は **1 件実行用**（1 サイクルで終了）
- `/vloop` は **複数件の連続実行用**（停止条件まで止まらない）

ChatGPT が Vault に書き込み、Claude Code が pull して連続処理する片方向の作業チャネル。

---

## 基本方針

- 最大 **10 件**まで処理する（10 件は上限・必ず停止）
- **4 時間は目安**（ハード上限ではない）。開始時刻を記録し各サイクル開始前に経過時間を確認するが、
  4 時間を超えても **利用制限・大逸脱・危険操作がなければ継続してよい**
  - 4 時間到達時は「区切りチェックポイント」を作り、停止条件を再点検してから継続/停止を判断
  - 継続する場合も理由を作業ログに残す（無確認で延長してよいが足跡は残す）
- **1 件ごとに commit / push** する（複数 ToDo を 1 commit にまとめない）
- **1 件ごとに完了報告（作業ログ）を残す**
- ユーザーへの途中確認は **原則しない**
- 「続けてよいですか？」「次を実行しますか？」のような確認は **禁止**
- 停止条件に該当しない限り、確認せず次の ToDo へ進む

---

## 確認禁止ルール（厳守）

- 原則、ユーザーに確認質問を投げない
- 「このまま進めますか？」は **禁止**
- 「次の ToDo に進みますか？」は **禁止**
- 「A と B どちらにしますか？」は **禁止**
- 判断に迷う場合は、Vault・Issue 本文・既存ルール（CLAUDE.md / CLAUDE.local.md）から
  **妥当な仮説を置いて進める**
- 仮説は最終報告の「仮説」「未対応点」「次の一手」に記録する
- 停止条件に該当しない限り、作業を継続する

---

## 重要ルール

- **git pull せずに開始しない**。pull 失敗時は停止条件として止める
- **一括 for ループで大量 Issue を読まない**。`gh issue list` で候補確認後、
  **1 件ずつ `gh issue view <番号>`** する
- `Contains simple_expansion` 等が出やすい Bash 構文（複雑な変数展開・多重クォート・
  ヒアドキュメント乱用）を避ける。1 コマンド1 目的でシンプルに書く
- `candidate` / `pending_approval` の収益化シナリオを **AI 判断で `approved` にしない**
  - 承認は ChatGPT（方向性決定）＋人間（status 書換）。vloop は判断材料作成まで
    （[[../../05_monetization/ChatGPT承認ゲート標準]]）
  - 判断材料が揃ったら [[../../90_templates/chatgpt-approval-template]] を使って [[../../20_reviews/ChatGPT承認待ち]] に 1 ブロック追記する（Issue #19。approved 化はしない）
  - 承認待ちを提示する際は **承認コマンド例**（`<scenarioId> approve|hold|reject`）を必ず併記する（[[../../05_monetization/ChatGPT承認コマンド標準]] / Issue #24）
- **`approved` でない収益化シナリオを progress へ送らない**
- **外部公開・課金設定・広告アカウント操作はしない**（停止条件）
- **秘密情報 / API キー / トークン操作はしない**（停止条件）
- **データ削除・破壊的変更はしない**（停止条件）
- **収益化インパクトを最優先**に次の 1 件を選ぶ
- **一度に複数 ToDo へ広げない**（1 サイクル 1 ToDo。連続処理だが各回は単一 ToDo）
- **push できない場合は完了扱いにしない**
- **未確認項目を「確認済み」と書かない**
- **Claude 実行対象外 Issue を作業対象にしない**（Issue #41）
  - 主体が ChatGPT のレビュー運用ルール Issue（例: Issue #40 / [[../ChatGPTレビュー手順]]）
  - ラベル `review` かつ本文で主体者が ChatGPT と明示されている Issue
  - これらは Issue コメントで「Claude 実行対象外」を 1 行報告し、次の Issue へ進む（実装・追記しない）

---

## 実行フロー

### 開始時（1 回だけ）

- 開始時刻を記録する（4 時間目安の起点）
- 処理済みカウンタを 0 で初期化する（10 件上限の起点）

### Step 1: Vault へ移動して最新を取得

```bash
cd /root/company/obsidian-vault
git status
git pull origin main
```

- pull が **コンフリクト / 認証 / ネットワーク**で失敗 → 停止条件。作業せず報告

### Step 2: 未完了 ToDo を確認する

`gh issue list` で候補を一覧化してから、選んだ 1 件だけ `gh issue view` する。

```bash
gh issue list --repo kaeru07/vault --state open
```

- 一括 for ループで全 Issue 本文を読まない（候補一覧 → 1 件選定 → その 1 件のみ view）
- Issue 以外の ToDo 源も確認:
  - `00_inbox/` / `05_monetization/`（status: candidate / pending_approval / approved に注意）
  - `20_reviews/_review_queue.md` の未チェック `[ ]`
  - その他の未完了メモ（`- [ ]` / `TODO` / `要対応`）

### Step 3: 次に実行する 1 件を選ぶ

- **収益化インパクトを最優先**（high > medium > low > none）
- **Epic 単位で関連 Issue を続けて選ぶ**（Issue #42 / [[../Epic単位運用]]）。同 Epic 内は依存順（A → B → C）で進める
- 同等なら priority / 完了条件の明確さ / 安全度
- `candidate` / `pending_approval` は承認待ち＝実装対象にしない（仮説で承認扱いにしない）
- 選んだ ToDo と理由を 1〜2 行で宣言（確認は投げない）

### Step 4: 対象 1 件の本文を精読する

```bash
gh issue view <番号> --repo kaeru07/vault
```

- 禁止事項・変更禁止範囲・完了条件を読む
- 完了条件が不明でも確認は投げない。Vault / 過去運用 / 既存ルールから
  **完了条件を仮説設定**し報告に明記して進める
- Issue 本文と既存ルールが**明確に矛盾**する場合のみ停止条件

### Step 5: 作業を実行する

- Step 4 で確認・仮説設定した範囲のみ実装・確認する
- 別リポジトリ実装が必要なら company CLAUDE.md の原則に従いそのリポジトリ内で完結
- 外部公開 / 課金 / 広告 / 秘密情報 / データ削除 / 破壊的変更が必要になったら
  その ToDo は実行せず**停止条件**として報告（次へ進まずループ終了）

### Step 6: 変更ファイルを確認する

```bash
cd /root/company/obsidian-vault
git status
git diff --stat
```

- 機密パターン（`.env` / `api[_-]?key` / `sk-` / `Bearer ` / `password` /
  `secret` / `token` / `-----BEGIN`）混入チェック。混入時は commit せず停止条件

### Step 7: commit / push する（1 件ごと）

```bash
cd /root/company/obsidian-vault
git add -A
git commit -m "vloop: <対象 ToDo の要約>"
git push origin main
```

push 後確認:

```bash
git status
git log --oneline -3
```

- push 成功まで完了扱いにしない。失敗時は停止条件（理由・未 push hash・手動コマンドを報告）

### Step 8: GitHub Issue があれば完了コメント

```bash
gh issue comment <番号> --repo kaeru07/vault --body "<実施内容と commit hash の短い要約>"
```

- Issue を**自動 close しない**（人間判断。本文に明示指示があり完了条件充足時のみ close）

### Step 9: この ToDo の作業ログ報告を残す

- 1 件ごとに「完了 ToDo / commit hash / push 結果 / 仮説 / 未対応点」を記録
- CLAUDE.local.md の progress POST 等の運用ルールはそのまま適用

### Step 10: 次サイクルへ（確認せず）

- 処理済みカウンタを +1
- 停止条件を判定:
  - 10 件処理した → 停止（上限・正常終了）
  - 開始から 4 時間経過 → **即停止ではない**。区切りチェックポイントを作り、
    利用制限・大逸脱・危険操作が無ければ **継続してよい**（継続理由を作業ログに残す）
  - open ToDo が無くなった → 停止（正常終了）
  - その他の停止条件に該当 → 停止（理由を報告）
- いずれにも該当しなければ **確認せず Step 1（pull）から次の 1 件へ**

> 各サイクル開始前に必ず `git pull` し直す（連続実行中に ChatGPT 側が追記する可能性があるため）。

### Step 11: 一括サマリーを残す（ループ終了時・必須）

ループを停止したら（正常終了・停止条件いずれも）、**必ず一括サマリーを1ファイル残す**。

- 保存先（どちらか・1ファイル）:
  - `20_reviews/_vloop-summary_YYYY-MM-DD_HHMM.md`、または
  - `03_prompts/claude-commands/logs/vloop_YYYY-MM-DD_HHMM.md`
  - 既定は `03_prompts/claude-commands/logs/`（無ければ作成）
- 必須項目（すべて記載・省略禁止）:
  - 実行日時（開始〜終了 / JST）
  - 実行件数
  - 完了 Issue（Issue 由来は番号、それ以外は ToDo 名）
  - 各 commit hash（全件）
  - 停止理由（どの停止条件か。正常終了も明記）
  - 次に残った ToDo（未着手・承認待ち・別タスク化したもの）
- このサマリーファイルも **commit / push する**（[[../GitHub反映ルール]] 準拠）
- サマリー未作成のままユーザーへ最終応答を返さない（最終報告の前提条件）

---

## 停止条件（これ以外では止まらない・確認しない）

- 最大 10 件を完了した（上限・正常終了）
- open ToDo がなくなった（正常終了）
- ※ 4 時間到達は**停止条件ではない**（目安）。区切りチェックポイントを作り、
  利用制限・大逸脱・危険操作が無ければ継続してよい
- Claude Code の利用制限 / Pro 上限が近い
- 外部公開が必要になった
- 課金設定が必要になった
- 広告アカウント操作が必要になった
- 秘密情報 / API キー / トークン操作が必要になった
- データ削除 / 破壊的変更が必要になった
- Issue 本文と既存ルールが明確に矛盾した
- git pull / commit / push が失敗した
- 作業継続に必要なファイルが存在しない

---

## 停止時の対応

- 停止条件に該当した場合だけ止まる（4 時間到達は停止条件ではない）
- 停止したら **必ず Step 11 の一括サマリーを残してから**最終応答を返す
- その場合も**質問ではなく、停止理由・途中成果・次の一手を報告**する
- 「どうしますか？」で終わらない
- **次にユーザーが取るべき具体行動を 1 つ**書く

---

## 最終報告フォーマット（このフォーマットで出す）

コードブロックで囲まず、プレーンテキストで以下を出力する:

実行件数:
完了ToDo:
各ToDoのcommit hash:
push:
一括サマリー:
成果物紹介:
仮説:
未対応点:
停止理由:
次の一手:
ChatGPTレビュー依頼文:

- `一括サマリー:` は Step 11 で残したファイルパスと push 状態を明記（未作成は不可）
- `push:` は各件 `pushed` / `pending（理由）` を明記
- `成果物紹介:` は各 ToDo の **何ができたか / どこで見れるか / 何に使うか / どう使うか / 次に見るファイル / 注意点** を 1〜3 行で明記（Issue #21 / [[../../04_reviews/Claude作業レビュー運用]] §成果物紹介）
- `仮説:` は完了条件を仮説設定した箇所をすべて列挙
- `停止理由:` はどの停止条件で止まったかを 1 つ明記（正常終了も理由として書く）
- `ChatGPTレビュー依頼文:` はそのまま ChatGPT に貼れる文面（対象アプリ / commit hash / 観点）
- 未確認の項目を「確認済み」と書かない

$ARGUMENTS
