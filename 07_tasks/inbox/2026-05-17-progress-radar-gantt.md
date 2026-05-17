---
title: progress 案件レーダー・ガントチャート追加
project: progress
status: pending_approval
priority: high
risk: medium
revenueImpact: high
assignee: claude
source: chatgpt
type: feature
targetApp: ny01/progress
targetPath: /root/company/apps/ny01/progress
created: 2026-05-17
nextAction: Claude Codeで /radar 案件レーダー画面を実装する
estimate: 1-2h
---

# 背景

途中の案件がどこまで進んでいるか、あと何をやらないといけないかを把握しづらく、やる気が出なくて放置してしまう。

progressアプリを「ToDo管理」中心から、「案件状況の可視化」中心へ進化させたい。

# 目的

以下を一目で把握できる /radar 画面を追加する。

- 今何が止まっているか
- 次に何を1個やれば進むか
- 放置案件
- レビュー待ち案件
- 収益インパクト

# 実装対象

新画面:
/radar

名称:
案件レーダー

表示コンセプト:
ガントチャート + 案件カード

# 変更OK範囲

- app/radar/*
- components/*
- lib/*
- 必要なAPI追加
- app-progress.json
- execution-runs.json
- project-tasks.json
- work-queue.json

# 変更禁止範囲

- 既存Queueロジック破壊
- ExecutionRun投稿仕様変更
- 集中作業モード仕様変更
- /queue の既存導線削除
- 既存ToDoステータスの破壊的変更

# 画面要件

## 画面上部

「今やるべき1件」を表示する。

例:
今やる: Goal Planner反映確認
想定時間: 5分
理由: 収益インパクト高 / 3日放置中

## ガントチャート

縦軸:
案件

横軸:
日付

状態表示:
- 着手
- 進行中
- レビュー待ち
- 停止
- 放置
- 完了

## 案件バー押下

モーダル表示。

表示項目:
- 案件名
- 状態
- 進捗率
- 最終更新
- 放置日数
- 現在地
- 次の1手
- 推定時間
- 収益インパクト
- ブロッカー
- 関連ToDo
- 最新ExecutionRun

# 重要ルール

「次の1手」を1行固定で表示する。

例:
- Goal Planner画面確認
- localhost:3010でスマホ確認
- Vercel反映確認

絶対にToDo一覧を大量表示しない。

# 表示ルール

- 放置7日: 黄色
- 放置14日: 赤
- レビュー待ち: 青
- 収益高: 王冠アイコン
- 次の1手あり: 緑

# スマホ要件

iPhone最優先。

- 横スクロール可能なガントにする
- 案件名列は固定
- 日付部分は横スクロール
- sticky利用可
- 必要なら簡易仮想化
- ダークモードで見やすくする

# Claude Code向け作業方針

## secretary

役割:
現状progress構造整理

出力:
- 現状データ流れ
- 追加不足情報
- 仮説

禁止:
実装しない

## researcher

役割:
既存ファイル調査

最低確認:
- work-queue
- project-tasks
- execution-runs
- app-progress

出力:
- 案件状態判定方法
- 放置日数算出方法
- 次の1手候補生成方法

禁止:
推測だけで決めない

## architect

役割:
ガントデータ構造設計

出力:
- 新規型
- 変更ファイル
- リスク

重要:
既存データから自動算出優先。
手入力項目を増やさない。

## coder

役割:
実装

重要:
既存データから以下を自動生成する。
- 現在地
- 放置日数
- 次の1手

## reviewer

確認:
- TypeScript
- build
- lint
- スマホ
- 横スクロール
- ダークモード
- 3010表示
- 既存Queue動作

# 完了条件

- /radar追加
- ガント表示
- 放置警告
- 次の1手表示
- 収益表示
- モーダル
- iPhone表示崩れなし
- build成功
- TypeScriptエラー0

# 最終報告に含めること

1. 変更ファイル一覧
2. ガント生成ロジック
3. 次の1手決定ロジック
4. 放置判定ルール
5. 未解決事項
6. 次の改善候補
7. progressレビュー用コピーに貼れる短い報告
