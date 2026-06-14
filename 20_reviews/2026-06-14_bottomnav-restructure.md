---
date: 2026-06-14
task: モバイル下タブを5主要+その他に再編・自動実行(/queue)を主要タブへ昇格
runId: 20260614-105125
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260614-101321]
commitHashes: [f4fa2f9]
---

# 2026-06-14 モバイル下タブ再編（自動実行を主要タブへ）

> ルート棚卸しレビュー（runId 20260614-101321）の最重要指摘を実装で解消。ユーザー指示「2をいますぐ」＝5主要+自動実行の下タブ再編。

## 1. 目的
モバイル BottomNav に自動実行キュー(/queue)と作業予約(/prompt-queue)が無く iPhone から辿れなかった問題を、下タブ再編で解消。

## 2. 実施内容
- **BottomNav（6タブ）**: ホーム(`/`) / ToDo(`/decide`) / Project(`/portfolio`) / 目標(`/goal-planner`) / **自動実行(`/queue`)** / その他(`/legacy`)。`NEW_ROUTES` を新タブに更新（その他=/legacy が旧画面・Revenue・運用の catch-all）。
- **orphan回避**: Revenue(`/revenue`) / 運用(`/guide`) / 作業予約(`/prompt-queue`) / 動作確認(`/verify-todos`) を `/legacy` ハブの新グループ「よく使う（下タブにない主要画面）」に集約。`/legacy/queue` は「旧キュー・非正本（正本は /queue）」と明記、`/tasks/import` リンク追加。
- **自動実行ハブ化**: `/queue` ページ上部に 作業予約(`/prompt-queue`)・実行履歴(`/logs`) への導線を追加。
- guide FAQ（下タブ構成／自動実行キューはどこ）・operating-model（画面構成・変更履歴・frontmatter）更新。
- 先に登録した実装タスク `pq-mqd3acmp-k0tp3r` を `completed` に更新（定時 Factory の重複実行防止）。

## 3. 変更ファイル
components/navigation/BottomNav.tsx / app/legacy/page.tsx / app/queue/page.tsx / app/guide/page.tsx / docs/operations/current-operating-model.md

## 4. 検証（Fable独立）
- tsc 0 / lint 0 / build 成功。
- 新下タブ描画: ホーム/ToDo/Project/目標/自動実行/その他。
- 全主要ルート 200（/ /decide /portfolio /goal-planner /queue /legacy /prompt-queue /revenue /guide /logs）。
- `/legacy` ハブに Revenue（収益マイルストーン）/ 運用ガイド / 作業予約（Prompt Queue）導線を確認（orphan なし）。
- `/queue` に 作業予約 / 実行履歴 導線を確認。

## 5. 未対応 / 残課題
- iPhone実機での6タブ表示崩れ・到達確認。
- 「自動実行」タブを真のハブ画面化（実行中/失敗/履歴の集約表示）するかは次イテレーション。
- 重複ページ（旧 /inbox ↔ /decide、旧 /projects ↔ /portfolio、/legacy/queue）の**廃止可否はユーザー判断**（今回は導線整理のみ・削除なし）。

## 6. 次にやるべきこと
- iPhone実機確認。
- 旧画面の廃止/統合の最終判断（棚卸し review 20260614-101321 の対応方針表に従って）。

## ChatGPT レビュー依頼文
```
対象: progress モバイル下タブ再編（runId 20260614-105125 / commit f4fa2f9）
変更: components/navigation/BottomNav.tsx, app/legacy/page.tsx, app/queue/page.tsx, app/guide/page.tsx, docs/operations/current-operating-model.md
背景: 自動実行キュー(/queue)がモバイル下タブに無くiPhone未到達→下タブをホーム/ToDo/Project/目標/自動実行/その他に再編。Revenue/運用/作業予約は/legacyハブへ。
観点:
1. 6タブ構成(自動実行を主要化)とその他=/legacyハブへの集約は、iPhoneで迷わない導線になっているか。
2. Revenue/運用を主要タブから外した判断の是非（毎日見るならタブに戻すべきか）。
3. 「自動実行」タブを/queue単体でなく実行中/失敗/履歴の集約ハブ画面にすべきか。
4. 旧画面(旧inbox/旧projects/legacy queue)の廃止/統合の優先度。
```
