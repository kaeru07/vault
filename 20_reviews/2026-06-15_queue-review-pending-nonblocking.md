---
date: 2026-06-15
task: 自動実行キューの「レビュー待ちで止まる」判定を見直し（レビュー待ちは止めない）
runId: 20260615-204710
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260614-175356, 20260613-125025]
commitHashes: [fbc3e5a]
---

# 2026-06-15 自動実行キュー「レビュー待ちで止まる」判定の見直し

> Codex 実装 → Fable レビュー（整合性バグ修正含む）。

## 背景 / 目的
ただのレビュー待ち（not_reviewed）だけで自動実行候補から外れ、工場が止まって見える問題を是正。止めるのは「危険」と「ユーザー判断要」だけにする。

## 判定ロジック変更（`deriveWorkItemStatus`）
優先順:
1. done/merged → `done`
2. decisionPolicy=manual / factoryEligible=false → `manual`
3. **危険**（riskFlags: billing/production_db/auth_secret/external_publish/destructive/migration）/ 最新Run failed / blocker / status=blocked → `blocked`
4. **ユーザー判断要**（pendingApproval / approval_required / needs_human）→ `waiting_user`
5. hold/paused → `ai_hold`
6. factoryEligible=true → `executable`（それ以外 → `manual`）

- 最新Run `not_reviewed`/`copied`（非failed）→ status は止めず、`AutoQueueItem.reviewPending=true`「レビュー未確認あり（自動実行は継続）」。
- `needs_followup` / fixPrompt あり → `fixRequested=true` ＋ queueScore **+700** boost（優先修正）。
- 従来の「P0/P1の未レビュー→waiting_user」エスカレーションと「レビュー待ちのため次回候補に入りません」表記は**廃止**。`review_waiting` は not_reviewed からは生成しない（enum 互換残置）。

## Fable レビューで見つけ修正した整合性バグ
Codex初版は `reviewPending`/`fixRequested` を **factoryEligible を無視して `executable` 返す**実装だったため、対象外Epic（factoryEligible未設定の epic-91）が `status=executable` になりつつ候補（`factoryEligible===true`）から漏れ、**全リストから消えて counts だけ executable=1・next=None** の不整合に。→ `factoryEligible===true → executable / else manual` に従わせ、flag は `toEpicItem` で status==='executable' のときのみ付与する形へ修正。

## 変更ファイル
lib/auto-queue-score.ts / lib/auto-queue.ts / types/auto-queue.ts / app/queue/page.tsx / app/page.tsx / docs/operations/current-operating-model.md

## UI
- /queue・司令塔トップ: 「要修正優先（fixPrompt）」「レビュー未確認・継続」バッジ。候補外理由は危険/判断要/対象外のみ（レビュー待ち表記なし）。

## 検証
- tsc 0 / lint 0 / build 成功。
- 実データ: `review_waiting:1→0`。対象外 epic-91（factoryEligible未設定）は `manual` で可視（「手動または対象外のため候補外」）・候補可否整合（counts と next の不整合解消）。
- 安全: 危険（blocked）/ 判断要（waiting_user）の停止は維持（ゲートが not_reviewed 判定より前）。

## 残課題
- factoryEligible=true かつ not_reviewed の Epic 発生時に executable＋reviewPending 継続、needs_followup で fixRequested boost 上位、の実挙動を確認（現状そのデータが無い）。
- 重要レビューで止めたい場合はレビュー側を needs_human / approval に昇格させる運用の周知。
- iPhone実機でバッジ表示確認。

## ChatGPT レビュー依頼文
```
対象: progress 自動実行キュー レビュー待ち判定見直し（runId 20260615-204710 / commit fbc3e5a）
変更: lib/auto-queue-score.ts, lib/auto-queue.ts, types/auto-queue.ts, app/queue/page.tsx, app/page.tsx, operating-model
背景: ただのレビュー待ち(not_reviewed)で工場が止まる→止めない判定へ。停止は危険/判断要のみ。修正依頼は優先boost。
観点:
1. 「未レビューでも自動実行を継続」する方針の是非（前作業未確認のまま次が走るリスク許容度）。
2. 危険判定(hasDangerRisk)に deploy を含めない(=blockでなくcaution)現行方針の妥当性。
3. fixRequested +700 boost の優先度バランス(pin>fixRequested>P0)。
4. status と factoryEligible 候補可否の整合(対象外Epicの扱い)。
```
