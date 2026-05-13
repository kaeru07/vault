# 20_reviews/ — 作業レビュー（セッション単位・自動生成）

> Claude / Codex / Claude Code が**作業を終えるたびに 1 ファイル**生成する作業レビュー。
> ChatGPT 等の外部レビュアーに即コピペで流せる依頼文を含む。
> Claude Code 側の正本ルールは `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール。

---

## 04_reviews/ との違い

| フォルダ | 性質 | 件数 | 内容 |
|---|---|---|---|
| `04_reviews/` | **手動キュレーション**（既存） | 厳選 | 重要なレビュー記録 / 差し戻し / 棄却 / 危険判断 |
| `20_reviews/` | **作業終了時自動生成**（新） | 全件 | 1 作業 1 ファイル / ChatGPT 依頼文込み |

両者は併存。重要なものは `04_reviews/` に**昇格**して残してもよい（リンクで参照）。

---

## ファイル命名

```
20_reviews/YYYY-MM-DD_<task-slug>.md
```

- `task-slug`: 内容が分かる英数ハイフン（例: `vault-init` / `phase-a-test` / `review-file-rule`）
- 同日複数件は task-slug で区別（連番は付けない）
- 衝突したら task-slug を具体化

---

## 必須セクション

1. 作業目的
2. 実施内容
3. 変更ファイル
4. 検証結果
5. 未対応
6. 危険ポイント
7. 次にやるべきこと
8. ChatGPT レビュー依頼文（即コピペ可能な形）

Frontmatter: `date / task / runId / targetApp / monetizationImpact / theme / relatedRunIds / commitHashes`

任意フィールド: `reviewFileCommit`（本レビュー Markdown 自身を push した commit hash。後追いで埋めるか空欄でよい）

### commitHashes の意味（2026-05-14 明確化）

`commitHashes` は**レビュー対象の作業 commit / 関連 commit**を記録する欄として運用する。

- **記録する**: レビュー対象の作業 commit / 派生・先行・関連 commit / 修正対象の旧 commit
- **記録対象外（必須にしない）**: レビュー Markdown 自身を push した commit hash（自己参照問題のため。`git log --follow` で参照可能）
- **理由**: 自身の push commit hash を frontmatter に書くには「2 度コミット」「amend / force-push」「placeholder の後追い書き換え」などが必要になり、運用が複雑化する
- **既存レビューへの遡及適用**: 既存 push 済みの commitHashes は変更しない。新規生成分から本ルールを適用
- **自己参照 commit を残したい場合**: 任意フィールド `reviewFileCommit:` を使う（未記入可）

---

## 使い方

1. `90_templates/session-review-template.md` をコピー
2. `20_reviews/YYYY-MM-DD_<slug>.md` として保存
3. 8 セクションを埋める
4. Frontmatter の `runId` は progress POST 後の値
5. **必ず同時に [[_review_queue]] の「未レビュー」へ 1 件追加**（後述）
6. ChatGPT 依頼文セクションをそのまま ChatGPT に貼って外部レビューを得る
7. 戻ってきた指摘を `04_reviews/` 側に要約として昇格させてもよい

---

## レビューキュー運用（_review_queue.md・2026-05-14 追加）

`_review_queue.md` を**レビュー状態の正本**として運用する。iPhone Obsidian でチェックボックスを切り替えるだけでレビュー進捗を管理できる。

**ChatGPT 側の想定運用**（GitHub 経由）:

1. `kaeru07/vault` を見る
2. `20_reviews/_review_queue.md` を読む
3. `[ ]` 未チェック項目をレビュー対象候補にする
4. リンク先 `20_reviews/YYYY-MM-DD_<slug>.md` を読み、いつもの観点でレビュー

ユーザーから ChatGPT への依頼例:

> 「レビューキュー見て、未チェックのものをいつもの観点でレビューして」

**Claude / Codex 側の運用**: 新規 `20_reviews/YYYY-MM-DD_<slug>.md` を生成したら、**必ず同時に** `_review_queue.md` の「未レビュー」に 1 件追加する。形式は `_review_queue.md` 末尾の「今後の運用ルール」セクション参照。

**追加項目に `createdAt` 必須（2026-05-14 追加）**: 各キュー項目の 1 行目（リンク直下）に `createdAt: YYYY-MM-DD HH:mm` を必ず入れる。値はレビュー Markdown 生成時刻（JST）。runId のタイムスタンプ部や対象作業の commit 時刻から逆算してもよい。`reviewedAt` は導入しない（`[x]` をレビュー済み判定の正本とする方針を維持）。

**レビュー済みマーク**: iPhone Obsidian で `- [ ]` → `- [x]` に変更する。要対応は `result: needs_followup` を書く。

---

## スキップ可（生成不要）

- 単純な事実確認・1 行質問・了解応答
- ファイル変更ゼロ かつ 設計判断ゼロ の応答
- ユーザーが「レビューファイル不要」と明示

---

## 関連

- [[../90_templates/session-review-template]]
- [[../03_prompts/Claude-Code標準運用]]
- [[../04_reviews/Claude作業レビュー運用]]
- [[../00_index]]
