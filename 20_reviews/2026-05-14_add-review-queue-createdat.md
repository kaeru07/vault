---
date: 2026-05-14
task: _review_queue.md に createdAt フィールドを追加（既存補完 + ルール反映）
runId: PENDING
targetApp: obsidian-vault
monetizationImpact: low
theme: [obsidian, workflow]
relatedRunIds: []
commitHashes: []
---

# 2026-05-14 _review_queue.md に createdAt フィールド追加

> レビューキューに「いつ作られた項目か」が分かる createdAt を導入し、関連ドキュメント / ルールを揃える。

---

## 1. 作業目的

- なぜこの作業をするのか: レビューキュー上で各項目の鮮度（古い未レビュー / 直近の未レビュー）を一目で判断したい。
- 背景: 現状の `_review_queue.md` は `[ ]` / `[x]` の状態しか持たず、「何日前から残っているか」が iPhone Obsidian でぱっと見えない。GitHub 経由で ChatGPT がレビューする際にも優先順位の参考情報が薄い。
- 期待効果: createdAt があれば、「未レビューが何日溜まっているか」を ChatGPT 側でも数えられ、古い項目を優先的に消化する運用が回せる。

---

## 2. 実施内容

- 主な変更（箇条書きで具体的に）:
  - `obsidian-sync-vault/20_reviews/_review_queue.md` の項目テンプレに `createdAt: YYYY-MM-DD HH:mm` を追加
  - 既存 4 項目（clarify-commit-hashes / review-queue-setup / fix-commit-hashes / review-file-rule）に createdAt を補完（sync-vault 側の mtime ベース）
  - 本作業分の項目（add-review-queue-createdat）を未レビュー先頭に追加（createdAt: 2026-05-14 08:13）
  - `_review_queue.md` 末尾「今後の運用ルール」に createdAt の記入要領 + reviewedAt 非導入の方針を明記
  - `obsidian-sync-vault/20_reviews/README.md` のキュー運用セクションに createdAt 必須の追補を記載
  - `obsidian-sync-vault/90_templates/session-review-template.md` の冒頭注釈に createdAt 記入の指示を追加
  - `/root/company/CLAUDE.local.md` § レビューキュー連動ルールの追加形式を更新（createdAt 行を追加・省略禁止と明記）
- 関連調査・判断:
  - 既存項目の createdAt は sync-vault 側ファイルの mtime（最新編集時刻）ではなく、初版作成に近い値を採用したかったので obsidian-vault 側の `git log --diff-filter=A` の commit 時刻を一旦突き合わせた上で、より早い sync-vault mtime を採用（ファイル作成 → 編集 → commit の時系列で commit 時刻 ≧ mtime のため）。
  - `reviewedAt` は導入しない（軽量運用維持・`[x]` を正本とする方針継続）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `obsidian-sync-vault/20_reviews/_review_queue.md` | 既存 4 項目に createdAt 補完 / 新規項目追加 / 追加形式に createdAt 行を追加 + reviewedAt 非導入を明記 |
| `obsidian-sync-vault/20_reviews/README.md` | キュー運用セクションに createdAt 必須の追補 |
| `obsidian-sync-vault/90_templates/session-review-template.md` | 冒頭注釈に createdAt 記入指示を追加 |
| `obsidian-sync-vault/20_reviews/2026-05-14_add-review-queue-createdat.md` | 本レビューファイル新規作成 |
| `/root/company/CLAUDE.local.md` | § レビューキュー連動ルールの追加形式に createdAt 必須化を反映 |

---

## 4. 検証結果

- typecheck: n/a（Markdown のみ）
- build: n/a
- lint: n/a
- 手動確認: `_review_queue.md` を Read で再確認し、追加形式と既存項目の整合を確認
- 機密パターン事前チェック: OK（API キー / トークン / .env / 鍵本文の追加なし）
- ob sync: PENDING（最終応答前に実行）
- git push: PENDING（mirror → commit → push を最終応答前に実行）

---

## 5. 未対応

- スキップした項目: なし
- 環境制約で実行できなかったこと: なし
- ユーザー判断待ち: createdAt の運用が回り始めた後、古い項目を一括消化する仕組み（例: createdAt から N 日経過で priority を自動上げ）が必要かどうかは別途判断

---

## 6. 危険ポイント

- 既存機能への影響リスク: なし（Markdown 追記のみ。Obsidian / iPhone 側の表示崩れもなし）
- DB / 認証 / 本番 / 機密に触れたか: 該当なし
- ロールバック手段: 直前 commit の revert で元に戻せる（commit 単位の変更）
- 観察すべき項目: iPhone Obsidian で createdAt 行が崩れずに表示されるか、ChatGPT が createdAt を読んで優先度判断に使えるか

---

## 7. 次にやるべきこと

- フォローアップ作業: 当面の運用観察（createdAt が機能しているか / iPhone 編集時に崩れないか）
- ユーザー判断待ち事項: 古い未レビュー項目の自動エスカレーション要否
- 観察項目（数日〜数週間）: createdAt 入りキューでの ChatGPT レビュー精度・抜けの増減
- 関連 ToDo の追加候補: 必要に応じて `_review_queue.md` のソート順を「createdAt 古い順」に切り替える運用提案

---

## 8. ChatGPT レビュー依頼文

そのままコピペで ChatGPT に流せる形:

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: obsidian-vault
作業: _review_queue.md に createdAt フィールド追加（既存補完 + ルール反映）
runId: PENDING（progress POST 後に確定）
日付: 2026-05-14
GitHub commit: PENDING

## 作業目的
- レビューキュー上で各項目の鮮度を可視化したい
- ChatGPT が GitHub 経由でレビューする際、createdAt を優先度判断に使えるようにする

## 実施内容
- _review_queue.md 各項目に createdAt 行を追加（既存 4 件補完、本作業 1 件追加）
- _review_queue.md の追加形式テンプレに createdAt を組み込み + reviewedAt 非導入を明記
- README.md / session-review-template.md / CLAUDE.local.md に createdAt 必須化を反映

## 変更ファイル
- obsidian-sync-vault/20_reviews/_review_queue.md
- obsidian-sync-vault/20_reviews/README.md
- obsidian-sync-vault/90_templates/session-review-template.md
- obsidian-sync-vault/20_reviews/2026-05-14_add-review-queue-createdat.md
- /root/company/CLAUDE.local.md

## 検証結果
- typecheck / build / lint: n/a（Markdown のみ）
- 手動確認: ファイル再読込で整合確認
- 機密スキャン: OK

## 未対応
- なし

## 危険ポイント
- なし（Markdown 追記のみ）

## 次にやるべきこと
- 運用観察（createdAt が機能しているか）
- 古い項目の自動エスカレーション要否の判断

## 確認したい観点
- createdAt のフォーマット（YYYY-MM-DD HH:mm / JST）が iPhone Obsidian と ChatGPT 両方で扱いやすいか
- reviewedAt を入れない方針は妥当か（[x] のみで十分か）
- 既存 4 項目への createdAt 補完値（sync-vault mtime ベース）が妥当か
- README / template / CLAUDE.local.md の 3 箇所更新で抜けがないか
- 収益化インパクトの評価は妥当か（low）
````

---

## 関連

- progress runId: PENDING（progress POST 後に確定）
- 関連 run: 20260514-002846 系列（20_reviews 自動生成ルール / commitHashes 整理）
- 関連アプリ: [[../02_apps/obsidian-vault]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
