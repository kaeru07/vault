---
date: 2026-06-14
task: Prompt Queueを実Factory自動実行に接続（安全ゲート付き）
runId: 20260614-100448
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260614-092943]
commitHashes: [3dcc134]
---

# 2026-06-14 Prompt Queue → 実Factory自動実行 接続

> Codex 実装 → Fable レビュー（reviewer）・dry_run実機テスト・コミット。**安全第一**の接続。

## 1. 接続方式
- 新規 `runPromptQueueDispatch()`（`lib/prompt-queue-runner.ts`）を、定時起動 `runScheduledFactory`（11/14/16/23 JST）の lock 内で **Epic Factory 実行の後**に別ステップとして呼ぶ（`{mode:'auto', confirm:true, maxItems:1}`）。**Epic dispatch ロジックは不変更**。

## 2. 安全ゲート（最重要）
各 item の `title + prompt` を:
- `classifyCodexEligibility`（危険シグナル）
- hard-deny 正規表現: `課金/billing/deploy/デプロイ/本番/production/secret/.env/認証/migration/削除/destructive/force`
に通し、**該当したら adapter を起動せず `needs_user_prompt_fix` へ隔離**（errorMessage に理由）。

## 3. status遷移 / mode別
- 対象（次回やる候補）→ `running`。
- **dry_run（既定）**: adapter 実起動せず ExecutionRun 記録のみ → `reserved`。
- **auto+confirm（定時起動のみ）**: 既存 `getAdapter('claude')` で実起動 → 成功/partial=`completed` / 失敗=`failed` / rate limit=`needs_retry`。
- 危険 → `needs_user_prompt_fix`。completed/canceled/archived/snoozed は対象外。

## 4. ExecutionRun記録
`source='prompt_queue'` / `factoryRun=true` / `factoryDispatch=true` / `dispatchMode` / `dispatchPlanId='prompt-queue:<id>'` / `promptUsed`。`item.executionRunId` に runId を戻す。`factoryEnabled=false` / `Blocked` では何もしない。cap=1〜2。

## 5. 変更ファイル
lib/prompt-queue-runner.ts（新規）/ lib/factory-schedule.ts / docs/operations/current-operating-model.md

## 6. 検証（Fable独立・dry_runで実施）
- tsc 0 / lint 0 / build 成功。
- **dry_run実機テスト**（一時エンドポイント tmppqtest を作成→検証→削除）:
  - considered 2 / reserved 1 / blocked 1 / **executed 0**。
  - 安全 item「Inboxの表示文言を直す」→ `reserved` ＋ ExecutionRun `20260614-100029-601`（**adapter 未起動**）。
  - 危険 item「本番DBを削除 force」→ `needs_user_prompt_fix`（危険シグナル「本番」・**未実行**）。
  - cap 2、factory OFF→factory_off、Blocked→blocked。
  - テスト後: 一時エンドポイント削除・prompt-queue.json を空に戻し・テスト ExecutionRun 削除。
- 既存 /prompt-queue・/queue・/decide 回帰なし（200）。

## 7. リスク / 危険ポイント
- 定時 `auto` で**安全ゲート通過分は実 adapter を起動する**（実際にコードが動く）。想定外プロンプトの誤実行を hard-deny＋分類器の多層防御で抑止。過剰ブロック（例「不要ログを削除」→blocked）は安全側の失敗で許容。
- auto+confirm の実 adapter 起動はレビューでは未実行（dry_run で検証）。翌日の定時で実挙動を要観察。

## 8. 次にやるべきこと
- 翌日の定時（11/14/16/23）で Prompt Queue 候補が実 auto 実行され、安全分=completed / 危険分=needs_user_prompt_fix になるかを ExecutionRun（source=prompt_queue）で確認。
- hard-deny の過剰ブロック調整・maxItems スループット調整を運用後に判断。

## ChatGPT レビュー依頼文
```
対象: progress Prompt Queue→実Factory自動実行 接続（runId 20260614-100448 / commit 3dcc134）
変更: lib/prompt-queue-runner.ts(新規), lib/factory-schedule.ts, docs/operations/current-operating-model.md
背景: Prompt Queueの次回やる候補を定時Factoryで1件/起動、安全ゲート通過分のみauto実行(危険はneeds_user_prompt_fix隔離)。dry_runは予約のみ。
観点:
1. 安全ゲート(classifyCodexEligibility＋hard-deny正規表現)は自動実行の防御として十分か。すり抜けリスク・過剰ブロック。
2. 定時autoで実adapter起動する設計の是非(maxItems:1/起動)。承認なしで実行してよい範囲の線引き。
3. dry_run/auto+confirmのmode分岐とstatus遷移(running→reserved/completed/failed/needs_retry/needs_user_prompt_fix)に穴はないか。
4. Epic FactoryとPrompt Queueを同一lock内で順次実行する設計の負荷・停止条件。
```
