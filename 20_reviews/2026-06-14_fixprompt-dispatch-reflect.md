---
date: 2026-06-14
task: 修正依頼(fixPrompt)が次回自動実行に本当に反映されるかの確認テスト＋反映不具合の修正
runId: 20260614-085241
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260613-234811, 20260614-021255]
commitHashes: [1a5d275]
---

# 2026-06-14 fixPrompt → 次回自動実行 反映テスト＋修正

> ユーザー依頼: 「修正依頼を保存したから、本当に自動実行でこの内容で再修正がされるかの確認テストもして」

## 1. テスト結果（最重要）
**反映されていなかった（欠落を特定）→ 修正して反映を確認。**

- ユーザーが実 fixPrompt「inboxページでフィルターがゴール進捗とproject単位でできるようにしたい。」を run `20260614-021255`（epic-91）に保存済み。
- 修正前: `POST /api/operations/factory-dispatch {epicId:epic-91, executor:claude}` の生成プロンプトに修正指示が**含まれていなかった**（`fix instruction in prompt: False`）。

## 2. 原因
fixPrompt の流れ: run保存 → followup候補(reason/doneCriteria/notes) → (承認で) `epic.remainingWork`。
しかし Factory Dispatch プロンプト（`generateClaudeFactoryPrompt` / `generateCodexPrompt`）は **`epic.goal` / `epic.doneCriteria` / ExecutionRun由来 `nextActions`** のみ参照し、`remainingWork` も `fixPrompt` も読まない。→ 人間の修正指示が次回自動実行の作業指示に乗らなかった。

## 3. 修正内容
- `buildDispatchPlan` に `humanFixInstructions` を追加（当該 Epic の `needs_followup` Run の `fixPrompt`（新しい順）＋ 承認済みで `remainingWork` に入った修正指示）。
- `FactoryDispatchPlan` 型に `humanFixInstructions: string[]` を追加。
- Claude Factory プロンプトに `[3-1] 人間の修正指示（最優先で対応すること）`、Codex 引き継ぎプロンプトに `[4-1]` を出力。

## 4. 変更ファイル
lib/factory-dispatch.ts / lib/operations-store.ts / lib/types/operations.ts / docs/operations/current-operating-model.md

## 5. 検証（修正後・e2e）
- tsc 0 / lint 0 / build 成功。
- (a) `GET factory-dispatch?epicId=epic-91` → `humanFixInstructions: ['inboxページでフィルターがゴール進捗とproject単位でできるようにしたい。']` を取得。
- (b) epic-91 を一時的に dispatch 可能化（doneCriteria/priority/factoryEligible 付与）→ `POST factory-dispatch claude` → プロンプトに `[3-1] 人間の修正指示（最優先で対応すること） - inboxページでフィルターが…` が**出力された**（`fix instruction in prompt: True`）→ epic-91 をファイルスナップショットから**原状復帰**。

## 6. 注意 / 残課題
- **doneCriteria / priority の無い Epic は dispatch 自体が blocked**（epic-91 もこの状態）。修正指示は「dispatch 可能になった時点で必ずプロンプトに乗る」が、Epic に有効な契約（doneCriteria/priority）が無いと自動実行に進まない。→ 修正依頼だけで自動実行させたい場合は Epic に契約が必要。
- 承認フローは修正指示を `remainingWork` に入れる（`doneCriteria` には入れない）ため、dispatch 可能性自体は上がらない。これを上げるか（doneCriteria にも載せる）は要検討。
- 本番定時（11/14/16/23）で実際に fixPrompt 反映 Run が出るかは翌日確認。

## 7. 次にやるべきこと
- 修正依頼だけで自動実行に乗せたいなら、承認時に doneCriteria へも修正指示を載せる案を検討。
- 翌日の定時実行ログで fixPrompt 反映を確認。

## ChatGPT レビュー依頼文
```
対象: progress fixPrompt→Factory Dispatch反映（runId 20260614-085241 / commit 1a5d275）
変更: lib/factory-dispatch.ts, lib/operations-store.ts, lib/types/operations.ts, docs/operations/current-operating-model.md
背景: 「修正する」で保存したfixPromptが次回自動実行のdispatchプロンプトに乗っていなかった欠落をテストで特定し修正。Claude[3-1]/Codex[4-1]に人間の修正指示(最優先)を追加。
観点:
1. humanFixInstructionsの収集元(needs_followup Runのfixprompt＋remainingWorkの修正指示)は十分か・重複や古い指示の混入リスク。
2. doneCriteria無しEpicはdispatch blockedのまま=修正依頼だけでは自動実行されない。承認時にdoneCriteriaへ載せるべきか。
3. [3-1]/[4-1]を最優先指示として置く位置・文言は適切か。
4. 大量のfixPrompt履歴がある場合のプロンプト肥大化対策(現状は当該Epicのneeds_followupのみ)。
```
