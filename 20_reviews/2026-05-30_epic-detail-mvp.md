---
date: 2026-05-30
task: Phase1 Epic詳細画面 /epic/[epicId] をAI工場の主画面として実装
runId: 20260530-215557
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260530-210159]
commitHashes: []
---

# 2026-05-30 Phase1 Epic詳細画面 /epic/[epicId] 実装

> ProgressをAI工場へ進化させる統合路線のPhase1。新正本ゼロ・追加専用フィールドのみで既存正本を読み取り集約。

---

## 1. 作業目的

- なぜ: AI工場=Progress の主画面を作る。ユーザーが毎日見る場所を `/operations` から `/epic/[epicId]` へ移す。
- 背景: 前回(runId 20260530-210159)で統合路線を確定。handoffをUIに出さず、前回作業/決定事項/次回予定/承認待ち/実行履歴の5観点で見せる方針。
- 期待効果: 案件単位でAI工場の状態を一望し、「続きから実行」1ボタンで再開できる。

---

## 2. 実施内容

- データ構造調査: Epic/ToDo/Approval/Decision/ExecutionRun/NextActions を実データで精査。ExecutionRun↔Epic の結合キーが唯一の不足と特定。
- 最小変更方針: `ExecutionRun.epicId?` と `Epic.targetApps?` を追加（任意・後方互換）。153件の既存Runは無改変で動作。結合は epicId→relatedTodoIds→latestRunId→targetApps の優先順位、キー未整備時は global-fallback 仮表示。
- 読み取り集約 `getEpicDetail()` を新設（書き込みゼロ・既存getter再利用）。
- UI: `/epic`(一覧)、`/epic/[epicId]`(主画面)、`ResumeButton`(続きから実行=内部handoffコピー)。
- ナビ: TopNavに「工場」追加、BottomNavは「AI自走」→「工場」差し替え（Phase3廃止計画と整合）。
- Phase0: `/operations` の「handoff」見出し→「継続実行情報」へ内部化。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| types/execution-run.ts | `epicId?` 追加 |
| lib/types/operations.ts | `Epic.targetApps?` / `EpicDetail` / `ExecutionRunBrief` 追加 |
| lib/operations-store.ts | `getEpic`/`getEpicDetail`/スコープ判定/`nextActionText`正規化、`generateHandoffView(epicId?)` |
| app/api/operations/epics/[epicId]/route.ts | 新規 GET |
| app/api/operations/handoff/route.ts | `?epicId` 対応 |
| app/api/execution-runs/route.ts | POSTで `epicId` 受理 |
| app/epic/[epicId]/page.tsx | 新規（AI工場主画面） |
| app/epic/page.tsx | 新規（Epic一覧） |
| components/epic/ResumeButton.tsx | 新規（続きから実行） |
| components/navigation/TopNav.tsx / BottomNav.tsx | 「工場」導線 |
| app/operations/page.tsx | Phase0 ラベル内部化 |
| data/real/epics.json | epic-91に `targetApps` 追加（結合キー・追加専用） |

---

## 4. 検証結果

- typecheck: OK（`npx tsc --noEmit`）
- build: OK（`npm run build`、/epic・/epic/[epicId] ルート登録）
- lint: OK（`next lint` 0 warnings）
- 手動確認: /api/operations/epics/epic-91 → runScope=epic, latestRun=20260530-210159, recentRuns5/nextActions5/executors4。/epic/epic-91 HTTP200 全セクション描画。/epic 200 / 不明Epic 404 / handoff?epicId=epic-91 source:generated。/operations 200・handoff表記消失。
- 不具合修正: 当初500（前回POSTで nextActions をオブジェクト配列で送信→React child error）。`nextActionText()` で全経路を文字列正規化し解消（再build後200・`[object Object]`消失）。
- 機密スキャン: OK（実値なし）
- ob sync / git push: 本応答で実施

---

## 5. 未対応

- session.handoffText（自由文・/queue・/morning）廃止はPhase3で別途。今回は温存。
- Approval決定UIのEpic詳細インライン化（現状は /approvals へ誘導）。
- progress アプリ本体の git commit/push（後述・要remote確認）。

---

## 6. 危険ポイント

- 全変更が追加専用。既存153 Run・既存API・既存画面に破壊なし。
- data/real/epics.json は1フィールド追加のみ（rm/mv なし）。
- pm2 はビルド済み `.next` を配信するため、コード変更後は `npm run build` → `pm2 restart` が必須（今回これを失念し一時500を誤認、再build後解消）。

---

## 7. 次にやるべきこと

- executor が Run POST 時に `epicId` を付与する運用へ（scope精度が自動向上）。
- Phase2: `/operations` → `Automation` へ改名、health bar を Dashboard へ移設。
- Phase3: `/ai-drive` モック撤去・`session.handoffText` 廃止。
- progress リポジトリの commit/push。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress
作業: Phase1 Epic詳細画面 /epic/[epicId] をAI工場の主画面として実装
runId: 20260530-215557
関連: 20260530-210159（設計確定）
日付: 2026-05-30

## 目的
- ProgressをAI工場へ進化させる統合路線のPhase1。Epic詳細を主画面化し、handoffをUIに出さず「続きから実行」ボタンとして内部利用。

## 設計/実装
- 不足データはExecutionRun↔Epicの結合キーのみ。ExecutionRun.epicId?(任意POST)とEpic.targetApps?(フォールバック)を追加し新DB/移行なしで解消。
- 結合優先順位: epicId→relatedTodoIds→latestRunId→targetApps。未整備時はglobal-fallback仮表示。
- getEpicDetailは既存getterの読み取り集約のみ(書き込みゼロ)。
- /epic, /epic/[epicId], ResumeButton 新設。Phase0でoperationsのhandoffラベルを内部化。

## 検証
- tsc/build/lint すべてOK。/epic/epic-91 HTTP200全セクション。handoff?epicId 動作。
- 一時500の原因=前回POSTのnextActionsがオブジェクト配列→nextActionTextで文字列正規化し堅牢化。

## 確認したい観点
- 結合キー設計(epicId優先+targetAppsフォールバック+global-fallback)は妥当か
- 追加専用で既存153Run/既存画面を壊していないか
- handoff内部化でCodex/Claude再開文脈が劣化しないか(handoff?epicIdの内容)
- session.handoffText廃止をPhase3に回す判断は妥当か
- 次の一手(executorのepicId付与運用)の優先度
````

---

## 関連

- progress runId: 20260530-215557（正本）
- 関連 run: 20260530-210159（統合路線の設計確定）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
