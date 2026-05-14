# レビューキュー

> ChatGPTレビュー用の入口ファイル。
> iPhone Obsidianでは、このチェック状態をレビュー状態の正本として扱う。

## 未レビュー

- [ ] [[2026-05-14_claude-task-template-update]]
  - createdAt: 2026-05-14 23:21
  - app: company-meta
  - project: obsidian-workflow
  - priority: medium
  - summary: claude-task-template を最新運用へ更新（完了マーカー 7 項目 / Vault 同期チェックリスト / 20_reviews・_review_queue 連動）
  - result: 
- [x] [[2026-05-14_push-required-rule]]
  - createdAt: 2026-05-14 09:09
  - app: company-meta
  - project: obsidian-workflow
  - priority: high
  - summary: commit 後の push 必須化ルール追加 + global git push deny 解除（push 完了 = 同期完了の運用統一）
  - result: 
- [x] [[2026-05-14_add-review-queue-createdat]]
  - createdAt: 2026-05-14 08:13
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: medium
  - summary: _review_queue.md に createdAt フィールド追加（既存 4 件補完 + README / template / CLAUDE.local.md ルール反映）
  - result: 
- [ ] [[2026-05-14_clarify-commit-hashes]]
  - createdAt: 2026-05-14 01:36
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: medium
  - summary: 20_reviews commitHashes 運用整理（意味明確化 + reviewFileCommit 任意フィールド追加・既存遡及なし）
  - result: 
- [x] [[2026-05-14_review-queue-setup]]
  - createdAt: 2026-05-14 01:26
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: medium
  - summary: 20_reviews レビューキュー運用追加（_review_queue.md 新設・運用ルール反映）
  - result: 
- [ ] [[2026-05-14_fix-commit-hashes]]
  - createdAt: 2026-05-14 01:13
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: low
  - summary: 20_reviews 自己参照修正（commitHashes に push commit 72c5f3f 反映）
  - result: 
- [ ] [[2026-05-14_review-file-rule]]
  - createdAt: 2026-05-14 00:56
  - app: company-meta
  - project: obsidian-workflow
  - priority: high
  - summary: 20_reviews 自動生成ルール追加（8 セクション + ChatGPT 依頼文 + 完了マーカー 7 項目化）
  - result: 

## レビュー済み

- [x] [[サンプル]]
  - app: 
  - project: 
  - priority: 
  - summary: 
  - result: ok

## 今後の運用ルール

Claude / Codex / Claude Code が `20_reviews/YYYY-MM-DD_<slug>.md` を生成したら、**必ず同時にこの `_review_queue.md` の「未レビュー」へ 1 件追加**する。

追加形式:

```markdown
- [ ] [[YYYY-MM-DD_<slug>]]
  - createdAt: YYYY-MM-DD HH:mm
  - app: <対象アプリ / company-meta 等>
  - project: <プロジェクト名 / 略称>
  - priority: high | medium | low
  - summary: <1 行要約>
  - result: 
```

`createdAt` はレビュー Markdown を生成した時刻（VPS のローカル時刻 = JST）を `YYYY-MM-DD HH:mm` で記入する。runId のタイムスタンプ部、または対象作業の commit 時刻から逆算してもよい。`reviewedAt` は導入しない（`[x]` の有無をレビュー済み判定の正本とする方針を維持）。

## ChatGPT レビュー後の運用

ユーザーが iPhone Obsidian で対象行を以下のように変更する:

変更前:
```
- [ ] [[YYYY-MM-DD_<slug>]]
```

変更後:
```
- [x] [[YYYY-MM-DD_<slug>]]
```

このチェック状態をレビュー済み判定の**正本**として扱う。各レビュー Markdown の frontmatter `status:` は必須更新にしない。

要対応は `result: needs_followup` と書く。

## ChatGPT 側の想定運用

依頼例（ユーザーから）:

> 「レビューキュー見て、未チェックのものをいつもの観点でレビューして」

ChatGPT 側の処理順:

1. GitHub の `kaeru07/vault` を見る
2. `20_reviews/_review_queue.md` を読む
3. `[ ]` 未チェック項目をレビュー対象候補にする
4. `app` / `project` / `priority` / `summary` を見て対象を絞る
5. リンク先 `20_reviews/YYYY-MM-DD_<slug>.md` を読む
6. いつもの観点でレビューする

## レビュー観点（ChatGPT 共通）

- 抜け漏れ
- 進め方の妥当性
- 危険な変更
- 既存運用との矛盾
- 未承認 ToDo に着手していないか
- pending_approval / approved / queued の混在
- progress.md や正本を壊していないか
- build / TypeScript / lint / 主要画面確認が未実施なのに OK 扱いされていないか
- 追加で ToDo 化すべき問題
- 次に進めるべき作業

## 重要ルール

- `_review_queue.md` のチェック状態をレビュー状態の**正本**にする
- iPhone で軽く運用できることを最優先
- `04_reviews/` は重要レビューの**手動キュレーション**用として維持
- 既存レビュー本文は削除しない
- GitHub 公開前に秘密情報チェックを行う
- `.env` / `token` / `cookie` / API キー / 本番 DB 接続文字列 は絶対に commit しない

## 関連

- [[README]]
- [[../03_prompts/Claude-Code標準運用]]
- [[../90_templates/session-review-template]]
- `/root/company/CLAUDE.local.md` § レビューキュー（_review_queue.md）連動
