---
title: Issue 記述共通ルール（Claude 確認過多を減らす）
type: prompt-ops
issue: kaeru07/vault#17
created: 2026-05-19
updated: 2026-05-19
status: active
---

# Issue 記述共通ルール（Claude 確認過多を減らす）

> Issue #17。vloop/vrun 実行中の「確認しますか？」「承認しますか？」を減らし**自走率を上げる**ための、
> ChatGPT が Issue を書くときの共通ルール。vloop/vrun の確認禁止ルールと整合させる。

## 背景

Issue 本文に「承認」「選ぶ」「判断」などがあると、AI が停止・確認しやすい。
→ Issue 側を「推奨/仮説で自走できる」書式に統一し、AI 側ルール（[[claude-commands/vloop]] 確認禁止）と噛み合わせる。

## Issue 共通ルール（ChatGPT が Issue を書くとき）

1. **ユーザー確認を前提にしない**（「確認して」「どうしますか」を完了条件に書かない）
2. AI に **approved 化を求めない**（承認は ChatGPT＋人間。[[../05_monetization/ChatGPT承認ゲート標準]]）
3. AI に **progress 投入を求めない**（approved 後に人間が投入）
4. 「承認する」ではなく **「推奨する/比較する/選定案を出す」** と書く
5. 判断が必要な箇所は **「仮説で進めて記録」** と明記する（停止させない）
6. 完了条件は **成果物ベース**（「比較表」「推奨1件」「計画」等）で書く（「承認」を完了条件にしない）

## 推奨/仮説/人間判断待ち の標準書式

Issue・成果物で状態を表す語を統一:

| 状態 | 書き方 | 意味 |
|---|---|---|
| AI 推奨 | `推奨: <案>（承認ではない）` | AI が比較し勧めるが決定ではない |
| 仮説 | `仮説: <前提> → このまま進行` | 不明点を仮置きして自走（停止しない） |
| 人間判断待ち | `人間判断待ち: <論点>` | approved/公開/課金 等。AI は触れない |
| ChatGPT 承認待ち | `chatgpt_pending` | 判断材料完備（[[../05_monetization/ChatGPT承認ゲート標準]]） |

## 停止条件以外は確認禁止（再掲・AI 側）

- vloop/vrun は確認禁止ルールを既に内蔵（[[claude-commands/vloop]] / [[claude-commands/vrun]]※vrun は未移管・.claude/commands 側）
- 停止条件（外部公開/課金/広告/秘密情報/データ削除/破壊的変更/Issue とルールの明確矛盾/git失敗）**以外**では確認しない
- 迷ったら仮説で進め、最終報告の「仮説/未対応点/次の一手」に記録

## vloop/vrun への反映

- vloop.md は本ルールと整合済（確認禁止ルール＋承認ゲート相互参照を内蔵。Issue #18 で追記済）
- vrun.md も確認方針セクションで同等（停止例外以外は確認しない）
- 追加のコマンド改変は不要＝**Issue 側の書式を本ルールに合わせることで自走率を上げる**（責務を Issue 記述側へ）

## 既存 Issue テンプレへの反映

- 既存テンプレ: [[../90_templates/claude-task-template]] / [[../90_templates/chatgpt-approval-template]]（#19）
- 本ルールの「推奨/仮説/人間判断待ち書式」を Issue 起票時に適用（テンプレ側は #19 で承認待ち書式を導入済。タスク系テンプレへは次回改定時に反映）

## 未対応点 / 仮説
- vrun.md は未移管（.claude/commands 側の通常ファイル）。本ルールとの整合は内容上満たすが、移管はユーザー判断（既出）
- claude-task-template への書式反映は破壊を避け次回改定時に統合（仮説: 今回はルール明文化に留める）

## 次の一手
1. ChatGPT が Issue 起票時に本ルールの書式を使う
2. claude-task-template の次回改定で「推奨/仮説/人間判断待ち」欄を正式追加
3. Issue #20（vloop 承認系単語の扱い）と整合確認

## 関連
- [[claude-commands/vloop]] / [[../05_monetization/ChatGPT承認ゲート標準]] / [[../90_templates/chatgpt-approval-template]]
- Issue: kaeru07/vault#17（関連 #18 / #19 / #20）
