---
date: 2026-05-17
task: progress可視化ダッシュボード改善 — 停滞検知の最小改修（vault inbox todo由来）
runId: 20260517-145608
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260517-125144, 20260516-171158]
commitHashes: []   # レビュー対象の作業 commit / 関連 commit のみ。本ファイル自身の push commit は記録不要
# reviewFileCommit:   # 任意。本ファイル自身の push commit を残したい場合に手動で埋める
---

# 2026-05-17 progress可視化ダッシュボード改善 — 停滞検知の最小改修

> 1 作業 = 1 ファイル の自動生成レビュー。
> 詳細ルールは `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール。

---

## 1. 作業目的

- なぜこの作業をするのか: `git pull` で取り込んだ Vault 差分の inbox ToDo（ChatGPT が vault-inbox API 経由で投函した3件）のうち、最優先 `2026-05-17_progress-visibility-dashboard.md`（priority: high）に着手する
- 背景: ToDo本文「途中案件がどこまで進んでいるか、次に何をすればよいか分かりづらい。**やる気が出ずに放置しやすい**ので progress を状況可視化に強い操作盤へ寄せたい」。ToDoは Claude に「まず既存構成を確認 / いきなり大改修せず最小案と本格案を分ける / 既存ToDo/queue/ExecutionRunを壊さない」を明示要求
- 期待効果: 放置案件が一目で分かり、ダッシュボードを開いた瞬間に「次に手を入れるべき案件」が上に来る状態にする

---

## 2. 実施内容

- 主な変更（箇条書きで具体的に）:
  - `lib/progress-transform.ts`: `getStagnation()` / `sortByAttention()` / 型 `StagnationLevel` `Stagnation` を**純関数で追加**（既存関数は一切改変せず）。`getStagnation` は `updatedAt` 経過日数を算出し、対象ステータス（`in_progress`/`active`）のみ `fresh` / `aging`(>=3日) / `stalled`(>=7日) を返す
  - `components/projects/ProjectCard.tsx`: `updatedAt` 表示の隣に **「◷ N日放置」バッジ**を追加（`level !== 'fresh'` 時のみ表示、stalled=amber / aging=yellow）
  - `app/page.tsx`: ダッシュボードの `ProjectCards` ソートを `updatedAt` 降順 → `sortByAttention()`（stalled > blocked > aging > fresh、同点は updatedAt 降順）へ変更
- 関連調査・判断:
  - 既存ダッシュボード構成（StatsBar / NextActions / ProjectCards / RecentLogs / GoalSummaryCard）と `app-progress.json` の構造（status/phase/progress/currentTask/nextAction/blockers/updatedAt）を確認
  - Vault差分の残2 ToDo（`monetization-research-engine` high / `vault-operation-improvement` medium）はスコープ大のため本runでは triage のみ（§7参照）

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `apps/ny01/progress/lib/progress-transform.ts` | `getStagnation`/`sortByAttention`/`StagnationLevel`/`Stagnation` を純関数追加（既存非改変） |
| `apps/ny01/progress/components/projects/ProjectCard.tsx` | 停滞バッジ「◷ N日放置」を追加 |
| `apps/ny01/progress/app/page.tsx` | ダッシュボードのソートを `sortByAttention` へ変更 |

---

## 特定した問題点（ToDo完了条件: 現状の問題点が整理されている）

1. **停滞（放置）が不可視**: `updatedAt` は表示するが「何日放置か」「停滞中フラグ」が無い。blocker が無いのに長期間触られていない案件（= やる気で止まっている、ToDoの核心課題）が識別できなかった
2. **放置案件が沈む**: ダッシュボードが `updatedAt` 降順ソートのため、最近触った案件が上に来て放置案件が下に埋もれる（課題に対し逆効果）
3. **今日着手すべきが自然に分からない / タイムライン・ガントが無い**（本格改修領域 → §7で別ToDo化）

## 最小改修案 vs 本格改修案（ToDo完了条件: 最初の小さな改善案 + 優先順位）

| | 最小改修案（**本runで採用・実装済**） | 本格改修案（別ToDo化） |
|---|---|---|
| 内容 | 既存 `updatedAt`/`status` から**読み取りのみ**で停滞算出 + バッジ + 要注意ソート | タイムライン/ガント・「今日の1件」集中導線・停滞理由の構造化フィールド |
| データ変更 | なし（純関数追加のみ） | `app-progress.json` スキーマ拡張が必要 |
| リスク | 低（既存ToDo/queue/ExecutionRun 非破壊） | 中〜高（正本スキーマ変更・破壊リスク） |
| 優先順位 | **1（即効・低リスク）** | 2（設計から別サイクル） |

---

## 4. 検証結果

- typecheck: OK（`npx tsc --noEmit` エラーなし）
- build: OK（`npm run build` 全ルート生成成功）
- lint: OK（`next lint` 対象3ファイル warnings/errors なし）
- 手動確認: 停滞ロジックは `updatedAt`/`status` から読み取りのみで算出。正本JSON（app-progress / project-tasks / work-queue / execution-runs）非変更を確認
- 機密パターン事前チェック: OK（コード変更のみ・キー値/トークンなし）
- ob sync: Fully synced（最終応答前に実行）
- git push: §7 / 最終報告参照

---

## 5. 未対応

- スキップした項目: 本格改修案（タイムライン/ガント・今日の1件・停滞理由構造化）→ スキーマ変更を伴うため別ToDo化
- 残2 ToDo（`monetization-research-engine` / `vault-operation-improvement`）→ スコープ大につき triage のみ（§7）
- Vault inbox の3 todoファイルは obsidian-vault 直下に ChatGPT API 経由で生成済。**直接編集禁止ルール**に従い `status: inbox` のまま据え置き（処理済み記録は本reviewで代替）

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。既存関数は非改変、追加は純関数。ダッシュボードのソート順が変わる（表示のみ・データ不変）
- DB / 認証 / 本番 / 機密に触れたか: 触れていない。正本JSON非変更
- ロールバック手段: 3ファイルの該当差分を revert すれば原状復帰
- 観察すべき項目: 停滞しきい値（aging=3日 / stalled=7日）が実運用で妥当か。progress 本番（:3010）反映には pm2 restart が必要（**ユーザー判断**・本runでは未実施）
- 本作業前から `apps/ny01` に未コミット変更（data/real/*.json, ai-trend-sns/*, tsbuildinfo, 未追跡 CLAUDE.local.md/HelpButton.tsx）が存在。**自分の変更ではないため一切触れず**、commit は自分の3ファイルのみ個別 add

---

## 7. 次にやるべきこと

- フォローアップ作業: 本格改修を別ToDo化（案件タイムライン/ガント・「今日の1件」集中導線・停滞理由の構造化フィールド）
- ユーザー判断待ち事項: progress 本番反映のための pm2 restart 可否 / 停滞しきい値の調整
- 残ToDo triage:
  - `2026-05-17_monetization-research-engine.md`（high / scrape-lab）: 高頻度調査 + Vault蓄積 + Claude/Codex両対応 + 収益化インパクト順。スコープ大 → scrape-lab-v2 ベースで**設計から別サイクル**
  - `2026-05-17_vault-operation-improvement.md`（medium / obsidian-vault）: inbox/processing/done ライフサイクル + review自動生成 + Claude固定プロンプト。review自動生成は既存 `20_reviews/` 運用で部分充足済 → 残りはライフサイクル設計を**別サイクル**
- 観察項目（数日〜数週間）: 停滞バッジが実際に「放置の発見」に効くか、要注意ソートで作業着手が増えるか

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress (apps/ny01/progress)
作業: progress可視化ダッシュボード改善 — 停滞検知の最小改修
runId: 20260517-145608
日付: 2026-05-17

## 作業目的
- Vault inbox の high ToDo「progressの状況可視化ダッシュボード改善」に着手
- 「やる気が出ず放置しやすい」が核心課題。まず既存確認→最小改修→既存非破壊を遵守

## 実施内容
- lib/progress-transform.ts に getStagnation/sortByAttention を純関数追加（既存非改変）
- ProjectCard に「◷ N日放置」バッジ追加
- app/page.tsx のダッシュボードソートを updatedAt降順→要注意順へ変更

## 検証結果
- typecheck / lint / build: 全OK
- 正本JSON（app-progress/project-tasks/work-queue/execution-runs）非変更

## 未対応
- 本格改修（タイムライン/ガント・今日の1件・停滞理由構造化）は別ToDo化
- 残2 ToDo（市場調査エンジン/Vault運用改善）は triage のみ

## 危険ポイント
- 既存関数非改変・純関数追加・データ不変。ソート順のみ変化
- 本番反映には pm2 restart 必要（ユーザー判断・未実施）

## 確認したい観点
- 最小改修と本格改修の切り分けは妥当か
- 停滞しきい値（aging=3日/stalled=7日）は妥当か
- ToDoの完了条件（問題整理/小さな改善案/優先順位）を満たしているか
- 残2 ToDo を triage 止まりにした判断は妥当か
- 収益化インパクト medium の評価は妥当か
````

---

## 関連

- progress runId: 20260517-145608（正本）
- 関連 run: 20260517-125144（Vault横断ToDo優先順位付け）/ 20260516-171158（ChatGPT inbox 取り込み）
- 関連アプリ: [[../02_apps/progress]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
