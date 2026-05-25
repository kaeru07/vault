---
date: 2026-05-24
task: candidate-006 / 007 起票 + 案件別ToDo一覧 新規作成（vloop6）
runId: 20260524-vloop6-candidate006-007-project-todos
targetApp: company-meta / obsidian-vault
monetizationImpact: medium
theme: [monetization, app-strategy, prototype, workflow]
relatedRunIds: [20260524-223229, 20260524-222244, 20260524-200532, 20260524-194403, 20260524-185950]
commitHashes: []
---

# candidate-006 / 007 起票 + 案件別ToDo一覧 新規作成

## 1. 作業目的

- ユーザーが pull で取得した新ルール「[[案件別ToDo運用ルール]]」に準拠し、案件別ToDo一覧.md を新規作成
- vloop5 末の「次サイクルで N-03 / N-04 候補化判断」を達成 → candidate-006 / 007 を scenarios 正規化
- 試作完成後の自然な次ステップ（候補化）を Epic 完了優先で進める

## 2. 実施内容

- `20_reviews/案件別ToDo一覧.md` 新規作成（Vault運用 / 麻雀アプリ / トークン速度 / 複数案試作 / 収益化案 / レビュー承認 の 6 案件で整理）
- `05_monetization/scenarios/candidate-006.md` 新規（N-03 LLM Chooser を candidate-004 形式コンパクト版で正規化 / scoreTotal 26/40 / status: candidate）
- `05_monetization/scenarios/candidate-007.md` 新規（N-04 Vault Search Cheatsheet 同様 / scoreTotal 28/40 / status: candidate / 既存 Vault 見方ガイドとの重複整理 3 統合方針案）
- `05_monetization/scenarios/README.md` 候補全件テーブル 5 → 7 件 + candidate-006/007 補助ファイルセクション追加
- `05_monetization/idea_trace.md` §8 N-03 / §9 N-04 のステータスを「idea」→ 「candidate（candidate-006 / 007 として正規化済）」へ変更 + リンク欄に scenarios 追加 + 判断履歴に vloop6 追記
- `00_START_HERE.md` 入口を 5 → 6 件化（**案件別ToDo一覧を最優先①に追加** / scenarios 7 件に更新）
- `20_reviews/次に実体化するToDo.md` 実体化済 8 件目に N-03/N-04 候補化追加 + 優先 3 を「完了」化 + 優先 6 として「候補-006/007 補助 4 ファイル × 2 = 8 ファイル」を planned_only として記録

## 3. 変更ファイル

| ファイル                                                           | 変更内容                                          |
| -------------------------------------------------------------- | --------------------------------------------- |
| `20_reviews/案件別ToDo一覧.md`                                      | 新規（新運用ルール準拠の正本 / 6 案件 / ユーザー確認待ち / 新規発生 ToDo） |
| `05_monetization/scenarios/candidate-006.md`                   | 新規（コンパクト版 candidate）                          |
| `05_monetization/scenarios/candidate-007.md`                   | 新規（コンパクト版 candidate + 既存資産との重複整理 3 案）         |
| `05_monetization/scenarios/README.md`                          | 7 件テーブル + candidate-006/007 補助セクション           |
| `05_monetization/idea_trace.md`                                | §8/§9 ステータス変更 + 履歴更新                          |
| `00_START_HERE.md`                                             | 入口 6 件化 + 案件別ToDo一覧を最優先 + 7 件に更新              |
| `20_reviews/次に実体化するToDo.md`                                    | 実体化済 8 件目 + 優先 3 完了 + 優先 6 追加                 |
| `20_reviews/2026-05-24_candidate-006-007-and-project-todos.md` | 本ファイル新規                                       |
| `20_reviews/_review_queue.md`                                  | 先頭追加                                          |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| 新運用ルール（案件別ToDo運用ルール）準拠 | ✅ 案件別ToDo一覧.md を最優先入口に配置 |
| candidate-006/007 の approved 化禁止ルール | ✅ status: candidate のまま（pending_approval にも上げていない）|
| candidate-006/007 補助ファイルは次サイクル化 | ✅ 「次に実体化するToDo」優先 6 として登録（やりっぱなし防止）|
| 既存リンク保持 | ✅ 既存リンクは全て維持・追加のみ |
| 機密スキャン | ✅ 実値なし |
| build / typecheck / lint | n/a（Markdown のみ）|
| ob sync | 次フェーズで実行 |
| GitHub push | 次フェーズで実行 |

## 5. 未対応

- candidate-006 / 007 の補助 4 ファイル × 2 = 8 ファイル（次サイクル / 次に実体化するToDo 優先 6）
- ChatGPT が候補-006 / 007 を方向性レビュー
- iPhone 実機表示確認（候補-006 / 007 試作 + 新規案件別ToDo一覧）
- candidate-001 / candidate-005 の ChatGPT 方向性承認（既存待ち）
- #67 Hermes Agent × Codex 検討 / #59 Vault 全体棚卸し（次サイクル候補）
- candidate 本体への用語注記 + Mermaid 図追加（次サイクル）

## 6. 危険ポイント

- ❌ なし（破壊的変更なし / 既存ファイル削除なし / DB 操作なし / 認証情報なし / 外部公開判断なし）
- candidate-006/007 を**勝手に pending_approval / approved 化していない**（ルール厳守）
- ChatGPT 承認待ち.md への追加も**意図的に保留**（候補-001/005 と同じ運用 / 人間判断後）

## 7. 次にやるべきこと

1. ChatGPT が candidate-006 / 007 + 案件別ToDo一覧 を方向性レビュー
2. ユーザーが iPhone Obsidian で「案件別ToDo一覧」が最優先入口として開けるか確認
3. 次サイクルで candidate-006 / 007 の補助 4 ファイル × 2 = 8 ファイル作成
4. 次サイクルで #67 Hermes Agent × Codex 検討着手
5. 次サイクルで #59 Vault 全体棚卸し Phase 計画
6. ChatGPT が candidate-001 / candidate-005 方向性レビュー（既存待ち）

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です（6 サイクル目・本日 6 回目）。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: vloop6 candidate-006 / 007 起票 + 案件別ToDo一覧 新規作成

## できるようになったこと
- 新運用ルール「案件別ToDo運用ルール」準拠の案件別ToDo一覧.md（6 案件）を新規作成
- N-03 LLM Chooser → candidate-006 として scenarios 正規化（26/40 / コンパクト版）
- N-04 Vault Search Cheatsheet → candidate-007 として scenarios 正規化（28/40 / 既存資産との重複整理 3 統合方針案付き）
- scenarios/README 候補全件 5 → 7 件
- idea_trace §8/§9 候補化反映
- 00_START_HERE 入口 5 → 6 件化（案件別ToDo一覧を最優先）

## 確認したい観点
1. 案件別ToDo一覧の 6 案件分類（Vault運用 / 麻雀アプリ / トークン速度 / 複数案試作 / 収益化案 / レビュー承認）は妥当か
2. candidate-006（個人観察ベース判定 / 26-40）の scoreTotal は妥当か
3. candidate-007（既存資産流用 / 28-40）の scoreTotal は妥当か / 既存「Vault の見方ガイド」との統合方針 A/B/C のいずれが望ましいか
4. candidate-005 + candidate-006 の相互送客判断は妥当か（同じ AI 開発者層）
5. 候補-006/007 を approved 化せず candidate のまま留めた判断は妥当か（候補-005 と同パターン）
6. 補助 4 ファイル × 2 = 8 ファイルを次サイクル化（次に実体化するToDo 優先 6）は妥当か

参考リンク:
- 20_reviews/案件別ToDo一覧.md
- 05_monetization/scenarios/candidate-006.md
- 05_monetization/scenarios/candidate-007.md
- 05_monetization/scenarios/README.md（7 件テーブル）
- 05_monetization/idea_trace.md（§8/§9 更新）
```

## 関連

- 前サイクル: [[2026-05-24_vault-accumulation-rule-residual]]（vloop5）
- [[案件別ToDo運用ルール]]（本サイクルの根拠ルール / pull で取得）
- [[案件別ToDo一覧]]
- [[../05_monetization/scenarios/candidate-006]]
- [[../05_monetization/scenarios/candidate-007]]
- [[candidate-001_ChatGPT承認パック]] / [[candidate-005_ChatGPT承認パック]]（形式比較）
- Issue: kaeru07/vault#61 / #70 / #63
