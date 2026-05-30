---
title: AI工場自動化 Phase2（Approval / Decision / pending_approval）
type: design-note
issue: kaeru07/vault#91
relatedIssues: [kaeru07/vault#90]
created: 2026-05-30
status: active
tags: [vloop, progress, automation, approval, decision-log, execution-run, handoff]
---

# AI工場自動化 Phase2（Approval / Decision / pending_approval）

> Claude→Codex自動切替は後回し。
> 今回は Approval Queue 自動生成、Decision Log 読み戻し、ExecutionRun nextActions → pending_approval 生成を優先する。

## 1. Approval Queue設計

正本は Progress の `data/real/approvals.json`。
新しい正本は作らない。

```json
{
  "approvalId": "appr-...",
  "epicId": "epic-91",
  "title": "認証方式を選ぶ",
  "priority": "critical | high | normal | low",
  "category": "goal_change | billing | destructive | production_risk | secret | external_publish | monetization | multi_option | executor_fallback",
  "options": [
    { "key": "A", "label": "JWT", "detail": "最小構成" },
    { "key": "B", "label": "Clerk", "detail": "外部SaaS連携" },
    { "key": "C", "label": "NextAuth", "detail": "Next.js標準寄り" }
  ],
  "recommended": "A",
  "reason": "最小構成で、現時点の自動化基盤に対して過剰依存を増やさないため",
  "status": "pending"
}
```

## 2. Approval Queue自動生成案

実装API:

- `POST /api/operations/approvals/generate`

役割:

- executor が判断不能になったときに `approvals.json` へ pending approval を作る
- A/B/C 選択、推奨案、理由を必ず持つ
- モバイルでは既存 `/operations` の承認カードで1タップ選択する

禁止:

- approval を作っただけで実行対象にしない
- candidate / pending_approval を AI 判断で approved にしない
- 課金、外部公開、秘密情報利用を自動実行しない

## 3. Decision Log読み戻し設計

保存場所:

- `data/real/operational-decisions.ndjson`

読込タイミング:

1. vloop開始時
2. executor開始時
3. handoff生成時
4. Approval Queueを作る前

executorへの注入方法:

- 実装API: `GET /api/operations/decisions/context`
- 集中作業プロンプト（`components/queue/PromptCopy.tsx`）がこのAPIを読み、promptへ注入する
- Codex実行プロンプトへも同じ `promptBlock` を流用できる

## 4. pending_approval生成設計

正本:

- 入力正本: `execution-runs.json`
- 出力正本: `project-tasks.json`

実装API:

- `GET /api/operations/next-actions?limit=N` — ExecutionRun `nextActions` 候補を返す
- `POST /api/operations/next-actions` — 候補を `project-tasks.json` の `pending_approval` ToDoへ変換する

生成ルール:

- `status: pending_approval`
- `sourceType: execution_review`
- `sourceRunId` を保持
- `preferredExecutor` / `fallbackExecutor` / `canRunOnCodex` を任意フィールドで保持
- 未承認で `queued` にしない
- 重複判定は `sourceRunId + title`

今回の検証:

- `ExecutionRun 20260530-174639` の `nextActions` から `pending_approval` 3件を生成
- 再実行時は `created: 0 / skipped: 3` になり冪等性を確認

## 5. handoffの位置付け整理

handoffは独立した正本にしない。

位置付け:

- `ExecutionRun`
- `Decision Log`
- `Next Actions`
- `Approval Queue`

から生成される引き継ぎビュー。

実装API:

- `GET /api/operations/handoff`

Progress表示:

- `/operations` では handoff の正本ファイルではなく、生成元と不足状況を見る
- handoff本文はAPIから生成できる

## 6. 正本構造の整理

| 項目 | 正本 | 備考 |
|---|---|---|
| vloop ToDo台帳 | Vault `案件別ToDo一覧.md` | 人間向け正本 |
| vloop実行対象 | Vault `vloop_queue.md` | `[x]` のみ |
| Progress作業キュー | `work-queue.json` | `queued` / `in_progress` |
| 案件別タスク | `project-tasks.json` | `pending_approval` 生成先 |
| 作業履歴 | `execution-runs.json` | nextActions 入力元 |
| 承認待ち | `approvals.json` | A/B/C 選択 |
| 判断履歴 | `operational-decisions.ndjson` | 次回executorへ注入 |
| handoff | 生成ビュー | 独立正本にしない |

## 7. MVP実装範囲

Progress:

- Approval Queue生成API
- Decision Log context API
- ExecutionRun nextActions取得API
- nextActions → pending_approval 生成API
- handoff生成ビューAPI
- Decision Logを集中作業プロンプトへ注入
- `/operations` から AI次ToDoを承認待ち化するボタン

Vault:

- 本設計メモ
- vloop_queue / 案件別ToDo一覧 / review_queue への反映

## 8. 後回し範囲

- Claude→Codex自動切替
- systemd / cron / pm2 自動起動
- Approval Queueの高度な自動分類
- pending_approvalからqueuedへの自動昇格
- handoffファイル分割
- Factory成果物台帳
- 収益化機能
- 市場調査連携

## 9. 検証結果

- `git pull origin main`（ny01 / vault）: Already up to date
- `npm run lint`: 成功
- `npx tsc --noEmit`: 成功
- `npm run build`: 成功
- `GET /api/operations/decisions/context`: 200 OK
- `GET /api/operations/handoff`: 200 OK
- `GET /api/operations/next-actions?limit=3`: 200 OK
- `POST /api/operations/next-actions`: 200 OK
- `HEAD /operations`: 200 OK（build済み `next start` / port 3013）

## 10. 未対応点

- Approval Queue生成APIは実装済みだが、架空判断を残さないため実データPOST検証は未実施
- Decision Logが空の場合のUI表現は最小
- pending_approval生成は実施済みだが、runtimeデータは既存未コミット変更が多いためコードcommitから除外
- Claude→Codex自動切替は未実装

## 11. 次の一手

1. executor が blocked になった時点で `POST /api/operations/approvals/generate` を呼ぶ導線を入れる。
2. `pending_approval` ToDoをモバイルで approve / hold / reject しやすくする。
3. Decision Log context を Codex 実行プロンプトにも注入する。
4. Claude→Codex自動切替は、上記3点が安定してから実装する。

