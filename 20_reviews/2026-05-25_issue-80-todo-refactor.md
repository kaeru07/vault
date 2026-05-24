---
date: 2026-05-25
task: Issue #80 案件別ToDo運用を実ファイルへ反映する実行パック
runId: 20260525-vloop9-issue-80-execution
targetApp: company-meta / obsidian-vault
monetizationImpact: medium
theme: [obsidian, workflow, vault]
relatedRunIds: [20260525-001259, 20260524-234509]
commitHashes: []
---

# Issue #80 案件別ToDo運用を実ファイルへ反映する実行パック

## 1. 作業目的

- Issue #80 の Phase 1-5 を 1 サイクルで実ファイル反映
- 新しい Issue / ToDo を増やさず、既存設計を実体化する
- 案件別ToDo一覧.md を正本として機能する状態にする
- 完了 ToDo を完了ログへ移動し、未完了一覧から消す
- START_HERE 導線を 1-2 タップ化
- 重複 ToDo を目的ベースで統合

## 2. 実施内容

### Phase 1 完了ToDoログ.md 新規（#74）

- `20_reviews/完了ToDoログ.md` 新規作成
- vloop1-8 で達成した 20+ 件の完了 ToDo を案件別に整理し移動
- セクション: Vault 運用基盤 / 試作ループ / candidate 正規化 / 検討・調査 / vloop8 / vloop9

### Phase 5 案件別ToDo一覧.md 全面リファクタ（#76 / #80）

- 4 セクション構造で全面書き換え:
  - **A. 実行ToDo**（Claude が次に手を動かす / 承認不要 / チェックボックスなし）
  - **B. あなた確認待ち**（Claude 完了済 / あなた操作で前進）
  - **C. 承認待ち**（チェックボックス `[ ]` 使用 / ChatGPT + あなた判断）
  - **D. 完了移動候補**（次回 vloop で完了ToDoログへ移す）
- 重複統合: candidate-001/005 補助 md 用語注記 / Vault 残作業 / 候補-006/007 補助 8 ファイル を 1 行ずつに集約
- 候補-001/005/006/007 方向性レビューを C セクションで一覧化（チェックボックス 4 件）

### Phase 2 START_HERE 導線整理（#78）

- 入口 6 → 4 件に絞り込み（1-2 タップ化）
- ① 案件別ToDo一覧（正本）/ ② 承認判断 / ③ 候補一覧 / ④ Epic 進捗 の順
- 「次に実体化するToDo」の最優先位置を削除（非推奨化）
- 「さらに掘る」に完了ToDoログ リンク追加

### Phase 5 次に実体化するToDo.md を非推奨化（#76）

- frontmatter `status: deprecated` 追加
- ファイル先頭に `[!warning] このページは非推奨です` callout
- 正本は案件別ToDo一覧.md と明記
- 過去内容は履歴として保持（破壊的削除なし）

### Phase 3 埋もれToDo回収（#77）

- 過去 vloop1-8 サマリーから partial_done / planned_only / reviewed_followup を抽出
- 主要回収項目: 候補-006/007 補助 8 ファイル / candidate 本体用語注記 / Vault 残作業（個別注記 / 全 .md grep / 見方ガイド分割 / chatgpt 運用ルール）/ Mermaid テンプレ candidate 反映 / Hermes 最小運用フロー（blocked）/ cron 投入判断

### Phase 4 重複統合（#79）

- 「次に実体化するToDo」の優先 1-6 と 案件別ToDo一覧 §5 収益化案の重複を統合
- 候補-001/005 補助 md 用語注記を 1 行ずつに集約（前は分散）
- ChatGPT 承認待ち.md / candidate ChatGPT 承認パック への入口を C セクション 1 箇所に集約

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `20_reviews/完了ToDoログ.md` | 新規（#74）|
| `20_reviews/案件別ToDo一覧.md` | 全面リファクタ（#76 / #80）|
| `00_START_HERE.md` | 入口 6 → 4 件 + 完了ToDoログリンク追加（#78）|
| `20_reviews/次に実体化するToDo.md` | 非推奨化 callout 追加（#76）|
| `20_reviews/2026-05-25_issue-80-todo-refactor.md` | 本ファイル新規 |
| `20_reviews/_review_queue.md` | 先頭追加 |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| 案件別ToDo一覧.md が正本として機能 | ✅ 4 セクション構造 / 用語注記済 |
| 完了 ToDo が完了ログへ移動 | ✅ 20+ 件移動 |
| START_HERE から 1-2 タップ | ✅ ① 案件別ToDo一覧（1 タップ）→ A セクション（2 タップで到達）|
| 埋もれ ToDo 回収 | ✅ 過去 vloop1-8 から partial_done / planned_only 抽出済 |
| 重複統合 | ✅ 案件別ToDo一覧 と 次に実体化するToDo の重複を案件別ToDo一覧へ統合 / 次に実体化するToDo は非推奨化 |
| 承認待ちと実行 ToDo の整理 | ✅ A 実行（チェックボックスなし）と C 承認（チェックボックス）を分離 |
| iPhone Obsidian で見やすい | ✅ Callout + 表 + frontmatter で構造化 |
| 新規 Issue / ToDo を増やしていない | ✅ 既存 Issue #74-#80 の実体化のみ |
| 設計だけで完了扱いしていない | ✅ 6 ファイル変更 + 完了 20+ 件移動 + 重複統合 |
| 完了 ToDo を未完了一覧へ残し続けていない | ✅ 完了ToDoログへ移動 |
| 機密スキャン | ✅ 実値なし |
| ob sync / GitHub push | 次フェーズで実行 |

## 5. 未対応

- 個別 ToDo の中身（候補-006/007 補助 8 ファイル / candidate 本体用語注記 / Vault 個別ファイル注記 等）は本サイクルでは着手していない（次サイクルで A セクション上から処理）
- これは Issue #80 が「**運用構造の実ファイル反映**」を求めているため、個別 ToDo 実装は別サイクル

## 6. 危険ポイント

- ❌ なし（破壊的削除なし / 既存ファイル削除なし / DB 操作なし）
- 次に実体化するToDo.md は **非推奨化のみ**（ファイル削除はしていない / 履歴として保持）
- 案件別ToDo一覧 の旧 6 案件構造は履歴として完了ToDoログに残している（情報損失なし）

## 7. 次にやるべきこと

1. ChatGPT が _review_queue.md 先頭の本レビューをレビュー
2. ユーザーが iPhone Obsidian で 00_START_HERE → 案件別ToDo一覧 が 1-2 タップで開けるか確認
3. 次サイクルから vloop は A セクション上から処理（候補-006/007 補助 8 ファイル等）
4. C セクションの ChatGPT 承認 4 件（candidate-001/005/006/007）の方向性レビュー

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です（9 サイクル目）。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: Issue #80 案件別ToDo運用を実ファイルへ反映する実行パック

## できるようになったこと
- 完了ToDoログ.md 新規（vloop1-8 で達成した 20+ 件を移動）
- 案件別ToDo一覧.md 全面リファクタ（A 実行 / B 確認待ち / C 承認待ち / D 完了移動候補 の 4 セクション）
- 承認必要なものだけチェックボックス化（C セクション 7 件）
- START_HERE 入口 6 → 4 件に絞り（1-2 タップ化）
- 次に実体化するToDo.md を非推奨化（status: deprecated / [!warning] callout）
- 重複統合（候補-001/005 補助 md 用語注記など）

## 確認したい観点
1. 4 セクション構造（A 実行 / B 確認待ち / C 承認待ち / D 完了移動候補）は妥当か
2. C 承認待ちのチェックボックス化は妥当か（A 実行はチェックボックスなし）
3. 「次に実体化するToDo」非推奨化（削除はしない）の判断は妥当か
4. 完了 ToDo を完了ToDoログへ移動するルールは持続可能か
5. START_HERE 入口 4 件への絞り込みは過剰削減ではないか
6. A セクション上から処理する vloop 運用は機能するか
7. 重複統合の粒度は妥当か（候補-001/005 補助 md 用語注記を 1 行に集約）

参考リンク:
- 20_reviews/案件別ToDo一覧.md（全面リファクタ）
- 20_reviews/完了ToDoログ.md（新規）
- 00_START_HERE.md（4 件入口）
- 20_reviews/次に実体化するToDo.md（非推奨化）
```

## 関連

- [[案件別ToDo一覧]]（リファクタ後 / 正本）
- [[完了ToDoログ]]（新規）
- [[次に実体化するToDo]]（非推奨）
- [[案件別ToDo運用ルール]]
- [[Vault蓄積運用ルール]]
- [[../00_START_HERE]]
- Issue: kaeru07/vault#80 / #74 / #76 / #77 / #78 / #79 / #70
