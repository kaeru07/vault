---
date: 2026-06-13
task: progress「レビュー用コピー」機能の実装（Codex実装→Fable検証・本番反映）
runId: 20260613-013109
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260613-001054]
commitHashes: [b77c4b7]
reviewFileCommit:
---

# progress「レビュー用コピー」機能 実装+検証

## 1. 作業目的

設計レビュー（runId 20260613-001054 / [[2026-06-13_progress-review-copy-design]]）で確定した「司令塔ホームから現在状態をMarkdownでワンタップコピーしChatGPT/Fableレビューへ貼る」機能を、Codexに実装させてFableが検証・本番反映する。

## 2. 実施内容

- Codex（threadId 019ebc9b-f9e2-7d70-ac0f-714097a3a415）へ設計プロンプトをそのまま投入し5タスクを実装
- Fableが検証: 変更範囲のdiff確認 / コードレビュー（既存ビルダー再利用・2タップ式・フォールバックの遵守確認）/ tsc・lint・build / pm2再起動後のライブAPI・ホーム確認 / 機密スキャン / commit・push

## 3. 変更ファイル

- progress/lib/review-copy.ts（新規）— 14セクションMarkdown生成。RECENT_RUN_DAYS=7 / RECENT_RUN_MAX=10 / PROJECT_MAX=8 / TOTAL_CHAR_BUDGET=12000、超過時の段階切り詰め、機密マスキング最終パス
- progress/app/api/operations/review-copy/route.ts（新規）— 読み取り専用GET
- progress/components/review-copy/ReviewCopyButton.tsx（新規）— 2タップ式モーダル、コピー成功✓/失敗時readonly textarea全選択フォールバック
- progress/app/page.tsx — 日付行直後にボタン1個（既存表示変更なし）
- progress/app/guide/page.tsx — FAQ2件・フロー図3箇所・手動起票の注記
- progress/lib/command-center.ts — TERMS.reviewCopy 追加
- progress/docs/operations/current-operating-model.md — frontmatter+本文+用語表

## 4. 検証結果

- tsc --noEmit / next lint / next build: すべてOK
- ライブAPI（pm2 restart後）: 200・14セクション・charCount 3,840（実データ・JST表示正常・直近作業に設計レビューrun自体が出現し正確性を確認）
- ホーム: 200・「レビュー用コピー」ボタン出力確認
- 変更範囲: 許可リスト内のみ。data/real/ への書き込み追加なし（diffの data差分はアプリ稼働由来）
- 機密スキャン: diffにパターンヒットなし
- GitHub: ny01 commit b77c4b7 / pushed

## 5. 未対応

- iPhone実機でのコピー動作・モーダル操作感（CLI環境のため人間確認待ち）
- セクション別コピー（V2スコープ・意図的に未実装）
- レビュー結果をprogressへ戻す閉ループ（人間がInbox手動起票の運用。guideに明記済み）

## 6. 危険ポイント

- 機能は完全読み取り専用でデータ書き込みなし・破壊リスク極小
- pm2 restart progress を1回実施（新ビルド反映のため・全ページ200確認済み）
- Codexのサンドボックスでは listen EPERM のためdevサーバ検証不可 → Fable側でライブ検証して補完

## 7. 次にやるべきこと

- iPhone Safariで 司令塔→📋レビュー用コピー→全体をコピー の2タップ動作を確認
- 実際にChatGPT/Fableへ1回貼ってレビューを回し、セクション過不足を観察（V2判断の材料）

## 8. ChatGPT レビュー依頼文

```
progressアプリに実装した「レビュー用コピー」機能のレビューをお願いします。runId: 20260613-013109 / commit b77c4b7（kaeru07/ny01）

実装内容: 司令塔ホームの日付行付近に「📋 レビュー用コピー」ボタンを追加。タップでモーダルが開き GET /api/operations/review-copy をfetchしてプレビュー、「全体をコピー」タップで全文コピー（iOS Safariのユーザージェスチャ制約対応の2タップ式）。失敗時はreadonly textareaで手動コピー。本文は14セクション（工場状態/今日の判断/Inbox内訳/Now-Next-Later/Project/Goal/Revenue/最近の作業/未実装・保留/危険・注意/次回優先/相談欄）、12,000字バジェット、機密マスキング付き。完全読み取り専用でInboxカードは作らない。

検証済み: tsc/lint/build OK、本番反映後ライブAPIが実データで14セクション3,840字を返却、ホーム表示OK。未検証: iPhone実機のコピー動作。

確認したい観点:
- 実データ3,840字は外部レビューの情報量として過不足ないか（特に「危険・注意」が直近10run依存で見逃しがないか）
- needs_followupとユーザー判断待ちの表現が、レビュアーが優先度判断できる粒度になっているか
- 2タップ式UXの改善余地（モーダルを開いた時点でのプリフェッチ等）
- V2（セクション別コピー/Inbox起票閉ループ）に進む前に観察すべき運用指標は何か
```
