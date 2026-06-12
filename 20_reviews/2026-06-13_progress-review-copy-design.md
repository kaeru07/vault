---
date: 2026-06-13
task: progress「レビュー用コピー」機能の設計レビュー + Codex投入用プロンプト作成（実装なし）
runId: 20260613-001054
targetApp: progress
monetizationImpact: low
theme: [workflow, obsidian]
relatedRunIds: []
commitHashes: []
reviewFileCommit:
---

# progress「レビュー用コピー」機能 設計レビュー

## 1. 作業目的

ChatGPT/Fableへの作業レビューが現状スクショ貼り付けで、変更ファイル・検証結果・未実装・AI保留理由・次回自動実行予定などが抜けやすい。progressアプリに蓄積済みの現在状態をワンタップでMarkdownコピーできる機能を追加するため、実装前の設計レビューとCodex投入用プロンプトを作成した。実装はCodex最高モデルが行う。

## 2. 実施内容

- progress実コード調査: buildCommandCenter()（lib/command-center.ts）が必要データの約9割を既に集約済みであることを確認
- 既存類似機能との重複確認: AdhocReviewCopyPanel（キュー外アウトプット用・手入力ベース）とは役割が異なり共存可能と判定
- 設計判断: 置き場所=司令塔ホーム / 形式=Markdown単独 / UI=2タップ式モーダル（iOS Safariクリップボード制約対応）/ セクション別コピー=V2送り / データ集約=既存ビルダー再利用必須
- 足りない観点の指摘: iOSクリップボード制約・機密マスキング・データ鮮度表示・レビュー後の戻り道・運用ドキュメント4点セット更新・文字数バジェット
- Codex実装タスク5分解 + 投入用プロンプト作成

## 3. 変更ファイル

- obsidian-sync-vault/20_reviews/2026-06-13_progress-review-copy-design.md（本ファイル・新規）
- obsidian-sync-vault/20_reviews/_review_queue.md（未レビュー先頭に1件追記）
- progressアプリのコードは未変更（設計レビューのみ）

## 4. 検証結果

- build / tsc / lint: n/a（実装なし）
- progress POST: runId 20260613-001054 確認済み
- 機密スキャン: 本ファイルにAPIキー・トークン実値なし（パターン例示のみ）
- ob sync / GitHub push: 本作業サイクル内で実施

## 5. 未対応

- Codexによる実装本体（プロンプトは会話最終応答に記載済み）
- iPhone実機でのクリップボード動作確認（実装後）
- セクション別コピー（V2へ意図的に送り）

## 6. 危険ポイント

- 実装自体は読み取り専用でデータ書き込みなし・破壊リスク極小
- 注意: コピー内容に機密が混入しないよう、出力直前のマスキングパス（sk- / Bearer / -----BEGIN 等のパターン伏字化）を実装に含めた
- 表示ロジックとコピー整形ロジックの乖離（二重メンテ）が中期リスク → 既存ビルダー再利用を変更禁止条件としてプロンプトに明記

## 7. 次にやるべきこと

- Codex最高モデルへ投入用プロンプトを渡して実装させる
- 実装後: iPhone実機コピー確認 / 運用ドキュメント4点セット（guide・TERMS・フロー図・current-operating-model.md）更新確認
- 2週間運用後にセクション別コピー（V2）の要否を判断

## 8. ChatGPT レビュー依頼文

```
progressアプリ（Next.js 14 / ポート3010 / ファイルベースJSON）への「レビュー用コピー」機能の設計レビューをお願いします。runId: 20260613-001054

背景: Claude/Codex/Fableの作業結果をChatGPTにスクショで貼ってレビューしているが、変更ファイル・検証結果・未実装・AI保留理由・次回自動実行予定が抜けやすい。progressアプリに蓄積済みの現在状態（工場状態/Inbox/Project/Goal/Revenue/最近の作業）をワンタップでMarkdownコピーし、レビュー用チャットへ貼る機能を追加する。

確定した設計:
1. 置き場所: 司令塔ホーム（app/page.tsx）ヘッダ部に「レビュー用コピー」ボタン1個。タップでモーダル
2. UI: 2タップ式（①ボタン→モーダルでfetch+プレビュー ②コピーボタンでstateから同期コピー）。iOS Safariのクリップボードがユーザージェスチャ文脈必須のため、fetch直後の非同期writeTextを避ける設計
3. 形式: Markdown単独（JSON併記はV1ではしない）。全体約12,000字バジェット、直近7日/最大10runの窓
4. データ: 既存のbuildCommandCenter/buildInbox/buildQueueSplit/buildFactoryOutlookを再利用（新規集約ロジック禁止）
5. 分離: Inboxカード化しない（読み取り専用）/ Legacyの取り込み系は残す（import=書き込み、copy=読み取りの責務分離）/ 既存AdhocReviewCopyPanelも残す

確認したい観点:
- 2タップ式はiPhone運用の手数として許容範囲か（1タップ+クリップボードAPI工夫の代替案はあるか）
- 12,000字バジェットと直近7日窓は、ChatGPTレビューの情報量として過不足ないか
- 「レビュー後の戻り道」を人間のInbox手動起票に任せる設計で閉ループとして十分か
- セクション別コピーをV2に送った判断は妥当か
- 機密マスキングをMarkdown生成の最終パスに置くだけで防御として足りるか
```
