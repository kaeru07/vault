---
date: 2026-06-14
task: progress 全ルート棚卸し・旧画面/下タブ未掲載ページのナビ整合性レビュー
runId: 20260614-101321
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260614-100448]
commitHashes: []
---

# 2026-06-14 progress ルート棚卸し・ナビ整合性レビュー

> コード変更なし（レビュー＋実装タスクの Prompt Queue 登録）。

## 最重要発見
**モバイル BottomNav（`components/navigation/BottomNav.tsx`）は6タブ＝司令塔(/) / Inbox(/decide) / Projects(/portfolio) / Revenue(/revenue) / 運用(/guide) / Legacy(/legacy)。最重要の自動実行キュー(/queue)と Prompt Queue(/prompt-queue) が含まれず、iPhone から辿れない**（desktop TopNav・司令塔トップの「キュー調整」リンク・/legacy ハブ経由のみ）。これがユーザー指摘の中核。

## Queue / WorkQueue / Prompt Queue の関係（混乱整理）
| ルート | 正体 | 状態 |
|---|---|---|
| `/queue` | **自動実行キュー**＝Epic派生のAI工場「次にやる」単一ビュー（buildAutoQueue・正本） | **最重要・毎日見る**。だがモバイル下タブに無い |
| `/legacy/queue` | 旧 WorkQueue（work-queue.json 並べ替えUI） | 退避済み・非正本 → **廃止/統合候補（旧互換のみ残置）** |
| `/prompt-queue` | 作業プロンプト貯蔵庫（手動予約＋JSON取り込み・次回やる候補を定時Factoryが拾う） | 新設・Factory接続済。下タブに無い |

→ `/queue`(Epic自動実行) と `/prompt-queue`(プロンプト予約) は別物だが共に「自動実行」系。`/legacy/queue` は旧物で廃止候補。

## 全ルート棚卸し（39ルート）
| URL | ページ名 | 役割 | 現在の導線 | 下タブ | 必要度 | 対応方針 |
|---|---|---|---|---|---|---|
| / | 司令塔 | ホーム/次回予定 | BottomNav | ○ | 必須 | 維持 |
| /decide | 今日の判断/Inbox | レビュー/判断/Epic候補/AI保留 | BottomNav(Inbox) | ○ | 必須 | 維持 |
| /portfolio | Projects | Project一覧 | BottomNav | ○ | 必須 | 維持 |
| /revenue | Revenue | 収益マイルストーン | BottomNav | ○ | 中 | 維持 or 二次導線 |
| /guide | 運用 | 使い方/最終更新 | BottomNav(運用) | ○ | 中 | 維持 |
| /legacy | Legacy hub | 旧画面入口 | BottomNav | ○ | 中 | 維持 |
| **/queue** | **自動実行キュー** | **Epic派生・次にやる** | desktop+司令塔+legacy | **✗** | **毎日必須** | **★BottomNavに「自動実行」として追加** |
| **/prompt-queue** | **作業予約** | プロンプト貯蔵庫+次回やる候補 | desktop+legacy | **✗** | **頻繁** | **★自動実行タブ配下に統合** |
| /legacy/queue | 旧キュー(WorkQueue) | 旧並べ替え | desktop+legacy | ✗ | 低 | 廃止/統合候補 |
| /logs | ログ | ExecutionRun一覧/詳細 | desktop+legacy | ✗ | 中 | 自動実行タブ配下(実行履歴)へ統合検討 |
| /tasks | ToDo管理 | project-tasks ToDo | desktop+legacy | ✗ | 中 | ToDo導線・維持 |
| /tasks/import | ToDo JSON取り込み | 旧JSON一括 | /tasks内 | ✗ | 中 | 維持(Prompt Queue importと役割整理) |
| /inbox | 旧Inbox | 生triage | desktop+legacy | ✗ | 低 | /decideと重複→**廃止/統合候補** |
| /decisions | 決定事項(Decision Log) | 判断履歴 | legacy | ✗ | 低 | 旧互換残置 |
| /epic, /epic/[id], /epic/new | 工場/Epic | Epic一覧/詳細/新規 | desktop+legacy+内部 | ✗ | 中 | 内部リンク維持 |
| /recommended-epics(+[id]) | おすすめEpic | 候補承認 | desktop+legacy | ✗ | 中 | Inbox「Epic候補」と重複→統合検討 |
| /approvals | 承認 | 横断承認受信箱 | desktop+legacy | ✗ | 中 | Inbox「今日の判断」と関係整理 |
| /goal-planner | 目標 | Goal計画 | desktop+legacy | ✗ | 中 | **Goal進捗タブ候補** |
| /monetization(+[id]) | 収益化 | 収益化候補Hub | desktop+legacy | ✗ | 中 | Revenue配下に統合検討 |
| /verify-todos | 動作確認 | 確認Todo | desktop+legacy | ✗ | 中 | 維持 |
| /automation | 自動化 | Factory ON/OFF設定 | desktop+legacy(+/operations) | ✗ | 中 | 自動実行タブ配下(設定)へ統合 |
| /factory/candidates | アプリ開発候補キュー | 新規アプリ候補 | legacy | ✗ | 低 | 統合/廃止候補 |
| /projects(+new,[id]) | 案件 | 旧Project管理 | desktop+legacy | ✗ | 低 | /portfolioと重複→廃止/統合候補 |
| /radar | 案件レーダー | 案件俯瞰 | desktop+legacy | ✗ | 低 | 旧互換残置 |
| /morning | 朝会 | 朝会ブリーフィング | desktop+legacy | ✗ | 低 | 旧互換残置 |
| /ai-drive | AI自走 | AI自走状況 | desktop+legacy | ✗ | 低 | 旧互換残置 |
| /codex | Codex | Codex結果戻し | desktop+legacy | ✗ | 中 | 維持(Codex連携) |
| /daily | 日別進捗 | 日別 | desktop+legacy | ✗ | 低 | 旧互換残置 |
| /operations | →/automation | リダイレクト | - | ✗ | - | リダイレクトのみ維持 |
| /pending | →/tasks | リダイレクト | - | ✗ | - | リダイレクトのみ維持 |

## 下タブ未掲載の「重要」ページ（要昇格）
1. **/queue 自動実行キュー（最優先）** — AI工場の中核。iPhoneで辿れないのは致命的。
2. **/prompt-queue 作業予約** — 自動実行の入力口。
3. /logs（ExecutionRun詳細/実行履歴）— 実行中/失敗/履歴の確認。
4. /goal-planner（目標）— Goal進捗導線。

## 提案: モバイル下タブ再編（5タブ・自動実行を必ず見える化）
ユーザー案に沿った5タブ:
1. **ホーム** `/` — 全体サマリ・今日見るべき
2. **ToDo** `/decide` — 今日の判断/レビュー/要修正/（Prompt Queue状態リンク）
3. **Project** `/portfolio` — Project一覧/進捗/関連Goal/最新作業
4. **Goal進捗** `/goal-planner`（または goals ビュー）— 目標/進捗/関連Project/次回候補
5. **自動実行** `/queue` — 自動実行キュー/Prompt Queue/次回やる候補/実行中/失敗/実行履歴(/logs) を**ハブ化**

→ Revenue/運用(/guide)/Legacy は二次導線（オーバーフロー or Legacyハブ/運用ページ内）へ。

**即時の最小修正（推奨・低リスク）**: まず BottomNav に **「自動実行」(/queue)** を1つ追加（iPhone未到達の解消）。フル再編は段階的に。

## 重複・廃止/統合候補
- /inbox(旧) ↔ /decide（今日の判断）= 重複 → 旧inbox廃止/統合候補
- /projects(案件) ↔ /portfolio = 重複 → 旧projects廃止/統合候補
- /legacy/queue(旧WorkQueue) → 廃止候補（/queueが正本）
- /recommended-epics ↔ Inbox「Epic候補」= 役割重複 → 統合検討
- /monetization ↔ Revenue = 関係整理（Revenue配下へ）
- /operations→/automation, /pending→/tasks = リダイレクトのみ（維持でよい）

## 実装タスク（Prompt Queue へ登録済み）
runId 20260614-101321 のレビュー結果として、実装タスクを Prompt Queue に登録（id `pq-mqd3acmp-k0tp3r` / status queued）。BottomNav への自動実行導線追加＋Queue系統合を内容とする。status=queued のため**次回定時Factoryの候補**になる（不要なら snooze/cancel 可）。

## 完了条件チェック
- 全ページ/全ルート棚卸し ✓（39ルート表）
- 下タブにない重要ページ洗い出し ✓（/queue・/prompt-queue・/logs・/goal-planner）
- 旧画面からしか開けないページ特定 ✓
- 自動実行キューの現在地確認 ✓（/queue・desktop/司令塔/legacyのみ・mobile下タブ無し）
- 専用タブ/主要導線追加方針 ✓（5タブ案＋即時最小修正）
- Queue/WorkQueue/Prompt Queue重複整理 ✓
- 使わない旧ページの廃止/統合候補明記 ✓
- iPhoneで迷わない導線案 ✓
- 下タブ構成案 ✓
- Prompt Queue用JSON出力＋登録 ✓
