---
date: 2026-05-24
task: candidate-005 承認材料 4 ファイル完備（candidate-001 と同水準）
runId: 20260524-vloop3-candidate005-approval-pack
targetApp: company-meta / obsidian-vault
monetizationImpact: high
theme: [monetization, app-strategy, market-research]
relatedRunIds: [20260524-194403, 20260524-185950]
commitHashes: []
---

# candidate-005 承認材料 4 ファイル完備

## 1. 作業目的

- candidate-005 を candidate-001 と同水準の承認判断材料まで持っていく
- ChatGPT が候補-005 を方向性承認できる状態（公開ブロッカー / 7 日プラン / progress 投入設計 / ChatGPT 承認パック）を 1 サイクルで揃える
- vloop2 末の「次サイクルで candidate-005 の 7 日実行プラン + 公開ブロッカー + ChatGPT 承認パック化」を達成する
- candidate-001 と candidate-005 の**並走スケジュール**を可視化する

## 2. 実施内容

- `scenarios/candidate-005_公開ブロッカー.md` 新規（B1-B9 + 推奨解除順 + candidate-001 並走時の優先度）
- `scenarios/candidate-005_7日実行プラン.md` 新規（Day1-7 ToDo + candidate-001 との並走スケジュール表）
- `scenarios/candidate-005_progress投入設計.md` 新規（ExecutionRun フォーマット + 最初の 3 ToDo + 投入チェックリスト）
- `20_reviews/candidate-005_ChatGPT承認パック.md` 新規（§1-§14 / 比較 / メリデメ / 30 日プラン / 並走判断 / ChatGPT への質問観点 6 件）
- `scenarios/README.md` に candidate-005 補助ファイルセクション追加
- `idea_trace.md` §2 候補-005 のリンク欄と判断履歴を更新
- `00_START_HERE.md` candidate-005 セクションに承認材料 4 件のリンク追加

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `05_monetization/scenarios/candidate-005_公開ブロッカー.md` | 新規（B1-B9 / 推奨解除順 / 並走優先度） |
| `05_monetization/scenarios/candidate-005_7日実行プラン.md` | 新規（Day1-7 / candidate-001 並走表） |
| `05_monetization/scenarios/candidate-005_progress投入設計.md` | 新規（投入フォーマット / 3 ToDo / チェックリスト） |
| `20_reviews/candidate-005_ChatGPT承認パック.md` | 新規（§1-§14 自己完結） |
| `05_monetization/scenarios/README.md` | candidate-005 補助ファイルセクション追加 |
| `05_monetization/idea_trace.md` | §2 候補-005 リンク欄拡張 + 判断履歴に vloop3 追記 |
| `00_START_HERE.md` | candidate-005 セクションに承認材料 4 件のリンク追加 |
| `20_reviews/2026-05-24_candidate-005-approval-pack.md` | 本ファイル新規 |
| `20_reviews/_review_queue.md` | 未レビュー先頭追加 |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| 4 補助ファイルの形式 | ✅ candidate-001 補助ファイルと同形式（frontmatter / セクション構成 / 表項目）|
| approved 化禁止ルール遵守 | ✅ candidate-005 の status は `candidate` のまま（pending_approval にもしない / ChatGPT 承認待ち.md にもまだ追加していない） |
| 並走判断の整合性 | ✅ candidate-001 と candidate-005 が干渉しないことを 7 日プラン §並走スケジュール + 承認パック §8 で明示 |
| 機密スキャン | ✅ `api[_-]?key`/`sk-`/`Bearer`/`password`/`secret`/`-----BEGIN` 実値なし |
| build / typecheck / lint | n/a（Markdown のみ）|
| ob sync | 次フェーズで実行（最終応答前）|
| GitHub push | 次フェーズで実行 |

## 5. 未対応

- ChatGPT 承認待ち.md への candidate-005 ブロック追加（**意図的に未実施** — pending_approval ステータスは人間判断後にすべきため）
- iPhone 実機表示確認（候補-005 全体・ユーザー操作待ち）
- candidate-005 を `pending_approval` に上げるかの判断（ChatGPT レビュー後）
- 30 日プラン（Day8-30）の詳細化（次サイクル候補 / 公開承認後）
- ChatGPT 方向性承認 → 人間 status 確定 → progress 投入の一連の流れ実行

## 6. 危険ポイント

- ❌ なし（破壊的変更なし / 既存ファイル削除なし / DB 操作なし / 認証情報なし / 外部公開判断なし / approved 化なし）
- candidate-005 を**勝手に pending_approval / approved 化していない**（ルール厳守）
- ChatGPT 承認待ち.md への追加も**意図的に保留**（pending_approval ゲート通過は人間判断後）

## 7. 次にやるべきこと

1. ChatGPT が candidate-005_ChatGPT承認パック を方向性レビュー（§1-§14 / 質問観点 6 件）
2. ユーザー + ChatGPT が candidate-005 を pending_approval に昇格するか判断
3. 昇格後、Claude が ChatGPT 承認待ち.md に candidate-005 ブロック追加（candidate-001 と同形式）
4. ChatGPT 方向性承認 → 人間 status 確定 → [[../05_monetization/decision-log]] 追記
5. approved 後、最初の 3 ToDo を progress 投入（人間が実施）
6. Day1 から 7 日実行プランで実行（vault repo 内 / iPhone 実機 / Vercel）

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です（3 サイクル目・本日 3 回目）。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: vloop 2026-05-24 candidate-005 承認材料 4 ファイル完備（candidate-001 と同水準まで）

## できるようになったこと
- candidate-005 の公開ブロッカー（B1-B9）が candidate-001 と同形式で整理
- 7 日実行プラン（Day1-7）+ candidate-001 との並走スケジュール表
- progress 投入設計（ExecutionRun フォーマット + 最初の 3 ToDo + 投入チェックリスト）
- ChatGPT 承認パック §1-§14 が自己完結で読める
- candidate-005 は依然として status: candidate のまま（pending_approval にも approved にも上げていない）

## 確認したい観点（承認パック §14 と同じ）
1. candidate-005 を candidate-001 と並走させる判断は妥当か
2. scoreTotal 28 / 6 軸 25/30 のスコアリングは妥当か
3. 体感スコア計算式（tokensPerSec × 0.4 + (10/TTFT) × 4 + outputChars/100 × 0.2）の重みは妥当か
4. AI 開発者層を主市場に据えた判断は妥当か
5. B9 公開ベンチデータ取り込みルール（「手動取り込み + 出典必須」）は十分か
6. ChatGPT 承認後、人間が approved 化判断する流れは本ケースでも適用可か

## 加えて確認したい観点
- candidate-005 を pending_approval に上げる準備は整ったか（ChatGPT 承認待ち.md に追加する判断）
- candidate-001 の承認材料（5 ファイル）と candidate-005 の承認材料（4 ファイル）の差は妥当か
- 並走スケジュール（7 日プラン §並走スケジュール）の Day3-6 で両者の作業が並行可能と判断したのは妥当か

参考リンク:
- 05_monetization/scenarios/candidate-005_公開ブロッカー.md
- 05_monetization/scenarios/candidate-005_7日実行プラン.md
- 05_monetization/scenarios/candidate-005_progress投入設計.md
- 20_reviews/candidate-005_ChatGPT承認パック.md
- 05_monetization/scenarios/README.md（5 件テーブル + 補助ファイルセクション）
```

## 関連

- 前サイクル: [[2026-05-24_candidate-005-and-n03-n04-prototypes]]（vloop2）
- 前々サイクル: [[2026-05-24_idea-trace-and-token-speed-prototype]]（vloop1）
- [[../05_monetization/scenarios/candidate-005]]
- [[../05_monetization/scenarios/candidate-005_公開ブロッカー]]
- [[../05_monetization/scenarios/candidate-005_7日実行プラン]]
- [[../05_monetization/scenarios/candidate-005_progress投入設計]]
- [[candidate-005_ChatGPT承認パック]]
- [[candidate-001_ChatGPT承認パック]]（並走候補・形式比較）
- Issue: kaeru07/vault#60 / #61 / #62 / #63
