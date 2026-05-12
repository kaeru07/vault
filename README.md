# Obsidian Vault — kaeru07

このリポジトリは Obsidian 用の Markdown Vault です。VPS と iPhone の Obsidian を GitHub private repo 経由で同期して運用します。

## 目的

- Claude Code プロンプトの保管・整備（軽量/標準/フル）
- アプリ仕様・運用メモ（progress / mahjong / nanikiru-shorts など）
- 作業レビュー（focus-work / ultrareview / 自己レビュー）の蓄積
- 収益化アイデア・リサーチノート
- 日々の Daily / Inbox での思考整理

## フォルダ構成

| フォルダ | 用途 |
|---|---|
| 00_inbox/ | とりあえずメモ・未分類アイデア |
| 01_daily/ | 日次ノート（today / 振り返り） |
| 02_apps/ | アプリ別の仕様・状態メモ |
| 03_prompts/ | Claude Code 用プロンプト雛形 |
| 04_reviews/ | 作業レビュー・差し戻し記録 |
| 05_monetization/ | 収益化アイデア・検討メモ |
| 06_research/ | リサーチ・市場調査・競合分析 |
| 90_templates/ | ノートテンプレート |
| attachments/ | 画像など添付。private/ 配下は git 管理外 |

## 運用ルール

- 1 ノート 1 トピック。ファイル名は内容が分かる短いタイトルに
- 軽い思いつきはまず `00_inbox/inbox.md` か新規メモへ。あとから分類
- アプリ単位の仕様は `02_apps/<app>.md` に集約
- Claude Code に渡すプロンプトは `03_prompts/` 配下を編集して使う
- レビュー・差し戻し記録は `04_reviews/` に時系列で蓄積
- 進行中の意思決定は Daily（`01_daily/today.md`）に当日メモ
- リンクは `[[<note-name>]]` を使う。ファイル移動時は Obsidian が自動追従

## 機密情報の取り扱い（重要）

このリポジトリには **絶対に含めない**:

- API キー / シークレット / トークン（Anthropic / OpenAI / Vercel / Supabase 等）
- `.env` ファイルや環境変数の値
- パスワード・認証情報・SSH 鍵
- 本番データのダンプ、個人情報を含む顧客データ

うっかり貼り付けた場合は **commit する前** に削除する。push 後に気付いた場合は履歴から削除（`git filter-repo` など）してリモートに force-push し、漏洩したシークレットは即座にローテーションする。

非公開で保管したい添付は `attachments/private/` 配下に置く。`.gitignore` で除外されているので push されない。

## GitHub 同期の基本コマンド

```bash
# 変更を取り込む（iPhone で編集したものを VPS に持ってくる）
git pull --rebase

# 変更を push（VPS の編集を iPhone に反映）
git add -A
git commit -m "update notes"
git push
```

iPhone 側は Obsidian Git プラグイン or Working Copy で同等のことを行う（後述）。

## リポジトリ

- remote: `git@github.com:kaeru07/vault.git`
- ブランチ: `main`
