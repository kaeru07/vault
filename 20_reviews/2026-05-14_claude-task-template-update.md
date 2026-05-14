---
date: 2026-05-14
task: claude-task-template を最新運用（完了マーカー7項目・Vault同期チェックリスト・20_reviews/レビューキュー連動）へ更新
runId: 20260514-232120
targetApp: company-meta
monetizationImpact: low
theme: [workflow, obsidian, prompt-template]
relatedRunIds: [20260513-204614, 20260513-214413, 20260513-202846]
commitHashes: []
---

# 2026-05-14 claude-task-template を最新運用へ更新

> 1 作業 = 1 ファイル の自動生成レビュー。
> 詳細ルールは `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール。

---

## 1. 作業目的

- なぜこの作業をするのか: Claude Code 標準運用ルールと Obsidian Vault 同期ルールが現行ベースラインで整備済みか点検し、不足分を補うため
- 背景: Phase A 運用テスト以後、POST 範囲拡大・20_reviews 自動生成・レビューキュー連動・完了マーカー 7 項目化など多数のルール改訂があり、テンプレ側が追従できているかが未確認だった
- 期待効果: 依頼テンプレを開いた瞬間に Vault 同期チェックリスト・完了マーカー 7 項目まで埋められる状態にする

---

## 2. 実施内容

- 主な変更:
  - `90_templates/claude-task-template.md` を全面更新
  - 依頼本文に「Vault 編集を伴う場合の追加手順 10 ステップ」を追加（sync-vault 編集 → 機密スキャン → ob sync → rsync ミラー → 再スキャン → commit/push → POST → 20_reviews 生成 → _review_queue.md 追記 → 完了マーカー 7 項目）
  - 最終報告フォーマットを完了マーカー 7 項目 + 5 ブロック構造に統一
  - 末尾に「Vault 同期チェックリスト 10 項目」を新設
  - 関連リンクに [[Vault同期ルール]] / [[GitHub反映ルール]] / [[session-review-template]] を追加
- 関連調査・判断:
  - CLAUDE.local.md 134 行に「## Obsidian Vault更新時の同期ルール」が既に存在 → 追記不要
  - Claude-Code標準運用.md は Phase A 追補・GitHub 反映・20_reviews・レビューキュー・完了マーカー 7 項目まで網羅済み → 更新不要
  - Vault同期ルール.md（既存）はユーザー指示の「Obsidian-Vault同期ルール.md」と実質同等 → 重複作成しない（CLAUDE.md「既存の重要ルールは削除しない / 重複統合」に準拠）

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `obsidian-sync-vault/90_templates/claude-task-template.md` | Vault 同期チェックリスト・完了マーカー 7 項目・20_reviews / _review_queue 連動を反映する全面リライト |

---

## 4. 検証結果

- typecheck: n/a（Markdown のみ）
- build: n/a
- lint: n/a
- 手動確認: grep で更新後テンプレに `ob sync` / `Vault同期` / `完了マーカー 7` が含まれることを確認
- 機密パターン事前チェック: OK（ルール例示の文字列のみ）
- ob sync: Fully synced（device: vps-company / Push: _review_queue.md / New file: claude-task-template.md）
- git push: 本セッション内で obsidian-vault へミラー → commit → push を実施

---

## 5. 未対応

- スキップした項目: Obsidian-Vault同期ルール.md という別名ページの新規作成（重複のためスキップ）
- 環境制約で実行できなかったこと: なし
- ユーザー判断待ち: テンプレ末尾「Vault 同期チェックリスト」を更に小分けする必要があるか（現状 10 項目で運用してみる）

---

## 6. 危険ポイント

- 既存機能への影響リスク: なし（テンプレファイルのみ更新）
- DB / 認証 / 本番 / 機密に触れたか: ノー
- ロールバック手段: `git log -- 90_templates/claude-task-template.md` で旧版に戻せる
- 観察すべき項目: 次に claude-task-template.md を使う依頼で、新フォーマットがそのまま機能するか

---

## 7. 次にやるべきこと

- フォローアップ作業:
  - 次の依頼時に新テンプレを実際に使い、抜けがないか観察
  - 余裕があれば claude-code-light / standard / full の各規模テンプレも同様に最新化を検討
- ユーザー判断待ち事項: 別名「Obsidian-Vault同期ルール.md」を本当に作成すべきか（推奨は不要 / Vault同期ルール.md に集約）
- 観察項目（数日〜数週間）: テンプレを使った依頼で完了マーカー 7 項目が抜けないか
- 関連 ToDo の追加候補: なし

---

## 8. ChatGPT レビュー依頼文

そのままコピペで ChatGPT に流せる形:

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: company-meta
作業: claude-task-template.md を最新運用（完了マーカー7項目・Vault同期チェックリスト・20_reviews/レビューキュー連動）へ更新
runId: 20260514-232120
日付: 2026-05-14
GitHub commit: （本ファイル push 時に確定）

## 作業目的
- Claude Code 標準運用ルールと Obsidian Vault 同期ルールが現行ベースラインで整備済みか点検
- 不足分を補い、依頼テンプレが Phase A 以降の改訂に追従した状態にする

## 実施内容
- 90_templates/claude-task-template.md を全面リライト
- Vault 編集時の追加手順 10 ステップを依頼本文に組み込み
- 最終報告フォーマットを完了マーカー 7 項目 + 5 ブロックに統一
- Vault 同期チェックリスト 10 項目を末尾に新設
- 関連リンクに Vault同期ルール / GitHub反映ルール / session-review-template を追加

## 変更ファイル
- obsidian-sync-vault/90_templates/claude-task-template.md

## 検証結果
- typecheck / build / lint: n/a（Markdown のみ）
- 手動確認: grep で更新後テンプレに ob sync / Vault同期 / 完了マーカー 7 が含まれることを確認
- 機密スキャン: OK（例示文字列のみ）
- ob sync: Fully synced

## 未対応
- 別名「Obsidian-Vault同期ルール.md」は重複のため作成せず（既存 Vault同期ルール.md に集約）

## 危険ポイント
- なし（テンプレファイルのみ更新）

## 次にやるべきこと
- 新テンプレを次の依頼で実運用してみて抜けを観察
- 必要なら claude-code-light / standard / full も同様に最新化

## 確認したい観点
- テンプレに抜けがないか（完了マーカー 7 項目 / Vault 同期チェックリスト / 20_reviews 連動）
- 既存運用ルールと矛盾する箇所がないか
- ユーザー指示にあった「Obsidian-Vault同期ルール.md」の新規作成スキップ判断は妥当か
- 収益化インパクトの評価は妥当か（low）
````

---

## 関連

- progress runId: 20260514-232120（正本）
- 関連 run: 20260513-204614 / 20260513-214413（Phase A 運用テスト）/ 20260513-202846（progress × Obsidian 連携設計レビュー）
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]] / [[../03_prompts/Vault同期ルール]] / [[../03_prompts/GitHub反映ルール]]
- 関連テンプレ: [[../90_templates/claude-task-template]] / [[../90_templates/session-review-template]]
- フォルダ運用: [[../20_reviews/README]]
