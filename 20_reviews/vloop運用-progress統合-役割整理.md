---
title: vloop運用 × Progress 統合 役割整理（Issue #90 / #91 調査結果）
type: integration-policy
issue: kaeru07/vault#90
relatedIssues: [kaeru07/vault#91]
created: 2026-05-30
updated: 2026-05-30 (vloop #91 境界追記)
status: active
tags: [vloop, progress, automation, 役割整理, 正本]
---

# vloop運用 × Progress 統合 役割整理

> Issue #90「Progressアプリでvloop運用・Vault・GitHub Issue・Queue状況を一元確認できるようにする」の調査結果と統合方針。
> Epic #91「AI工場オペレーションセンター」の前段（自動実行管理の土台）。

> [!note] このページの位置づけ
> 実装は Progress アプリ（`apps/ny01/progress` / ポート 3010）の `/operations` ページに反映済み（commit `6291e14` / push 済み）。本ページは「どのデータが何の正本か」を文章で固定する役割整理の正本。

---

## 1. 調査した既存運用（分散の現状）

vloop 運用に関わるデータは、現状 4 系統に分散している。

| 系統 | 実体 | 役割（調査結果） |
|---|---|---|
| **Vault Markdown** | `20_reviews/案件別ToDo一覧.md` | **ToDo の正本・台帳**（A 実行 / B 確認待ち / C 承認待ち / D 完了移動候補） |
| | `20_reviews/vloop_queue.md` | **今回/次回の vloop 実行対象**（`[x]` 済み + ChatGPT 追加 Issue のみ実行） |
| | `20_reviews/完了ToDoログ.md` | 完了 ToDo の履歴 |
| **GitHub Issue** | `kaeru07/vault` の Issue | ChatGPT から ToDo を追加する入口 / 内部作業チケット / `review:` ラベルでレビュー状態可視化 |
| **Progress queue** | `data/real/work-queue.json`（`items[]`） | 集中作業モード（`/focus`）用の自動生成キュー。`work-candidates.json` から生成 |
| | `data/real/project-tasks.json` | 案件別タスク（assignee: claude/user/both） |
| **ExecutionRun** | `data/real/execution-runs.json` | **作業履歴の正本**（CLAUDE.local.md / 全作業 POST 先） |

要点: 「vloop の ToDo 正本」と「集中作業モードの queue」は別物。前者は Vault Markdown（人手キュレーション + 承認ゲート）、後者は Progress `work-queue.json`（candidate からの自動生成）。**両者を機械的に統合すると承認ゲートが壊れる**。

---

## 2. 採用した統合方針（5つの判断）

Issue #90 が「判断してほしいこと」とした 5 点への結論。

### Q1. vloop の正本はどこか
**A. `20_reviews/案件別ToDo一覧.md`（Vault Markdown）が正本。** vloop が「次に実行する対象」は `vloop_queue.md` の `[x]` 項目 + ChatGPT 追加 Issue のみ。Progress `work-queue.json` は vloop の正本ではなく、`/focus`（集中作業モード）用の別キュー。

### Q2. work-queue と vloop_queue.md は統合 / 同期 / ビューのどれにすべきか
**A. 統合・同期しない。Progress 側は「読み取り専用ビュー」に留める。**
理由: `vloop_queue.md` は承認ゲート（`[x]` のみ実行）を持つ人手キュレーション正本。`work-queue.json` は candidate からの自動生成。双方向同期すると未承認 candidate が vloop 対象に混入するリスクがある（#90 禁止事項「未承認 ToDo を実行対象にしない」に抵触）。`/operations` は両者を read-only で並べて表示するだけにする。

### Q3. GitHub Issue は入口として使うか / Progress に取り込むか
**A. Issue は「入口」として維持。Progress には取り込まず参照に留める。**
ChatGPT からの ToDo 追加・`review:` ラベル運用は GitHub Issue が担う（#65 で確立）。Progress に Issue 状態をミラーするのは将来フェーズ（read-only API 候補）。現状は `/operations` から Issue 一覧へリンクで辿れれば足りる。

### Q4. 完了ログは ExecutionRun から生成できるか
**A. 部分的に可能。ただし `完了ToDoログ.md`（Vault）を置き換えない。**
ExecutionRun（`execution-runs.json`）は全作業の正本なので「いつ何を実行したか」は生成可能。一方 `完了ToDoログ.md` は「どの ToDo（案件別台帳の項目）が完了したか」の人手対応付け台帳で、粒度が異なる。ExecutionRun → 完了サマリーの自動生成は将来フェーズ。現状は手動運用を維持。

### Q5. VPS 起動時・毎朝 11:00 起動でどのデータを読むべきか
**A. 起動時に読む入口の優先順位（自動再開の前提）:**
1. `20_reviews/vloop_queue.md` の `[x]` 実行対象（承認済みのみ）
2. `kaeru07/vault` の未反映 Issue（ChatGPT 追加分）
3. Progress `data/real/approvals.json` の pending（人間判断待ちが無いか）
4. Progress `data/real/operational-decisions.ndjson`（前回までの承認結果 = Decision Log を継続反映）

承認待ち（pending approval）が 1 件でもあれば、その Epic は自動実行を止めて承認を待つ（#91 継続実行ポリシー）。

---

## 3. 追加・変更した実装（Progress 側）

commit `6291e14`（`kaeru07/ny01`）。

| ファイル | 内容 |
|---|---|
| `progress/app/operations/page.tsx` | `/operations` 一元確認画面（health / autoexec / approvals / epics を read-only 集約 + autoexec の start/stop/restart 操作） |
| `progress/app/api/operations/health/route.ts` | runnable / running / pendingApproval / stopped / epicsActive / stale の集計（work-queue.json + app-progress.json + approvals + epics から算出） |
| `progress/app/api/operations/autoexec/route.ts` | pm2 プロセス（`VLOOP_PM2_NAME` or 名前に vloop を含むもの）の状態取得 + start/stop/restart |
| `progress/app/api/operations/approvals/route.ts` | pending 承認の取得 + 決定（決定時に operational-decisions.ndjson へ追記 = Decision Log） |
| `progress/app/api/operations/epics/route.ts` | Epic 一覧取得 |
| `progress/lib/operations-store.ts` | epics / approvals / decisions / health の永続ロジック |
| `progress/lib/store.ts` | data/real 配下 JSON/NDJSON の汎用 read/write/append ヘルパー（新規） |
| `progress/lib/types/operations.ts` | Epic / Approval / OperationalDecision / HealthSummary 型 |
| `progress/app/ai-drive/page.tsx` ほか | 自走フロー可視化モック（/ai-drive） |
| `progress/data/real/epics.json` | epic-91 シード |

---

## 4. 自動起動を次に足す場合の設計メモ（#91 へ引き継ぎ）

- 起動トリガ（VPS 起動時 / 毎朝 11:00 / Claude 上限回復後）は **別レイヤ**（cron or pm2）で実装し、`/operations` の autoexec API を叩く形にする。
- 自動実行プロセス本体（`VLOOP_PM2_NAME` で解決される pm2 プロセス）は**未作成**。現状 autoexec API は対象プロセスが無ければ `not_found` を返すだけで安全。
- Decision Log（`operational-decisions.ndjson`）を Claude Code が起動時に読み込む導線は**未実装**。承認結果を次回実行へ引き継ぐ #91「4. Decision Log」要件は次フェーズ。
- 承認カード（`approvals.json`）への投入元（誰が approval を作るか）も未実装。現状は空。

---

## 5. 検証結果

- `npx tsc --noEmit` → エラー 0（既存実装の `./store` / `./types` 欠落 import を解消）
- `npm run build` → 成功（`/operations` `/ai-drive` `/api/operations/*` 全てビルド）
- 機密スキャン（operations 機能ファイル）→ API キー / トークン / `.env` / 秘密情報なし
- commit `6291e14` → `kaeru07/ny01` main へ push 済み

---

## 6. 未対応点

- 自動起動（cron / pm2 トリガ）は未実装（#90 方針通り、本フェーズ対象外）
- approval の投入元・Decision Log 読み戻し導線は未実装（#91 次フェーズ）
- `ny01` リポジトリには news-app（Research OS）/ ai-trend-sns の**未コミット変更が併存**。news-app 分はレビューキュー上「GitHubミラー未push」（ChatGPT レビュー待ちで意図的に未 push）のため、本 commit には**含めていない**。
- 大量のランタイムデータ（`execution-runs.json` 等）も本 commit に含めていない（運用データのため別管理）。

---

## 7. 関連

- Issue #90 / #91
- [[2026-05-30_issue-91-ops-center-next-phase]]
- [[vloop_queue]] / [[案件別ToDo一覧]] / [[完了ToDoログ]]
- [[Issue完了判定ルール]] / [[vloop停止条件監査ルール]]
- [[../03_prompts/claude-commands/logs/vloop_2026-05-30_HHMM]]（本サイクルのログ）

---

## 8. #91 次フェーズの実行境界（2026-05-30 vloop追記）

Issue #91 の残機能は、次の 2 つに分けて扱う。

| 区分 | 内容 | vloop 扱い |
|---|---|---|
| 承認不要 | `/operations` の読み取り専用改善、approval / Decision Log のローカルスキーマ整備、Decision Log 読み戻し導線、ExecutionRun から新 ToDo 候補を下書き化 | 実行可。ただし未承認 ToDo を `[x]` 化しない |
| 承認待ち | VPS 起動時自動再開、毎朝 11:00 起動、Claude 上限回復後再開、pm2 / cron / systemd 実操作、外部公開、認証情報・課金設定利用 | 実行しない。`[ ]` 承認待ちに残す |

詳細な境界は [[2026-05-30_issue-91-ops-center-next-phase]] を正本とする。
