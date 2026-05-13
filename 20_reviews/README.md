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
