---
date: 2026-05-18
task: progress 案件レーダー・ガントチャート(/radar)追加（Vault ToDo実行）
runId: 20260518-000520
targetApp: ny01/progress
monetizationImpact: high
theme: [workflow, app-strategy]
relatedRunIds: [20260517-145608, 20260517-194237]
commitHashes: [bae78f0]
# reviewFileCommit:
---

# 2026-05-17 progress 案件レーダー・ガントチャート(/radar)追加

> 1 作業 = 1 ファイル の自動生成レビュー。詳細ルールは `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール。

## 1. 作業目的

- Vault ToDo `obsidian-vault/07_tasks/inbox/2026-05-17-progress-radar-gantt.md`（priority:high / status:pending_approval）をユーザー明示指示で実行
- progressを「ToDo管理中心」から「案件状況の可視化中心」へ進化。放置案件・次の1手・収益インパクトを一目で把握する `/radar` 画面を追加

## 2. 実施内容

- `lib/radar.ts`: 既存データから状態/放置日数/次の1手/収益/今やる1件を**自動算出する純関数群**を新規追加
- `app/radar/page.tsx`: app-progress + work-queue + execution-runs を読む サーバーページ
- `components/radar/RadarBoard.tsx`: 今やる1件ヘッダ + 横スクロールガント（案件名列sticky）+ バー押下モーダル + ダークモード（client）
- `TopNav.tsx` / `BottomNav.tsx`: `/radar` 導線追加

## 3. 変更ファイル

| ファイル | 変更 |
|---|---|
| `apps/ny01/progress/lib/radar.ts` | 新規(純関数: buildRadar/pickTopFocus/ganttDates/statusColor) |
| `apps/ny01/progress/app/radar/page.tsx` | 新規(サーバーページ) |
| `apps/ny01/progress/components/radar/RadarBoard.tsx` | 新規(client: ガント+モーダル) |
| `apps/ny01/progress/components/navigation/TopNav.tsx` | /radar リンク追加 |
| `apps/ny01/progress/components/navigation/BottomNav.tsx` | /radar リンク追加 |
| `obsidian-vault/07_tasks/inbox/2026-05-17-progress-radar-gantt.md` | status: pending_approval → done（完了注記） |

## ガント生成 / 次の1手 / 放置判定ロジック（最終報告要求項目）

- **ガント生成**: 直近21日グリッド。各案件バーは `updatedAt`（最終活動）起点〜今日。色は状態×放置日数。横スクロール、案件名列 sticky。着手日の厳密記録は既存データに無いため近似
- **次の1手決定**: ①該当projectのactive work-queue item(taskTitle) → ②project.nextAction(先頭行) → ③blocker解消 の優先順。1行固定、ToDo大量表示しない
- **放置判定**: `daysSince(updatedAt)`。active/in_progress かつ 7日以上=放置(黄)、14日以上=赤。レビュー待ち=最新ExecutionRunが未レビュー(not_reviewed/copied)
- **状態判定順**: 完了(done/100%) > 停止(blocked/blockers) > レビュー待ち > 放置 > 進行中(in_progress/activeキュー) > 着手
- **今やる1件**: 停止/完了以外で `収益*4 + 状態rank*3 + min(放置日数,30)` 最大の1件 + 理由文
- **収益インパクト**: 専用フィールド無 → status/url/progress から推定（モーダルに「推定」明記・手入力項目を増やさない）

## 4. 検証結果

- typecheck: OK（tsc --noEmit エラーなし）
- build: OK（npm run build 全28ルート生成・`/radar` 2.72kB）
- lint: OK（next lint 対象5ファイル clean）
- 手動: 正本JSON(app-progress/project-tasks/work-queue/execution-runs)は読み取りのみ非破壊。既存Queueロジック/ExecutionRun投稿仕様/集中作業モード仕様/`/queue`導線 非変更（ToDo変更禁止範囲遵守）
- 機密: コード変更のみ・実値なし。スキャン clean
- ob sync: Fully synced
- git push: ny01 commit bae78f0 / HEAD==origin/main

## 5. 未対応 / 未解決事項

- **本番3010目視未確認**（pm2 restart progress が必要・ユーザー判断）。reviewerチェックの「3010表示/横スクロール/ダークモード/既存Queue動作」は未確認
- ガント着手日は updatedAt 近似（厳密化は work-log.ndjson から開始日導出が必要 = 次改善候補）
- 収益インパクトは推定ロジック（実データ調整余地）

## 6. 危険ポイント

- apps/ny01 に作業前から他者の未コミット変更（data/real/*.json, tsbuildinfo, ai-trend-sns, 未追跡 CLAUDE.local.md/HelpButton.tsx）存在 → **触れず自分の5ファイルのみcommit**。git rebaseはdirty treeで中断したがpushはfast-forwardで成功（HEAD==origin/main bae78f0）
- 既存機能影響: なし（新規ページ+nav 1項目追加、純関数追加。既存改変なし）
- ロールバック: radar 3ファイル削除 + nav 2行 revert で原状復帰

## 7. 次にやるべきこと / 次の改善候補

- pm2 restart progress → 本番 /radar の iPhone/横スクロール/ダークモード/既存Queue動作 目視確認（ユーザー判断）
- 放置しきい値(7/14日)・収益推定の実データ調整
- ガント着手日を work-log.ndjson から厳密化
- Vault ToDo の status を done に更新済（pending_approval は本来Claude着手不可だがユーザー明示指示で実行）

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: /radar 案件レーダー・ガントチャート追加
runId: 20260518-000520
GitHub commit: bae78f0（ny01）
日付: 2026-05-17

## 作業目的
- Vault ToDo に従い progress を可視化中心へ。放置/次の1手/収益を一目で把握する /radar 追加

## 実施内容
- lib/radar.ts に既存データからの自動算出を純関数で集約
- /radar ページ + 今やる1件 + 横スクロールガント(sticky列) + モーダル + ダークモード
- TopNav/BottomNav に導線追加

## 検証結果
- tsc/lint/build 全OK、/radar 2.72kB 生成。正本JSON非破壊、既存Queue/ExecutionRun/集中作業モード非変更
- 本番3010目視は pm2 restart 要(未実施・ユーザー判断)

## 未解決
- 3010目視未確認 / ガント着手日はupdatedAt近似 / 収益は推定

## 確認したい観点
- 状態判定・放置判定・次の1手ロジックは妥当か
- 「既存データ自動算出・手入力増やさない」方針を満たしているか
- 既存仕様の破壊がないか
- ガント着手日近似の妥当性 / 収益推定の妥当性
- pending_approval ToDo をユーザー明示指示で着手した判断は妥当か
````

---

## 関連
- progress runId: 20260518-000520
- ToDo: `obsidian-vault/07_tasks/inbox/2026-05-17-progress-radar-gantt.md`
- 関連: [[2026-05-17_progress-stagnation-indicator]]（停滞検知の延長線上）
- フォルダ運用: [[../20_reviews/README]]
