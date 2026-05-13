---
date: 2026-05-14
task: 20_reviews commitHashes 運用整理（意味明確化 + reviewFileCommit 任意フィールド追加）
runId: 20260514-013531
targetApp: obsidian-vault
monetizationImpact: low
theme: [obsidian, workflow, review-system, github]
relatedRunIds: [20260514-012551, 20260514-011234, 20260514-003058, 20260514-000717]
commitHashes: [77ce0a3]
# reviewFileCommit:   # 任意・未記入
---

# 2026-05-14 20_reviews commitHashes 運用整理

> 本ルール適用後の**最初の運用例**を兼ねる（dogfooding）。
> `commitHashes` には本作業の関連 commit `77ce0a3`（前 run の push hash）のみを記録し、**本ファイル自身を push する commit は記録していない**（新ルール準拠）。
> 自身の push commit を後から知りたい場合は `git log --follow 20_reviews/2026-05-14_clarify-commit-hashes.md` で参照する。

---

## 1. 作業目的

- 20_reviews/ の自動生成ルールに潜む**自己参照問題**を運用ルールで根本解決する
- `commitHashes` を「レビュー対象の作業 commit / 関連 commit」に限定する形で意味を再定義
- レビュー Markdown 自身を push した commit を frontmatter に**必須記録しなくてよい**ことを明文化
- 任意フィールド `reviewFileCommit:` を用意し、必要な人だけ後追いで埋められる余地を残す

---

## 2. 実施内容

- `/root/company/CLAUDE.local.md` の「作業レビューファイル自動生成ルール § Frontmatter」を更新:
  - frontmatter サンプルに `# reviewFileCommit:` 任意行を追加
  - 新サブセクション「commitHashes の意味（2026-05-14 明確化）」追加
  - 記録対象 / 記録対象外 / 理由 / reviewFileCommit 任意フィールドの使い方 / 既存レビューへの遡及非適用を明記
- `obsidian-sync-vault/20_reviews/README.md` の「必須セクション」直下に同等の説明追加
- `obsidian-sync-vault/90_templates/session-review-template.md` の frontmatter に commitHashes コメントと `reviewFileCommit` 任意行を追加
- 既存 3 件の review file（review-file-rule / fix-commit-hashes / review-queue-setup）は**変更しない**（遡及適用なし）
- `_review_queue.md` と `2026-05-14_fix-commit-hashes.md` の登録は前 run（20260514-012551）で完了済みのため**重複作業しない**（破壊回避）
- 本ファイルを `_review_queue.md` の未レビュー先頭に追加

---

## 3. 変更ファイル

| ファイル | 種別 |
|---|---|
| `/root/company/CLAUDE.local.md` | commitHashes 意味明確化 + reviewFileCommit 任意フィールド追加 |
| `obsidian-sync-vault/20_reviews/README.md` | commitHashes の意味追記（必須セクション直下） |
| `obsidian-sync-vault/90_templates/session-review-template.md` | frontmatter にコメント + reviewFileCommit 任意行 |
| `obsidian-sync-vault/20_reviews/2026-05-14_clarify-commit-hashes.md` | 新規（本ファイル・dogfooding） |
| `obsidian-sync-vault/20_reviews/_review_queue.md` | 未レビュー先頭に本ファイル追加 |

obsidian-vault 側も同じ 5 ファイル（ミラー先）。

---

## 4. 検証結果

- typecheck: n/a
- build: n/a
- lint: n/a
- 機密パターン事前チェック: **OK**（commit hash のみ、`.env` / API キー / cookie / token / password / sk- / Bearer / `-----BEGIN` 該当なし）
- ob sync: Fully synced（後述）
- git push: 後述
- 既存ファイル削除: なし
- progress アプリコード変更: なし
- ExecutionRun 既存データ変更: なし
- 既存レビューへの遡及適用: **なし**（仕様通り）

---

## 5. 未対応

- **既存 3 件の review file の commitHashes 整合**: 旧定義時に push 自己参照 hash が含まれているが、遡及適用しない方針のため変更しない（再 commit による履歴汚染回避）
- **reviewFileCommit を実際に使う運用**: 任意化したが、現時点で使用ケースはない。1〜2 ヶ月運用して使われなければ削除候補
- **CLAUDE.local.md / 20_reviews/README.md / template.md の 3 箇所同期保守**: ルール変更時の更新漏れリスク。ChatGPT レビューで指摘される可能性
- **本ファイル自身の push commit**: 仕様通り frontmatter には記載しない。`git log --follow` で参照（自己参照問題の暫定対応）

---

## 6. 危険ポイント

- **遡及適用なしの方針が誤解されるリスク**: 「過去ファイルも揃えるべき」と読まれる可能性。本ルール明記で対処
- **ユーザー指示と既存状態の乖離**: 「_review_queue.md を新規作成」とあったが前 run で既に作成済み。重複作成を避けるため確認のみで進めた。判断の妥当性確認が必要
- **任意フィールド乱立リスク**: `reviewFileCommit` を追加することで、将来「commitHashes / reviewFileCommit / referencedCommits / parentCommits...」と肥大化する余地。フィールド追加は最小限に
- **CLAUDE.local.md の肥大化**: ルール本体が増え続けており、可読性が低下している懸念

---

## 7. 次にやるべきこと

- 次回 20_reviews/ 生成時に本ルールが想定通り適用されるか観察
- 1〜2 ヶ月後に reviewFileCommit の使用件数を集計し、ゼロなら削除判断
- CLAUDE.local.md の構造リファクタ要否判定（現状 3〜4 大セクションが「Obsidian / progress / 20_reviews / 完了マーカー」で重複している）
- 既存 3 件の commitHashes を遡及適用したいユーザー要望が来た場合の対応手順（本 ID を引いて差分 PR）

---

## 8. ChatGPT レビュー依頼文

そのままコピペで ChatGPT に流せる形:

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: obsidian-vault
作業: 20_reviews commitHashes 運用整理（意味明確化 + reviewFileCommit 任意フィールド追加）
runId: 20260514-013531
日付: 2026-05-14
GitHub commit: (本ファイル push 後 git log --follow で確認)

## 作業目的
- 20_reviews/ 自動生成の自己参照問題を運用ルール側で根本解決
- commitHashes を「レビュー対象の作業 commit / 関連 commit」に限定再定義
- レビュー Markdown 自身を push した commit hash は必須記録から外す
- 任意フィールド reviewFileCommit を追加（未記入可）

## 実施内容
- CLAUDE.local.md / 20_reviews/README.md / session-review-template.md の 3 ファイルに反映
- 既存レビューへは遡及適用しない方針を明記
- 前 run で完了済みの _review_queue.md 新規作成 / fix-commit-hashes 登録は重複作業しない

## 変更ファイル
- /root/company/CLAUDE.local.md（commitHashes 意味明確化 + reviewFileCommit 任意フィールド）
- obsidian-sync-vault/20_reviews/README.md（commitHashes の意味追記）
- obsidian-sync-vault/90_templates/session-review-template.md（frontmatter コメント + reviewFileCommit）
- obsidian-sync-vault/20_reviews/2026-05-14_clarify-commit-hashes.md（新規・本ファイル）
- obsidian-sync-vault/20_reviews/_review_queue.md（未レビュー先頭に本ファイル追加）

## 検証結果
- 機密パターン: OK（commit hash のみ、実値なし）
- ob sync: Fully synced
- git push: 本ファイル push hash は git log --follow で参照

## 未対応
- 既存 3 件の commitHashes 整合（遡及適用なし方針のため変更しない）
- reviewFileCommit を実際に使う運用の有無は今後観察
- CLAUDE.local.md の肥大化

## 危険ポイント
- 遡及適用なしの方針が誤解されるリスク
- ユーザー指示と既存状態の乖離（重複指示への対応判断）
- 任意フィールド乱立リスク
- ルール文書の肥大化

## 次にやるべきこと
- 次回 20_reviews/ 生成時のルール適用観察
- reviewFileCommit 使用件数の集計（1〜2 ヶ月後）
- CLAUDE.local.md 構造リファクタ要否判定
- 既存レビューへの遡及適用要望が来た場合の対応設計

## 確認したい観点
- commitHashes の意味再定義（対象作業 / 関連 commit のみ）は妥当か
- reviewFileCommit を任意化する判断は妥当か（必須化すべきか / 削除すべきか）
- 既存レビューへ遡及適用しない判断は妥当か（破壊リスク回避 vs 整合性）
- ユーザー指示『_review_queue.md を新規作成』に対し『既存』と判断して再作成しなかった判断は妥当か
- 3 ファイル同期保守の運用は持続可能か（自動チェックすべきか）
- 本ファイル自身の push commit hash を frontmatter に書かない判断は妥当か
- 収益化インパクトの評価（low）は妥当か
````

---

## 関連

- progress runId: 20260514-013531（本作業の正本）
- 関連 run: 20260514-012551（_review_queue.md 新設）/ 20260514-011234（commit hash 修正・自己参照問題の発端）/ 20260514-003058（20_reviews 自動生成ルール追加）/ 20260514-000717（GitHub 管理 Vault 運用開始）
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]] / [[../03_prompts/GitHub反映ルール]]
- レビューキュー正本: [[_review_queue]]
- フォルダ運用: [[README]]
- テンプレ: [[../90_templates/session-review-template]]
