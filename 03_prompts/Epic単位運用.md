---
title: Epic 単位運用ルール（自動化系 Issue の集約と進行）
type: workflow-ops
issue: kaeru07/vault#42
created: 2026-05-20
updated: 2026-05-20
status: active
relatedIssues: [kaeru07/vault#25-#39]
---

# Epic 単位運用ルール

> Issue #42。細かい Issue を 1 件ずつ進める運用から、**親 Epic 単位でまとめて進める運用**へ移行する。
> 自動化関連 Issue が細かく分かれすぎて vloop の作業量が小さくなり、同じ判断材料の Issue が重複する状態を解消する。

## 1. 方針（厳守）

- **細かい ToDo を増やしすぎない**: 同じ目的の作業は親 Epic に集約
- **vloop は Epic 単位の進捗を優先**（1 サイクル 1 Issue は維持、ただし Epic 内の関連 Issue は連続して進める）
- **1 回の vloop で 5〜10 件相当の関連作業を Epic 単位で進める**
- **レビューは原則 Epic 単位**（個別 Issue ではなく Epic 全体の整合を見る）
- **新規 ToDo はまず既存 Epic に紐づける**（独立 Issue を増やさない）

## 2. Epic 定義（Issue #42 本文準拠）

### Epic A: 情報収集基盤
- research-run / cron / @reboot 時実行 / daily 保存 / 失敗時継続 / 実行ログ / 1 日分実行テスト
- **収益化インパクト**: medium（実収益の前提となる原料供給）
- **実装着手前提**: ChatGPT/人間が方向性承認 → scrape-lab-v2 拡張 or 新規リポジトリ

### Epic B: 案生成基盤
- idea-run / 30 案生成 / 重複除去 / スコアリング / 上位 5 件抽出
- **収益化インパクト**: medium
- **実装着手前提**: Epic A 稼働後（daily 出力が必要）

### Epic C: 候補昇格と承認待ち
- 上位案の candidate 化 / ChatGPT 承認待ち更新 / approve・hold・reject 導線 / decision-log 連携
- **収益化インパクト**: medium〜high（承認 → progress → 実装の入口）
- **実装着手前提**: Epic B 稼働後（idea_pool 出力が必要）

## 3. 既存 Issue 分類表

| Issue | タイトル | Epic | 状態 |
|---|---|---|---|
| #25 | 無料トレンド情報収集エンジンを定期実行する | **A** | 設計済（コメント済） |
| #29 | 無料情報源ごとの取得方法と難易度を調査する | **A** | 設計済（コメント済） |
| #30 | daily 研究データの保存形式を先に固定する | **A** | 設計済（コメント済） |
| #32 | Google Trends・Google Play・YouTube で 1 日分の情報収集テスト | **A** | 計画済（コメント済） |
| #31 | 1 日分だけ情報収集→30 案生成を手動テスト | **A**（一部 B 含む） | 計画済（#32 同主旨） |
| #33 | 毎日定期調査を自動実行して daily へ保存する | **A** | #25 と重複（コメント済） |
| #36 | research-run と idea-run を cron + @reboot で自動実行する | **A**（B 含む） | 設計済（コメント済） |
| #37 | research-run 失敗時の継続ルールとログを作成する | **A** | 設計済（コメント済） |
| #38 | research-run 失敗時の継続ルールとログを作成する | **A** | #37 完全重複 |
| #39 | research-run / idea-run 実行ログ一覧を作成する | **A**（B 含む） | 設計済（コメント済） |
| #26 | トレンド情報から案を 30 件自動生成する | **B** | 設計済（コメント済） |
| #34 | daily から案 30 件を自動生成して idea_pool へ保存する | **B** | #26 と重複（コメント済） |
| #27 | 案プールから candidate を自動昇格するルールを作成する | **C** | 設計済（コメント済） |
| #35 | idea_pool から上位候補を自動抽出して承認待ちへ追加する | **C** | #27 と重複（コメント済） |
| #28 | 情報収集→案生成→candidate→承認待ちまでを完全自動化する | **A+B+C 統合** | Runbook 作成済（コメント済） |

> 全 15 Issue が既に「設計済 / コメント済」状態。Epic 単位でクローズ判断する候補。

## 4. 重複 Issue クローズ候補（同時クローズで OK）

| 残す Issue | duplicate クローズ候補 | 理由 |
|---|---|---|
| #25 | #33 | #33 は #25 の実装フェーズ細分化版 |
| #26 | #34 | 同上 |
| #27 | #35 | 同上 |
| #37 | #38 | 完全重複（タイトル・本文同一） |
| #32 | #31 | #31 と #32 は同主旨（1 日分テスト） |
| #6 | #13 | #13 は #6 を優先する依頼 Issue |

> ユーザーがまとめて duplicate クローズしてよい候補。残す側は本ファイル「Epic 整理」を完了の証跡として close 可能。

## 5. vloop での Epic 単位優先（運用変更）

vloop が次に処理する 1 件を選ぶときの優先順位（Issue #42 を反映）:

1. **Epic 跨ぎの統合 Issue**（#28 / 本 #42 のような全体整理）
2. **未着手 Epic 内の最も上流の Issue**（A → B → C の依存順）
3. **Epic 内で完了条件が未充足の Issue**
4. **Claude 実行対象外 Issue は除外**（[[claude-commands/vloop]] / Issue #41）

## 6. レビューは Epic 単位で行う方針

ChatGPT がレビューする際は、Issue 単位ではなく **Epic 単位でまとめてレビュー**する:

- Epic 単位レビュー観点:
  - Epic 全体の完了条件充足度
  - Epic 内の重複 Issue 整理状況
  - Epic 跨ぎの整合性（A→B→C の入力出力連鎖）
  - 実装着手の方向性承認可否（approve / hold / reject）
- 個別 Issue のレビューは Epic レビュー内のサブ項目として扱う
- レビューキュー（[[../20_reviews/_review_queue]]）にも将来「Epic 単位」エントリを設ける選択肢あり（仮置き）

## 7. 新規 ToDo の起票ルール（ChatGPT 側）

新規 Issue を起票するときの判断:

1. 既存 Epic（A/B/C）に紐づくか確認
2. 紐づくなら**新規 Issue を立てずに既存 Epic 内 Issue にコメント追記**を優先
3. それでも独立 Issue が必要なら起票し、frontmatter or 本文に `epic: A|B|C` を明記
4. 完全に新規領域なら **新 Epic D 以降**を本ファイルに追加

> 既存 15 Issue で Epic A/B/C はすでに飽和気味。**新規追加より既存への集約**を優先。

## 8. Issue ラベル運用案（実装は人間判断）

GitHub Issue ラベルに `epic:A` / `epic:B` / `epic:C` を追加すれば、`gh issue list --label epic:A` で一括取得可能。

> ラベル追加は GitHub Web UI 操作なので**人間判断**。本ファイルは仕様提案まで。

## 9. 完了条件と現状（Issue #42）

| 完了条件 | 現状 | 達成手段 |
|---|---|---|
| Epic 整理ファイルができている | **完了**（本ファイル） | — |
| 既存 Issue の分類表がある | **完了**（§3） | — |
| vloop が Epic 単位で扱う方針 | **完了**（§5 + vloop.md 追記） | — |
| レビューも Epic 単位で行う方針が明記 | **完了**（§6） | — |
| commit/push 済 | 本ファイル commit 済 | — |

5 つすべて充足。

## 関連

- [[claude-commands/vloop]]（Epic 単位優先ルール追記）
- [[ChatGPTレビュー手順]]（#40 / Epic 単位レビューの ChatGPT 側手順）
- [[../05_monetization/案工場_完全自動化フロー]]（#28 / Epic A+B+C 統合 Runbook）
- [[../20_reviews/_review_queue]]
- Issue: kaeru07/vault#42 / 関連 #25-#39
