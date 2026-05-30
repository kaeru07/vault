---
title: vloop_queue（vloop実行対象）
type: vloop-queue
created: 2026-05-25
updated: 2026-05-25 (vloop11)
status: active
source_issue: kaeru07/vault#87
tags: [vloop, ToDo, 承認, 案件別, 自動実行]
---

# vloop_queue（vloop実行対象）

> [!important]
> vloop が ToDo として扱うのは、原則この2つだけ。
>
> 1. このページの `[x]` が付いている項目
> 2. GitHub Issue で ChatGPT から新規追加された未反映ToDo
>
> それ以外の作業は、vloop が勝手に実行しない。

> [!important] 自動実行ルール
> vloop は、このページの `[x]` が付いているToDoを、可能な限り上から順に最後まで実行する。
> 作業中に都度ユーザー確認を求めない。
> ユーザー確認が必要なものは、実行せず `[ ]` の承認待ちに残す。

---

## 役割分担

| 場所 | 役割 |
|---|---|
| `20_reviews/vloop_queue.md` | 今回/次回の vloop 実行対象 |
| `20_reviews/案件別ToDo一覧.md` | 全ToDoの正本・台帳 |
| GitHub Issue | ChatGPTからToDo追加する入口・内部作業チケット |
| `20_reviews/完了ToDoログ.md` | 完了したToDoの履歴 |

---

## vloop開始時ルール

1. まずこの `vloop_queue.md` を読む
2. `[x]` の項目だけ実行する
3. `[ ]` の項目は承認待ちとして実行しない
4. GitHub Issue に ChatGPT から新規追加されたToDoがある場合は、まず Vault へ反映する
5. 反映先は原則 `20_reviews/案件別ToDo一覧.md` と、この `vloop_queue.md`
6. 承認が必要なものは `[ ]` のまま置く
7. 承認不要で目的達成に必要なものだけ `[x]` として vloop 対象に入れる
8. `[x]` の作業は、途中で都度ユーザー確認せず、完了または停止条件に当たるまで続ける

---

## チェックボックスの意味

| 表示 | 意味 | vloop実行 |
|---|---|---|
| `[x]` | 実行承認済み / 承認不要で実行可 | する |
| `[ ]` | 承認待ち | しない |

> [!warning]
> 破壊的変更、外部公開、課金/API利用、新規アプリ本格着手、大きな方針変更は、必ず `[ ]` の承認待ちにする。

---

## 自動実行中に止まってよい条件

以下に該当する場合だけ、vloop は止まってよい。

- `[ ]` の承認待ちに該当する作業へ進む必要が出た
- 破壊的変更、削除、外部公開、課金/API利用、認証情報利用が必要になった
- Git pull / commit / push / sync に失敗した
- 仕様矛盾やデータ欠損で、勝手に判断すると危険
- 実行環境がない、または検証不能
- Claude利用制限などで物理的に継続不能

止まる場合は、次を必ず残す。

- どのToDoで止まったか
- なぜ止まったか
- 次にユーザーが判断すること
- `案件別ToDo一覧.md` / `vloop_queue.md` のどちらを更新したか

---

# 案件別キュー

## Vault運用

### 実行承認済み / 承認不要（vloop9-11 で消化済 / 継続運用項目）

- [x] 案件別ToDo一覧を正本として維持し、Issueだけに残っている新規ToDoをVaultへ反映する（継続運用）
- [x] 完了ToDoを `20_reviews/完了ToDoログ.md` へ移動する（vloop9 で 5 件 / vloop10 で 4 件 / vloop11 で 2 件追記）
- [x] `00_START_HERE.md` から `vloop_queue.md` と `案件別ToDo一覧.md` へ辿れるようにする
- [x] `20_reviews/次に実体化するToDo.md` を正本扱いから外し、非推奨/参照用にする
- [x] 重複ToDoを目的ベースで統合し、統合元は merged 扱いにする
- [x] **旧運用フォルダ個別ファイル注記追加（A-4-1 / vloop9 完了 / Issue #59 残作業解消）**
- [x] **#68 Mermaid テンプレを candidate-001/005/006/007 本体へ反映（vloop10 完了 / 4 ファイル）**
- [x] **chatgpt/ フォルダ運用ルール README v1 確定（vloop10 完了）**
- [x] **Vaultの見方ガイド簡略化（vloop11 完了 / 1 分索引 + 最新候補状況 + 分割方針 / 破壊的削除なし）**
- [x] **全 .md 横断「存在しないパス案内」grep 検査（vloop11 完了 / 185 件検出 / カテゴリ A-F 分類）**

### 承認待ち

- [ ] 旧運用ファイルを削除する

---

## 麻雀アプリ

### 実行承認済み / 承認不要

- [x] `02_apps/mahjong.md` を司令塔ページとして整理する
- [x] 麻雀画像解析/鳴き読み問題生成の現在地を案件別ToDo一覧へ反映する
- [x] **candidate-001 本体と補助ファイルの分かりづらい用語をユーザー向け日本語に直す（A-1 / vloop9 完了 / 4 ファイル）**

### 承認待ち

- [ ] 麻雀画像解析を本格実装フェーズへ進める
- [ ] 公開用の独自類題生成まで進める

---

## トークン速度・LLMベンチ

### 実行承認済み / 承認不要

- [x] トークン速度・LLMベンチ案件の司令塔ページを作る
- [x] APIなしでできる範囲、手入力/ログ貼り付け/JSON保存の方針を整理する
- [x] **candidate-005 本体と補助ファイルの用語注記追加（A-2 / vloop9 完了 / 4 ファイル）**

### 承認待ち

- [ ] 実際に最小試作アプリを作る

---

## 収益化・市場調査

### 実行承認済み / 承認不要

- [x] 案の情報源と採用理由ページへ主要候補を追記する
- [x] **candidate-006 / 007 の判断するための資料一式（補助 4 ファイル × 2 = 8 ファイル）作成（A-3-1 / vloop9 完了）**
- [x] **N-03 LLM Chooser 判定基準客観化方針 1 ページ完成（vloop10 完了 / 3 層構造）**
- [x] **N-04 Vault Search Cheatsheet 統合方針 A/B/C 比較整理（vloop10 完了 / Claude 推奨案 B）**

### 承認待ち

- [ ] Hermes Agent × Codex の市場調査→実装→改善サイクルを本格運用へ入れる

---

## AI工場オペレーションセンター（Epic #91）

> 作業対象: `/root/company/apps/ny01/progress`
> 詳細は `05_monetization/AI工場_operations実装Runbook.md` を参照。
> 継続実行ポリシー: `03_prompts/継続実行ポリシー.md`（autonomous・途中でユーザー確認しない）

### Vault側（完了済み）

- [x] 継続実行ポリシー確定（`03_prompts/継続実行ポリシー.md`）
- [x] seed データ作成（`05_monetization/seeds/`）
- [x] 実装 Runbook 作成（`05_monetization/AI工場_operations実装Runbook.md`）

### Progress app 実装（VPS vloop 2026-05-30 完了済み / ny01 commit 6291e14）

- [x] **[E91-P1] 前提確認** — 完了（data/real/ ls確認・pm2確認・tsc確認）
- [x] **[E91-P2] data/real/epics.json 作成** — 完了（epic-91 シード投入）
- [x] **[E91-P3] data/real/approvals.json 作成** — 完了（空配列）
- [x] **[E91-P4] data/real/operational-decisions.ndjson 作成** — 完了（空ファイル）
- [x] **[E91-P5] lib/types/operations.ts 新規作成** — 完了（Epic/Approval/OperationalDecision/HealthSummary 等の型定義）
- [x] **[E91-P6] lib/operations-store.ts 新規作成** — 完了（getEpics/updateEpic/getPendingApprovals/decideApproval/computeHealthSummary）
- [x] **[E91-P7] app/api/operations/health/route.ts 新規作成** — 完了
- [x] **[E91-P8] app/api/operations/epics/route.ts 新規作成** — 完了
- [x] **[E91-P9] app/api/operations/approvals/route.ts 新規作成** — 完了（GET/POST）
- [x] **[E91-P10] app/api/operations/autoexec/route.ts 新規作成** — 完了（pm2 start/stop/restart）
- [x] **[E91-P11] app/operations/page.tsx 新規作成** — 完了（モバイル縦1カラム / ヘルスバー・自動実行・承認カード・Epic進行）
- [x] **[E91-P12] ナビゲーションに /operations リンク追加** — 完了（ラベル「工場」）
- [x] **[E91-P13] .env.local に VLOOP_PM2_NAME 追記** — 完了
- [x] **[E91-P14] 型チェック・ビルド確認** — 完了（tsc noEmit + build 通過）
- [x] **[E91-P15] 本番反映・動作確認** — 完了（pm2 restart progress）
- [x] **[E91-P16] Vaultにレビューファイル生成** — 完了（vloop運用-progress統合-役割整理.md 生成 / _review_queue.md 追記）

### 承認待ち

- [ ] Phase2: cron/pm2スケジュール自動起動（毎朝11:00 → /api/operations/autoexec start）
- [ ] Phase2: ExecutionRunレビュー→自動ToDo生成（source=execution_review）

---

## vloop終了時ルール

vloop終了時は、以下を更新する。

- 実行した `[x]` 項目の結果
- 完了したものは `完了ToDoログ.md` へ移動
- 継続が必要なものは `案件別ToDo一覧.md` に状態付きで残す
- 新しく発生したToDoは `案件別ToDo一覧.md` へ反映
- 承認が必要なものは、このページに `[ ]` で残す
- GitHub Issueだけに残っているものがないか確認する
- 未完了の `[x]` が残った場合は、残理由と次回継続対象を明記する

---

## 禁止事項

- `[ ]` の項目をvloopが勝手に実行しない
- `vloop_queue.md` にない作業をvloopが勝手に実行しない
- Issueだけ作ってVaultへ反映しない
- 承認が必要な作業を勝手に `[x]` にしない
- チェックボックスなしで承認待ちを表現しない
- `[x]` の作業中に、停止条件に当たらない通常確認をユーザーへ求めない
