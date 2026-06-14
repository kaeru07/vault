---
date: 2026-06-14
task: Prompt Queue（作業プロンプト貯蔵庫 /prompt-queue）新設・入力簡素化
runId: 20260614-092943
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow, prompt-template]
relatedRunIds: []
commitHashes: [dd560a7]
---

# 2026-06-14 Prompt Queue（作業プロンプト貯蔵庫）新設

> Codex 実装 → Fable レビュー（reviewer）・e2e検証・コミット。

## 1. 作業目的
やってほしい作業（プロンプト）を最小入力で貯め、未完了を「次回やる候補」として出す貯蔵庫を新設。GoalとGoal進捗を分けず、実行AI・優先度はユーザーに選ばせない方針。

## 2. 実装（新規）
- 入力簡素化: **タスク名 / プロンプト / Project / Goal進捗** の4項目のみ。任意=メモ/関連URL/関連レビューID/関連Inbox ID。
- **実行対象AI（codex/claude/fable_review/auto）・優先度（P0/P1/P2）は UI に出さない**（内部既定 auto / 未指定のみ）。
- Goal と Goal進捗を分けず単一 `goalProgress`（既存 Goal を参照）。
- 正本 `data/real/prompt-queue.json`（物理削除なし・DELETE→`archived`）。
- API: `GET/POST /api/prompt-queue`、`PATCH/DELETE /api/prompt-queue/[id]`、`POST /api/prompt-queue/import`。
- JSON一括取り込み: `{promptQueue:[...]}` ＋ 旧互換 `{todos:[...]}`。`goal`→`goalProgress` 正規化、status未指定→`queued`、`priority`/`assignee`/`preferredExecutor` 無視（警告）、title/prompt 無しエラー、project 不一致は未紐付け警告。
- 次回やる候補: 未完了（queued/reserved/not_started/failed/needs_retry/needs_user_prompt_fix）を Project/Goal進捗 状態順で「なぜ候補か」付き表示、completed/canceled/archived/snoozed 除外。
- Queue画面 `/prompt-queue`、ナビ（Legacy）「作業予約」。

## 3. 変更/作成ファイル
types/prompt-queue.ts / lib/prompt-queue.ts / app/api/prompt-queue/{route,[id]/route,import/route}.ts / app/prompt-queue/page.tsx / components/prompt-queue/PromptQueueBoard.tsx / data/real/prompt-queue.json / components/navigation/TopNav.tsx / docs/operations/current-operating-model.md / app/guide/page.tsx

## 4. データ構造
PromptQueueItem（id/title/prompt/projectId/projectName/goalProgressId/goalProgressTitle/status/source/各日時/executionRunId/resultSummary/errorMessage/notes/relatedInboxId/relatedReviewId/relatedUrl）。priority/preferredExecutor/goalId/goalTitle は内部互換のみ・UI非表示。

## 5. 検証結果（Fable独立実行）
- tsc 0 / lint 0 / build 成功（/prompt-queue ＋ 3 API がビルド対象）。
- フォームは4項目のみ（実行対象AI・優先度なし）。
- import: promptQueue/todos 両形式 OK、`goal`→`goalProgress` 正規化（t1 の goalProgressTitle=「旧Goal名」）、`priority`/`assignee`/`preferredExecutor` 無視（警告）、title無し/prompt無しエラー、project不一致は未紐付け警告。
- 次回やる候補3件が理由付き（「Goal進捗が未完了のため」「未実行のため」）。`snooze` で候補から除外（3→2）。`DELETE`→`archived`（物理削除なし）。
- 既存 /queue・/decide・/tasks/import 回帰なし（全200）。
- テストデータは prompt-queue.json を空に戻して消去。

## 6. iPhone表示
未実機（ポート待受制限）。1カラム・flex-wrap 構成。実機目視は残課題。

## 7. 未対応 / minor
- Prompt Queue は貯蔵庫＋候補表示まで。**実際の自動実行（Factory/auto-queue）へは未接続**。
- minor: `promptQueue` と `todos` を同一 payload に両方入れると `promptQueue` のみ処理（実運用は片方ずつ想定）。

## 8. 次にやるべきこと
- iPhone実機確認。Prompt Queue を実 Factory 実行へ接続するか・BottomNav 主要タブへ昇格するか検討。

## ChatGPT レビュー依頼文
```
対象: progress Prompt Queue 新設（runId 20260614-092943 / commit dd560a7）
新規: types/prompt-queue.ts, lib/prompt-queue.ts, app/api/prompt-queue/*, app/prompt-queue/page.tsx,
  components/prompt-queue/PromptQueueBoard.tsx ほか
背景: 作業プロンプト貯蔵庫。入力4項目(タスク名/プロンプト/Project/Goal進捗)、実行AI・優先度非表示、GoalとGoal進捗統一、JSON取り込み(goal→goalProgress)、未完了は次回やる候補。
観点:
1. 入力簡素化(優先度・実行AIを持たず Project/Goal進捗状態から候補順を決める)方針は運用に耐えるか。
2. 次回やる候補の並び順ロジック(Project優先→Goal進捗停止→failed→古いqueued→最近追加)は妥当か。
3. JSON取り込みのバリデーション/正規化に穴はないか(両配列同時/重複/project未紐付け)。
4. 貯蔵庫を実Factory実行へどう接続すべきか(auto-queueと統合 or 別系統)。
```
