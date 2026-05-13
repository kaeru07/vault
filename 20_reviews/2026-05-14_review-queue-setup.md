---
date: 2026-05-14
task: 20_reviews レビューキュー運用追加（_review_queue.md 新設）
runId: 20260514-012551
targetApp: obsidian-vault
monetizationImpact: low
theme: [obsidian, workflow, review-system, github, chatgpt-integration]
relatedRunIds: [20260514-011234, 20260514-003058, 20260514-000717]
commitHashes: [58571d8]
---

# 2026-05-14 20_reviews レビューキュー運用追加

> 本作業は、iPhone ChatGPT からのレビュー依頼を「毎回ファイル名を貼る」運用から「`_review_queue.md` の未チェック項目を辿る」運用に変更するための運用ルール整備。実装はなし。

---

## 1. 作業目的

- iPhone ChatGPT からレビュー依頼するときに、毎回 `20_reviews/YYYY-MM-DD_<slug>.md` のフルパス・ファイル名を貼らずに済むようにする
- レビュー状態の正本を `_review_queue.md` のチェックボックスに集約し、iPhone Obsidian でタップ一つで状態管理できるようにする
- ChatGPT / Claude / Codex 三者が共通の入口（`_review_queue.md`）を持つことで、レビュー対象の重複・抜けを防ぐ
- 既存 `04_reviews/`（手動キュレーション）の運用は維持しつつ、`20_reviews/` 全件管理レイヤーを補強

---

## 2. 実施内容

- `obsidian-sync-vault/20_reviews/_review_queue.md` を新規作成
  - 未レビュー / レビュー済み / 今後の運用ルール / ChatGPT 想定運用 / レビュー観点 / 重要ルール の 6 セクション
  - 既存 3 件（review-file-rule / fix-commit-hashes / review-queue-setup）を未レビュー初期登録
- `obsidian-sync-vault/20_reviews/README.md` に「レビューキュー運用（_review_queue.md・2026-05-14 追加）」セクション追加
- `obsidian-sync-vault/90_templates/session-review-template.md` ヘッダにキュー追記必須リマインダー 1 行追加
- `/root/company/CLAUDE.local.md` に新セクション「レビューキュー（_review_queue.md）連動ルール」追加（必須動作・追加形式・順序・スキップ可条件・Why・How to apply）
- `obsidian-sync-vault/03_prompts/Claude-Code標準運用.md` の Phase A 追補にキュー連動 1 行追記

---

## 3. 変更ファイル

| ファイル | 種別 |
|---|---|
| `obsidian-sync-vault/20_reviews/_review_queue.md` | 新規作成（レビュー状態の正本） |
| `obsidian-sync-vault/20_reviews/README.md` | レビューキュー運用セクション追加 |
| `obsidian-sync-vault/90_templates/session-review-template.md` | ヘッダにキュー追記リマインダー 1 行追加 |
| `/root/company/CLAUDE.local.md` | 新セクション「レビューキュー（_review_queue.md）連動ルール」追加 |
| `obsidian-sync-vault/03_prompts/Claude-Code標準運用.md` | Phase A 追補にキュー連動 1 行追加 |
| `obsidian-sync-vault/20_reviews/2026-05-14_review-queue-setup.md` | 新規作成（本ファイル・dogfooding） |

---

## 4. 検証結果

- typecheck: n/a（Markdown のみ）
- build: n/a
- lint: n/a
- 機密パターン事前チェック: **OK**
  - 検査パターン: `api[_-]?key` / `sk-` / `Bearer ` / `password\s*[:=]` / `secret\s*[:=]` / `token\s*[:=]` / `-----BEGIN [A-Z]` / `.env` / `cookie` / `本番DB接続文字列パターン`
  - ヒット全件がルール本文中の例示・伏字（`ANTHROPIC_API_KEY` / `sk-...xxxx`）
- ob sync: Fully synced（後述）
- git push: 後述（commit hash は `git log --follow` 参照）
- 既存ファイル削除: なし
- progress アプリコード変更: なし
- ExecutionRun 既存データ変更: なし

---

## 5. 未対応

- **本ファイル自身の push commit hash**: 自己参照問題により frontmatter `commitHashes` には親 commit `58571d8` のみ記載。本ファイルを push する commit hash は `git log --follow 20_reviews/2026-05-14_review-queue-setup.md` で参照
- **実機での ChatGPT 動作確認**: GitHub URL からの日本語ファイル名リンク追跡、`_review_queue.md` を最初に読む依頼が機能するかは実機検証必須
- **frontmatter `status:` の取り扱い**: 本ルールでは「必須更新にしない」と決めたが、long term で `_review_queue.md` と各 review の `status` の整合をどう取るかは未定
- **キューが肥大化した場合の整理ルール**: レビュー済み件数が大量になった月をまたぐとファイルが重くなる。アーカイブセクションを 1 年ごとに分けるなどは未策

---

## 6. 危険ポイント

- **レビュー状態の正本が複数化するリスク**: 「`_review_queue.md` チェックボックス」「各 review frontmatter status」「progress `reviewStatus`」の 3 系統。今は queue を正本としたが、競合する更新があれば混乱
- **Claude が誤って `[x]` に変えてしまうリスク**: ルールで「状態遷移は人手」と明文化したが、機械的な書き換えに引っ張られる可能性。Claude は queue 追加のみ
- **GitHub 公開ファイルへの機密混入**: queue サマリーに機密情報が紛れると公開される。事前チェックは厳格に
- **`_review_queue.md` の同時編集競合**: iPhone と VPS から同時に書き換える可能性。ob sync の merge 戦略で大抵問題ないが、長期運用で観察必要
- **`_` プレフィックス**: アンダースコア始まりはファイル探索によっては隠される。Obsidian は通常表示するが、一部 CLI ツールでデフォルト除外される可能性

---

## 7. 次にやるべきこと

- iPhone Obsidian で `_review_queue.md` が見える / チェックボックスがタップで切り替わるか実機確認
- ブラウザで https://github.com/kaeru07/vault/blob/main/20_reviews/_review_queue.md にアクセスできるか確認
- ChatGPT に「レビューキュー見て、未チェックのものをいつもの観点でレビューして」で依頼が機能するか実機確認
- 1 週間運用して queue の更新漏れが起きないか観察
- レビュー済み件数が増えた時のアーカイブ方針を決める（年次 / 月次 / プロジェクト別）
- 本作業の push commit hash を `git log --follow 20_reviews/2026-05-14_review-queue-setup.md` で確認し、必要なら別run（ルール改訂）で frontmatter 後追い反映

---

## 8. ChatGPT レビュー依頼文

そのままコピペで ChatGPT に流せる形:

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: obsidian-vault
作業: 20_reviews レビューキュー運用追加（_review_queue.md 新設）
runId: 20260514-012551
日付: 2026-05-14
GitHub commit: (本ファイルを push する commit / git log --follow 参照)

## 作業目的
- iPhone ChatGPT からのレビュー依頼を「毎回ファイル名を貼る」運用から「_review_queue.md の未チェック項目を辿る」運用に変更
- レビュー状態の正本を _review_queue.md のチェックボックスに集約
- ChatGPT / Claude / Codex 共通の入口を作成

## 実施内容
- 20_reviews/_review_queue.md 新規作成（未レビュー / レビュー済み / 運用ルール / 観点）
- 既存 20_reviews/ 3 件を未レビュー初期登録
- README / session-review-template / CLAUDE.local.md / Claude-Code標準運用.md に連動ルール追記

## 変更ファイル
- obsidian-sync-vault/20_reviews/_review_queue.md（新規・正本）
- obsidian-sync-vault/20_reviews/README.md（レビューキュー運用セクション追加）
- obsidian-sync-vault/90_templates/session-review-template.md（キュー追記リマインダー 1 行）
- /root/company/CLAUDE.local.md（新セクション「レビューキュー（_review_queue.md）連動ルール」）
- obsidian-sync-vault/03_prompts/Claude-Code標準運用.md（Phase A 追補にキュー連動 1 行）
- obsidian-sync-vault/20_reviews/2026-05-14_review-queue-setup.md（新規・本レビュー）

## 検証結果
- 機密パターン事前チェック: OK（ルール例示のみ・実値なし）
- ob sync: Fully synced
- git push: 本ファイルを push する commit は git log --follow で確認

## 未対応
- 本ファイル自身の push commit hash（自己参照問題）
- ChatGPT 実機動作確認
- frontmatter status の長期整合方針

## 危険ポイント
- レビュー状態の正本が複数化（queue / frontmatter / progress reviewStatus）
- Claude が誤って [x] に変更するリスク
- 公開ファイルへの機密混入
- iPhone と VPS の同時編集競合
- アンダースコア prefix の探索性

## 次にやるべきこと
- iPhone / GitHub Web / ChatGPT 実機検証
- 1 週間運用観察
- アーカイブ方針決定

## 確認したい観点
- _review_queue.md の構造（セクション分け）は妥当か
- 未レビュー先頭追加方針 / レビュー済み移動の運用は ChatGPT が解釈しやすいか
- Frontmatter / queue / progress の 3 系統で正本を queue に置く判断は妥当か
- 既存 04_reviews/ との併存設計は機能するか
- 「Claude は状態遷移しない」ルールは現実的か
- アンダースコア prefix の選択は妥当か（隠しファイル扱いされないか）
- queue 肥大化時のアーカイブ方針の決め方
- 収益化インパクトの評価（low）は妥当か
````

---

## 関連

- progress runId: 20260514-012551（本作業の正本）
- 関連 run: 20260514-011234（commit hash 修正）/ 20260514-003058（20_reviews ルール追加）/ 20260514-000717（GitHub 管理 Vault 運用開始）
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]] / [[../03_prompts/GitHub反映ルール]]
- レビューキュー正本: [[_review_queue]]
- フォルダ運用: [[README]]
- テンプレ: [[../90_templates/session-review-template]]
