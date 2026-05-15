---
date: 2026-05-15
task: progress に Codex 連携の最小実験機能を追加
runId: 20260515-221729
targetApp: progress
monetizationImpact: low
theme: [workflow, prompt-template]
relatedRunIds: []
commitHashes: []
---

# 2026-05-15 progress に Codex 連携の最小実験機能を追加

---

## 1. 作業目的

- なぜこの作業をするのか: progress の ToDo / Queue から Codex を安全に呼び出す実験を行うため
- 背景: SDK 直組み込みではなく、VPS 上の codex CLI (ChatGPT ログイン済み) をサーバー側から呼ぶ方式で最小検証したい
- 期待効果: Queue の taskPrompt をそのまま Codex に渡し、結果をレビュー対象として蓄積できる土台を作る

---

## 2. 実施内容

- 主な変更:
  - `lib/codex-runner.ts`: codex exec を spawn (read-only サンドボックス固定 / timeout / 同時実行ガード / status 確認)
  - `lib/codex-run-storage.ts`: `PROGRESS_DATA_PATH/codex-runs.json` への追記保存
  - `types/codex-run.ts`: CodexRun / CodexStatus 型
  - API: `GET /api/codex/status`, `GET|POST /api/codex/runs`, `GET /api/codex/runs/[runId]`
  - UI: 専用 `/codex` 画面 (status カード + 手動フォーム + 履歴一覧)、QueueCard に確認ダイアログ付き「Codexで試す」ボタン、TopNav に Codex 追加
- 関連調査・判断:
  - codex CLI は `/usr/bin/codex` (v0.128.0)、`codex login status` は ChatGPT ログイン済み
  - `codex login status` は exit 0 で結果を **stderr** に出すため、stdout+stderr 両方で `Logged in` 判定するよう修正
  - codex exec はプロンプトを引数渡し (stdin は ignore)、`-C` で作業ディレクトリ指定、`--skip-git-repo-check`

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| types/codex-run.ts | 新規 (CodexRun / CodexStatus) |
| lib/codex-runner.ts | 新規 (spawn + timeout + 同時実行ガード + checkCodexStatus) |
| lib/codex-run-storage.ts | 新規 (codex-runs.json 読み書き) |
| app/api/codex/status/route.ts | 新規 (GET) |
| app/api/codex/runs/route.ts | 新規 (GET一覧 / POST実行) |
| app/api/codex/runs/[runId]/route.ts | 新規 (GET詳細) |
| app/codex/page.tsx | 新規 (専用画面) |
| components/codex/CodexTrigger.tsx | 新規 (確認ダイアログ付き実行ボタン) |
| components/codex/CodexStatusCard.tsx | 新規 |
| components/codex/CodexRunList.tsx | 新規 |
| components/codex/CodexManualForm.tsx | 新規 |
| components/queue/QueueCard.tsx | Codexで試すボタン追加 + import |
| components/navigation/TopNav.tsx | /codex ナビ項目追加 |

---

## 4. 検証結果

- typecheck: OK (build 時型検証通過。ChildProcess の stdin/stdout null 型を修正)
- build: OK (next build / `/codex` と `/api/codex/*` 認識)
- lint: n/a (build の型検証で代替)
- 手動確認 (本番3010と別ポート3034で実施・終了後停止):
  - GET /api/codex/status → `{ok:true, login:logged_in, version:codex-cli 0.128.0}`
  - 空 prompt → 400 / codex 不可時 → 503 + reason / 実行中 → 409
  - POST /api/codex/runs (pong) → status=completed, exit=0, 7846ms, sandbox=read-only
  - codex-runs.json に1件保存 (stdout/stderr/exitCode/時刻/対象ToDo)
  - GET 一覧・詳細 200 / 不存在 runId → 404 / `/codex` ページ → 200
- 機密パターン事前チェック: OK (API キー・.env 追加なし。ルール例示のみ)
- ob sync: 後述 (この応答内で実行)
- git push: progress アプリは company 配下別リポ運用のため対象外 (n/a)。obsidian-vault は後述

---

## 5. 未対応

- スキップした項目:
  - 本番3010 progress の再起動 (pm2 restart はユーザー判断ルール)
  - Tasks 詳細への Codex ボタン展開 (今回は Queue のみ)
- 環境制約で実行できなかったこと:
  - 本番反映 (再起動が必要なため新機能はまだ 3010 で見えない)
- ユーザー判断待ち:
  - 本番 progress を pm2 restart して `/codex` を有効化するか

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低 (新規ファイル中心。既存変更は QueueCard への import+ボタン1ブロックと TopNav 1行のみ)
- DB / 認証 / 本番 / 機密: いずれも触れていない。API キー・.env 変更なし。read-only サンドボックス固定で destructive を構造的に抑止
- ロールバック手段: 追加ファイル削除 + QueueCard / TopNav の差分 revert
- 観察すべき項目:
  - codex exec の実行時間と timeout (default 120s) の妥当性
  - codex-runs.json の肥大化 (stdout/stderr 各 256KB 上限はあるが件数無制限)
  - 同時実行ガードがプロセス単位メモリのため複数インスタンス時は無効 (現状単一プロセス前提)

---

## 7. 次にやるべきこと

- フォローアップ作業:
  - 本番3010 の再起動可否をユーザー確認
  - codex run → ExecutionRun への変換連携
  - workspace-write サンドボックス + 差分レビューの段階導入
  - 長時間実行のストリーミング表示
- ユーザー判断待ち事項: pm2 restart, workspace-write 解禁範囲
- 観察項目: codex-runs.json サイズ、実行時間分布

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress (/root/company/apps/ny01/progress)
作業: Codex 連携の最小実験機能の追加
runId: 20260515-221729
日付: 2026-05-15
GitHub commit: (progress は別リポ運用 / obsidian-vault 側 commit は別途)

## 作業目的
- progress の ToDo/Queue から VPS の codex CLI を安全に手動1件ずつ呼ぶ実験
- SDK 直組み込みではなく codex exec を spawn する方式

## 実施内容
- lib: codex-runner (spawn + timeout + 同時実行ガード + status確認), codex-run-storage (codex-runs.json)
- API: GET /api/codex/status, GET|POST /api/codex/runs, GET /api/codex/runs/[runId]
- UI: /codex 専用画面, QueueCard の確認ダイアログ付き「Codexで試す」, TopNav 追加
- read-only サンドボックス固定 / timeout(default120s,max600s) / stdout・stderr 256KB上限 / プロンプト8000字上限

## 検証結果
- npm run build: pass
- GET /api/codex/status: ok:true logged_in (codex-cli 0.128.0)
- POST 1件: status=completed exit=0 7846ms read-only / codex-runs.json 保存
- 一覧・詳細 200 / 不存在 404 / 空prompt 400 / 実行中 409 / codex不可 503
- 検証は本番3010と別ポート3034で実施し停止済 (本番無影響)

## 禁止事項の遵守
- APIキー追加なし / .env変更なし / cron・自動ループなし / 並列禁止 / git push自動なし / 実行前に確認ダイアログ

## 未対応
- 本番3010 再起動 (pm2, ユーザー判断)
- ExecutionRun 自動変換 / workspace-write 段階導入 / ストリーミング表示

## 危険ポイント
- 同時実行ガードはプロセス単位メモリ (複数インスタンス時は無効)
- codex-runs.json は件数無制限
- read-only 固定だが codex exec のサンドボックス信頼度

## 確認したい観点
- spawn まわりの安全性 (引数注入・タイムアウト・kill シーケンス)
- read-only サンドボックスで本当に destructive を防げているか
- codex login status を stderr 参照する判定の堅牢性
- 同時実行ガードの設計 (メモリロックで十分か)
- 次段 (workspace-write / ExecutionRun連携) の優先順位
- 収益化インパクト評価 (low) は妥当か
````

---

## 関連

- progress runId: 20260515-221729 (正本)
- 関連 run: なし
- 関連アプリ: [[../02_apps/progress]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
