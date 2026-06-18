---
date: 2026-06-19
task: ゴール手動追加を直接追加/JSON一括追加に整理・分解プロンプト生成UIを廃止
runId: 20260619-000632
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260618-085742]
commitHashes: [d73816b]
---

# 2026-06-19 ゴール手動追加の整理（直接追加 / JSON一括追加・プロンプト生成廃止）

> ユーザー指示: ゴールを完全に手動で追加できるように（直接追加とjson追加）、プロンプト生成はいらない。

---

## 1. 作業目的

- 目標タブのゴール追加を「直接追加」と「JSON一括追加」の2方式に整理し、分解プロンプト生成は廃止する。
- 直接追加で進捗(=今/目標)まで手動設定できるようにする。

---

## 2. 実施内容

- `components/goals/GoalPlannerForm.tsx`:
  - 削除: 「目標を入力する→分解プロンプト生成→コピー」(Step1/Step2) と関連 state/関数（goalText / monetizationFirst / phaseHint / generatedPrompt / promptCopied / buildPrompt / handleCopyPrompt / copy / fallbackText）、未使用 useMemo import。
  - 残す: ①「ゴールを直接追加」（項目入力1件・`upsertSingle`）②「JSONで一括追加」（`validate`→`import`）。
  - 直接追加に **metric / 今(current) / 目標(target)** 入力を追加。単体 prompt ラベルを「説明(任意)」に変更。見出しを「ゴールを直接追加」「JSONで一括追加」に整理。
- `lib/goal-writer.ts`: `SingleGoalInput` と `upsertSingleGoal` に metric/target/current を追加（`upsertSingle` API は body.goal をそのまま渡すため UI 値が反映される）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/components/goals/GoalPlannerForm.tsx | プロンプト生成削除・直接追加にmetric/current/target追加・ラベル整理 |
| progress/lib/goal-writer.ts | SingleGoalInput/upsertSingleGoal に metric/target/current |
| progress/app/guide/page.tsx | FAQ追加（自分でゴールを追加するには） |
| progress/docs/operations/current-operating-model.md | frontmatter/変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc 0・未使用localエラーなし）
- build: OK（next build 0）
- 手動確認: `/goal-planner` 200 で「ゴールを直接追加」「JSONで一括追加」「今(current)」「目標(target)」描画、分解プロンプト/プロンプトをコピーは非表示（0件）。`/guide` 200。
- 直接追加 API E2E: `upsertSingle`(metric=月間収益, current=3, target=12)→ goals.json 反映 → `/api/auto-queue` で done/total 3/12・ratio 25% を実測（進捗=current/target が手動設定どおり）。テストゴールは削除。
- 機密スキャン: clean / ob sync: n/a / git push: d73816b

---

## 5. 未対応

- 直接追加は従来どおり初期フェーズ+初期todoを自動生成する（純粋なゴールのみにしたい場合は抑制オプションが要検討）。
- JSON スキーマ説明は guide / JSON取込ページと文言統一の余地あり。

---

## 6. 危険ポイント

- 既存機能への影響: 低。UI削除＋フォーム項目追加＋writer項目追加のみ。JSON一括追加・upsert系の既存ロジックは非変更。
- ロールバック: commit d73816b の revert。

---

## 7. 次にやるべきこと

- 直接追加の初期todo自動生成の要否判断。
- 実ブラウザでの直接追加/JSON追加の通し操作確認（E2EはAPI直叩きで代替）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: ゴール手動追加を直接追加/JSON一括追加に整理・分解プロンプト生成UIを廃止
runId: 20260619-000632
日付: 2026-06-19
GitHub commit: d73816b

## 実施内容
- GoalPlannerFormから分解プロンプト生成(Step1/2+state/関数)を削除。残すのは直接追加(upsertSingle)とJSON一括追加(validate→import)。
- 直接追加にmetric/今(current)/目標(target)を追加し、SingleGoalInput+upsertSingleGoalに反映=進捗(current/target)を手動設定可能に。promptラベルは説明(任意)に変更。

## 検証
- tsc0/build0。/goal-planner描画(直接追加/JSON追加/今/目標、プロンプト系非表示)。直接追加API E2E: current3/target12→25%反映。

## 未対応
- 直接追加は初期todoを自動生成(抑制オプション未)。実ブラウザ通し操作は未(API代替)。

## 確認したい観点
- プロンプト生成廃止と2方式整理の妥当性
- 直接追加でtarget/currentを手動設定→進捗反映の設計
- 初期todo自動生成を残す/抑制すべきか
- JSON一括追加スキーマの説明十分性
````

---

## 関連

- progress runId: 20260619-000632（正本）
- 関連: components/goals/GoalPlannerForm.tsx / lib/goal-writer.ts(upsertSingleGoal) / [[2026-06-18_goal-progress-fix]]
- 関連アプリ: [[../02_apps/progress]]
