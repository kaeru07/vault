---
date: 2026-06-21
task: 互換データ(work-queue/work-candidates)廃止の参照棚卸し＋廃止方針を今日の判断へ
runId: 20260621-095414
targetApp: progress
monetizationImpact: none
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: []
---

# 2026-06-21 互換データ廃止の棚卸し＋判断エスカレーション

## 1. 作業目的
- 集中作業モードで active[low]ゴール「work-queue/work-candidates等の互換データを段階的に廃止する」(goal-mqluko5l-ixkhf)に着手。ユーザー指示「微妙なところは今日の判断に追加して承認できるように」を実践。

## 2. 実施内容（調査＋エスカレーション）
- 参照棚卸し(grep): work-queue.json/work-candidates.json は operations-store(3 read)・session-reader/writer・app/api/queue・app/tasks・app/morning・app/radar・app/legacy/queue・SystemSpecification が依存。データは data/real(83KB/26KB)。正本は auto-queue だが旧 reader が一部画面(radar/morning/legacy)を支えている。
- 判断: 単純削除は既存機能(radar/morning/legacy)を壊すリスク＋人間判断が必要なため**自走せず**、`【要判断】互換データ(work-queue/work-candidates)の廃止のやり方を決める` を proposed ゴール(source=focus_decision)として**今日の判断(ゴール承認)へ追加**。
  - 選択肢 A: 現状維持(二重管理残る) / B: 依存ページを auto-queue へ移植後に削除 / C: legacy ページごと撤去。

## 3. 変更ファイル
- なし（調査＋判断エスカレーションのみ・コード変更なし）

## 4. 検証結果
- grep 棚卸しで依存箇所を確定。proposed ゴール追加を確認(ゴール承認に表示)。

## 5. 未対応
- 廃止の実作業（移植 or 撤去）は、ユーザーが方針(A/B/C)を承認/選択してから。

## 6. 危険ポイント
- 互換データの単純削除は radar/morning/legacy/queue を壊しうる（だから自走しない）。

## 7. 次にやるべきこと
- ユーザーが【要判断】ゴールで方針を選択 → その範囲で移植/撤去を実施。

## 8. ChatGPT レビュー依頼文
````text
対象: progress 互換データ(work-queue/work-candidates)廃止
runId: 20260621-095414 / decision goal 追加(ゴール承認)
依存: operations-store/session-reader,writer/api queue/tasks/morning/radar/legacy/queue。
正本はauto-queueだが旧readerが一部画面を支える。廃止のやり方を A現状維持/B移植後削除/
Clegacyごと撤去 で要判断。確認観点: どの方針が安全か / radar・morningはauto-queueへ移植可能か。
````
