# Obsidian Vault — kaeru07

このリポジトリは Obsidian 用の Markdown Vault です。VPS と iPhone の Obsidian を GitHub private repo 経由で同期して運用します。

**ここは AI 開発・収益化・作業レビューの「正本」**。会話ログや一時メモではなく、後から読み返して判断材料になる情報を蓄積する場所として運用します。

## 目的

- Claude Code プロンプトの保管・整備（軽量 / 標準 / フル）
- アプリ仕様・運用メモ（progress / mahjong / nanikiru-shorts など）
- 作業レビュー（focus-work / ultrareview / 自己レビュー）の蓄積
- 収益化アイデア・市場リサーチノート
- 日々の Daily / Inbox での思考整理

## フォルダ構成

| フォルダ | 用途 |
|---|---|
| 00_inbox/ | とりあえずメモ・未分類アイデア。後から分類して移動 |
| 01_daily/ | 日次ノート（today / 振り返り） |
| 02_apps/ | アプリ別の仕様・状態メモ。1 アプリ 1 ノート |
| 03_prompts/ | Claude Code 用プロンプト雛形（軽量 / 標準 / フル） |
| 04_reviews/ | 作業レビュー・差し戻し記録の時系列蓄積 |
| 05_monetization/ | 収益化アイデア・検討メモ |
| 06_research/ | リサーチ・市場調査・競合分析 |
| 90_templates/ | ノートテンプレート（コピーして使う） |
| attachments/ | 画像など添付。`private/` 配下は git 管理外 |

各フォルダ直下に `README.md` を置き、命名・運用ルールをそこに書く。迷ったらまず各フォルダ README を読む。

## 運用ルール（全フォルダ共通）

- 1 ノート 1 トピック。ファイル名は内容が分かる短いタイトルに
- 軽い思いつきはまず `00_inbox/inbox.md` か新規メモへ。あとから分類
- アプリ単位の仕様は `02_apps/<app>.md` に集約
- Claude Code に渡すプロンプトは `03_prompts/` 配下を編集して使う
- レビュー・差し戻し記録は `04_reviews/` に日付付きファイルで時系列に蓄積
- 進行中の意思決定は Daily（`01_daily/today.md`）に当日メモ
- リンクは `[[<note-name>]]` を使う。ファイル移動時は Obsidian が自動追従
- 新規ノートを作るときはまず `90_templates/` のテンプレートをコピーする

## 機密情報の取り扱い（絶対ルール）

このリポジトリには **絶対に含めない**:

- API キー / シークレット / トークン（Anthropic / OpenAI / Vercel / Supabase / GitHub 等）
- `.env` / `.env.local` ファイルの中身、環境変数の実値
- パスワード・認証情報・SSH 鍵・証明書（`.pem` / `.key`）
- 本番データのダンプ、個人情報を含む顧客データ
- 第三者の非公開情報・NDA 対象の資料

うっかり貼り付けた場合は **commit する前** に削除する。push 後に気付いた場合は履歴から削除（`git filter-repo` など）してリモートに force-push し、漏洩したシークレットは即座にローテーションする。

非公開で保管したい添付は `attachments/private/` 配下に置く。`.gitignore` で除外されているので push されない。`.env` / `.env.*` / `*.key` / `*.pem` / `*.sqlite` / `*.db` も同様に除外済み。

参考のためにキーや URL を載せたい場合は、**伏字**（`sk-...xxxx`）または **環境変数名のみ**（`ANTHROPIC_API_KEY`）を書く。

## Claude Code 作業後の Obsidian 追記運用

Claude Code（VPS 側）で作業した内容は、**作業終了時に必要分だけここへ追記する**。会話ログや `engineering/reports/` に残るログとは別に、後から読み返す価値のある情報を Obsidian 側に蓄積するのが目的。

**追記する対象（該当するものだけ）:**

| 追記先 | いつ追記するか |
|---|---|
| `02_apps/<app>.md` | アプリの仕様・運用ルール・重要ファイルが変わったとき |
| `03_prompts/*.md` | プロンプト雛形を改善 / 追加したとき |
| `04_reviews/YYYY-MM-DD-<title>.md` | 1 件の Claude Code 作業をレビューしたとき（`90_templates/claude-review-template.md` をコピー） |
| `05_monetization/*.md` | 収益化に関わる判断・検討メモが出たとき |
| `06_research/*.md` | 市場調査・競合分析の結果が出たとき |
| `01_daily/today.md` | その日の進捗・気付きを 1 行でも記録 |
| `00_inbox/inbox.md` | 上記いずれにも当てはまらない雑メモ |

**追記しないもの:**

- 作業ログ全文（`engineering/reports/focus-work-*.md` が正本）
- 機密情報（上記「機密情報の取り扱い」に該当するもの）
- 一時的な作業状態（`pm/active-projects.md` が正本）

**追記の流れ:**

1. Claude Code 側で作業 → `engineering/reports/focus-work-YYYYMMDD-HHMM.md` に記録 → progress アプリへ POST
2. 上記のうち「後から読み返す価値がある」ものを、該当する Obsidian ノートに 1 〜 3 行で追記
3. `01_daily/today.md` の「進捗 / 気付き」に当日分を 1 行で残す
4. commit & push（VPS）→ iPhone 側で pull

既存ファイルは消さない。差し戻し・棄却された場合も、判断の根拠として `04_reviews/` には残す。

## GitHub 同期の基本コマンド

```bash
# 変更を取り込む（iPhone で編集したものを VPS に持ってくる）
git pull --rebase

# 変更を push（VPS の編集を iPhone に反映）
git add -A
git commit -m "update notes"
git push
```

iPhone 側は Obsidian Git プラグイン or Working Copy で同等のことを行う。

## リポジトリ

- remote: `git@github.com:kaeru07/vault.git`
- ブランチ: `main`
