# レビューキュー

> ChatGPTレビュー用の入口ファイル。
> iPhone Obsidianでは、このチェック状態をレビュー状態の正本として扱う。

## 未レビュー

- [ ] [[2026-05-17_vault-multiple-todo-review]]
  - createdAt: 2026-05-17 12:43
  - app: company-meta
  - project: workflow / vault-todo
  - priority: high
  - summary: Vault横断の複数ToDo（00_inbox投函箱 / 途中プロジェクト一覧 / progress改善候補 / idle-backlog）を再読み取りし優先順位1本化。high=progress操作盤化2件、medium=連携CLI/ポータルLP/市場調査メモ、投函APIは消化済でclose提案。前回成果物が痕跡ゼロだったため推測せず再算出
  - result: 
- [ ] [[2026-05-16_vault-inbox-token-setup]]
  - createdAt: 2026-05-16 19:36
  - app: progress
  - project: workflow
  - priority: high
  - summary: VAULT_INBOX_TOKEN本番設定+pm2 restart+疎通確認(tokenなし401/ありで保存成功)。token値非露出・正本JSON無変更・再起動でvault-inbox等が本番反映
  - result: 
- [ ] [[2026-05-16_vault-inbox-hardening]]
  - createdAt: 2026-05-16 19:03
  - app: progress
  - project: workflow
  - priority: high
  - summary: vault-inbox API強化(VAULT_ROOT/VAULT_GIT_BRANCH環境変数化・main固定撤廃・投函ログndjson)。隔離検証で秘密非記録/git失敗時保存成功/本物vault非汚染を実証。ny01コードのみpush 7ab08fe
  - result: 
- [ ] [[2026-05-16_vault-inbox-api]]
  - createdAt: 2026-05-16 18:24
  - app: progress
  - project: workflow
  - priority: high
  - summary: POST /api/vault-inbox 追加(Bearer認証→obsidian-vault/00_inboxへMD保存+git push試行)。ny01(PUBLIC)へコードのみpush(338e5f1)・正本JSON無変更実証・検証テストMD掃除
  - result: 
- [ ] [[2026-05-16_chatgpt-inbox-import]]
  - createdAt: 2026-05-16 17:11
  - app: progress
  - project: workflow
  - summary: ChatGPT投函箱→progress取り込み機能を実装 (00_inbox走査→ToDo化でpending_approval追加 / 内容ハッシュ冪等 / 隔離検証済・本番無影響・要再起動)
  - priority: medium
  - result: 
- [ ] [[2026-05-16_service-map]]
  - createdAt: 2026-05-16 11:35
  - app: company-meta
  - project: workflow
  - priority: medium
  - summary: company 全体を7サービスに分割した俯瞰資料 docs/service-map.{html,json} を新規作成 (読み解きコスト削減 / 実装変更なし)
  - result: 
- [ ] [[2026-05-16_codex-sdk-experiment]]
  - createdAt: 2026-05-16 02:01
  - app: progress
  - project: workflow
  - priority: medium
  - summary: Codex SDK 読み取り専用最小実験 (CLI利用可/Python公式SDK無→subprocess方式 / execution-runs抽出→codexレビュー / 本体無影響・書込なし検証済)
  - result: 
- [ ] [[2026-05-16_scrape-lab-v2-postproc]]
  - createdAt: 2026-05-16 01:50
  - app: scrape-lab-v2
  - project: app-strategy
  - priority: medium
  - summary: scrape-lab-v2 v1 後処理 (private GitHub管理化 commit 2e3e761 / 20_reviews追記 / 02_apps新設 / v2候補整理)。新機能なし
  - result: 
- [ ] [[2026-05-15_progress-codex-experiment]]
  - createdAt: 2026-05-15 22:17
  - app: progress
  - project: workflow
  - priority: high
  - summary: progress に Codex CLI 連携の最小実験機能を追加 (read-only / 1件ずつ / 確認ダイアログ / status・実行・一覧API / /codex 画面 / codex-runs.json 保存)
  - result: 
- [ ] [[2026-05-15_scrape-lab-v2-init]]
  - createdAt: 2026-05-15 12:36
  - app: scrape-lab-v2
  - project: app-strategy
  - priority: high
  - summary: scrape-lab-v2 新規作成 v1 (Next.js 14 + Cheerio / SSRF ブロック / 差分検知 / JSON・Markdown export / Claude・Codex レビューコピー欄 / iPhone 対応)
  - result: 
- [ ] [[2026-05-15_idle-work-backlog]]
  - createdAt: 2026-05-15 00:06
  - app: company-meta
  - project: app-strategy
  - priority: high
  - summary: Claude/Codex 余剰リソース消化用 作業バックログ（既存棚卸し / 新規 30 案 / Shorts / Play・Web / ASO / ToDo / ランキング 4 種）
  - result: 
- [ ] [[2026-05-14_local-first-app-ideas]]
  - createdAt: 2026-05-14 23:47
  - app: company-meta
  - project: app-strategy
  - priority: high
  - summary: ローカル完結・高継続 Google Play アプリ案 30 件 + ランキング 4 種 + 本命 3 件（打牌セルフ採点 / 朝散歩カレンダー / 庭タイムラプス）
  - result: 
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
