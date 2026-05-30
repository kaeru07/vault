---
title: Issue #91 AI工場オペレーションセンター 次フェーズ実行境界
type: review-note
issue: kaeru07/vault#91
relatedIssues: [kaeru07/vault#90]
created: 2026-05-30
status: active
tags: [vloop, progress, automation, approval, operations]
---

# Issue #91 AI工場オペレーションセンター 次フェーズ実行境界

> Issue #91「Epic: AI工場オペレーションセンター（自動実行・承認・継続実行基盤）」のうち、承認不要で進める範囲と、承認待ちに残す範囲を固定する。
> 前段 #90 は [[vloop運用-progress統合-役割整理]] により完了済み。

## 1. 前段 #90 の到達点

| 項目 | 状態 | 根拠 |
|---|---|---|
| vloop 運用の統合可視化 | done | Progress `/operations` に health / autoexec / approvals / epics を集約 |
| 正本整理 | done | `案件別ToDo一覧.md` = ToDo 正本、`vloop_queue.md` = vloop 実行対象、Progress `work-queue.json` = focus 用別キュー |
| 承認ゲート保護 | done | vloop_queue と work-queue は統合・同期せず read-only ビュー |
| 自動起動入口 | design_done | vloop_queue → 未反映 Issue → pending 承認 → Decision Log の順に読む方針 |
| build / typecheck | done | ny01 commit `6291e14` で `npx tsc --noEmit` / `npm run build` 成功 |

## 2. #91 本文から見た残機能

| 機能 | 現在地 | 今回の扱い |
|---|---|---|
| 1. vloop 運用の統合可視化 | #90 で実装済み | done |
| 2. 自動実行管理ページ | `/operations` 基礎あり | 実プロセス起動は承認待ち |
| 3. 承認待ち管理 | `approvals.json` / API 基礎あり | 投入元は未実装 |
| 4. Decision Log | `operational-decisions.ndjson` 追記基礎あり | Claude Code 読み戻し導線は未実装 |
| 5. 継続実行ポリシー | 方針あり | 停止条件と自律判断境界を本ページで固定 |

## 3. 承認不要で進めてよい範囲

次は、破壊的変更・外部公開・課金・秘密情報操作・pm2/cron 実操作を伴わない限り、Claude / vloop が自律実行してよい。

1. `/operations` に表示する Epic / Approval / Decision Log の読み取り専用改善。
2. `approvals.json` に投入するためのローカルスキーマ・型・UI の整備。
3. `operational-decisions.ndjson` を次回実行の文脈として読むための設計と、ローカルファイル読み取りコード。
4. ExecutionRun から「新 ToDo 候補」を作る下書き生成。ただし自動で `[x]` 実行対象にしない。
5. Vault 正本への反映。承認が必要なものは必ず `[ ]` のまま置く。

## 4. 承認待ちに残す範囲

以下は `[ ]` 承認待ち扱い。今回の vloop では実装・実操作しない。

- [ ] VPS 起動時に vloop / autoexec を自動再開する。
- [ ] 毎朝 11:00 に vloop / autoexec を自動起動する。
- [ ] Claude 上限回復後の自動再開を実装する。
- [ ] pm2 プロセスを新規作成・再起動・常駐化する。
- [ ] cron / systemd / 外部スケジューラを設定する。
- [ ] GitHub / Vercel / 外部サービスへ自動公開する。
- [ ] 認証情報、API キー、トークン、課金設定を扱う。

## 5. 継続実行ポリシー

Issue #91 本文の方針を、vloop 実行時の判定に落とす。

| 判断 | 扱い |
|---|---|
| Epic ゴール達成に影響しない実装詳細 | autonomous（Claude が自律判断） |
| UI 文言、読み取り専用表示、型整備、ローカルログ整形 | autonomous |
| Goal 変更、収益化方針変更、課金、破壊的変更、本番破壊リスク | approval_required |
| 複数案で結果が大きく変わる設計判断 | approval_required |
| candidate / pending_approval の approved 化 | approval_required（AI 判断禁止） |

## 6. 次に実装するなら

承認不要で次に進める最小単位は「Decision Log 読み戻し導線」。

- 入力: `data/real/operational-decisions.ndjson`
- 出力: Claude Code / vloop 起動時に読む短いサマリー
- 禁止: 自動実行開始、pm2/cron 操作、未承認 approval の自動決定
- 完了条件: 読み戻し結果が `/operations` またはローカルログで確認でき、承認待ちがある Epic は停止扱いになる

## 7. 状態分類

| Issue | 状態 | レビュー状態 | 理由 |
|---|---|---|---|
| #90 | user_check | reviewed_followup | 前段実装と役割整理は完了。close はユーザー判断。 |
| #91 | open | reviewed_followup | 管制塔の土台は前進。自動起動、approval 投入元、Decision Log 読み戻しが残る。 |
