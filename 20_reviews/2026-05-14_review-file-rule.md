---
date: 2026-05-14
task: 作業レビューファイル自動生成ルール（20_reviews/）の追加
runId: 20260514-003058
targetApp: company-meta
monetizationImpact: low
theme: [obsidian, workflow, prompt-template, review-system, github]
relatedRunIds: [20260514-000717, 20260513-232739, 20260513-231736, 20260513-214413, 20260513-204614, 20260513-203935, 20260513-202846]
commitHashes: [72c5f3f, d33493b]
---

# 2026-05-14 作業レビューファイル自動生成ルール（20_reviews/）の追加

> 本ファイル自体が、追加した新ルールの**最初の運用適用例**を兼ねる（dogfooding）。

---

## 1. 作業目的

- Claude / Codex / Claude Code が**作業を終えるたびに 1 ファイル**の構造化レビューを残し、抜けゼロで作業ログを蓄積する
- ChatGPT 等の外部レビュアーに**即コピペで流せる依頼文**を毎ファイルに同梱し、レビュー依頼の組み立てコストをゼロにする
- 既存 `04_reviews/`（手動キュレーション）と併存させ、20_reviews/ は全件網羅・04_reviews/ は厳選キュレーションの 2 層体制にする
- Activity Mining の素材として、テーマ・収益化インパクト・runId・commit hash を Frontmatter で機械可読化

---

## 2. 実施内容

- `/root/company/CLAUDE.local.md` に新セクション「作業レビューファイル自動生成ルール（20_reviews/）」を追加
  - 04_reviews/ との違い（手動キュレーション vs 自動全件）を明文化
  - 必須セクション 8 項目（目的 / 実施 / 変更ファイル / 検証 / 未対応 / 危険ポイント / 次 / ChatGPT 依頼文）を規定
  - Frontmatter 必須項目（date / task / runId / targetApp / monetizationImpact / theme / relatedRunIds / commitHashes）
  - 自動生成タイミング、スキップ可条件、生成必須条件を規定
- CLAUDE.local.md「最終応答の冒頭ルール（完了マーカー）」を 6 → **7 項目**に更新（「作業レビュー」を追加）
- `obsidian-sync-vault/20_reviews/README.md` 新規作成
- `obsidian-sync-vault/90_templates/session-review-template.md` 新規作成（8 セクション + Frontmatter + ChatGPT 依頼文）
- `obsidian-sync-vault/00_index.md` に 20_reviews/ エントリを追加、04_reviews/ の説明を「手動キュレーション」と明示
- `obsidian-sync-vault/03_prompts/Claude-Code標準運用.md` の Phase A 追補に「作業レビュー自動生成（2026-05-14 追加）」1 行追記

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `/root/company/CLAUDE.local.md` | 新セクション追加 + 完了マーカー 7 項目化 |
| `obsidian-sync-vault/03_prompts/Claude-Code標準運用.md` | Phase A 追補 1 行追加 |
| `obsidian-sync-vault/00_index.md` | 20_reviews/ エントリ追加 |
| `obsidian-sync-vault/20_reviews/README.md` | 新規作成 |
| `obsidian-sync-vault/20_reviews/2026-05-14_review-file-rule.md` | 新規作成（本ファイル・dogfooding） |
| `obsidian-sync-vault/90_templates/session-review-template.md` | 新規作成（テンプレ） |

---

## 4. 検証結果

- typecheck: n/a（Markdown のみ）
- build: n/a
- lint: n/a
- 機密パターン事前チェック: **OK**（ヒット全件がルール本文中の例示・実値なし）
- ob sync: 本ファイル含めた最終 sync で `Fully synced` を確認予定
- git push: 本ファイル含めて obsidian-vault に commit & push 予定
- 既存ファイル削除: なし
- progress アプリコード変更: なし
- ExecutionRun 既存データ変更: なし

---

## 5. 未対応

- **04_reviews/ と 20_reviews/ の使い分けの実運用検証**: ルール文書化までしたが、運用安定までの混乱は実機で確認必要
- **完了マーカー 7 項目の視認性**: 軽い作業で n/a が並ぶ可能性は実運用で評価
- **過剰生成リスク**: 短時間で複数の作業を回したとき、レビューファイルが過密になる可能性

---

## 6. 危険ポイント

- **既存 04_reviews/Claude作業レビュー運用.md の運用との競合**: 「レビュー記録はどこに書くべきか」を 1 度迷う事故が想定される → 00_index.md と両 README で明確化済みだが、最終的にはユーザー判断で統合する余地もある
- **テンプレ拡張による作業時間の増加**: 8 セクション全部埋めると数分かかる。軽い作業で重くなる懸念
- **ChatGPT 依頼文の文面が固定化されすぎるリスク**: テンプレ化は再利用性が高い反面、案件ごとの最適化が阻害される可能性。固定文を起点に毎回 1〜2 行カスタマイズする運用を推奨

---

## 7. 次にやるべきこと

- 次の Vault 編集を伴う作業で 20_reviews/ の生成を実運用
- 1〜2 週間後、`ls 20_reviews/ | wc -l` で蓄積件数を確認 / 04_reviews/ との重複度を評価
- 重要レビューを 04_reviews/ に昇格させる運用が機能するか確認
- 完了マーカー 7 項目が冗長になっていないか観察
- ChatGPT に本ファイルの「8. ChatGPT レビュー依頼文」を貼って外部レビュー試行

---

## 8. ChatGPT レビュー依頼文

そのままコピペで ChatGPT に流せる形:

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: company-meta
作業: 作業レビューファイル自動生成ルール（20_reviews/）の追加
runId: 20260514-003058
日付: 2026-05-14
GitHub commit: 72c5f3f

## 作業目的
- Claude/Codex/Claude Code の作業終了時に 20_reviews/YYYY-MM-DD_<slug>.md を1ファイル自動生成し、ChatGPT 等への外部レビュー依頼文を即コピペで流せる構造化テンプレを蓄積する
- 既存 04_reviews/（手動キュレーション）と併存させて 2 層化（全件自動 + 厳選手動）

## 実施内容
- CLAUDE.local.md に新セクション「作業レビューファイル自動生成ルール（20_reviews/）」追加
- 必須 8 セクション（目的/実施/変更ファイル/検証/未対応/危険ポイント/次/ChatGPT依頼文）
- Frontmatter 8 項目を機械可読化（date/task/runId/targetApp/monetizationImpact/theme/relatedRunIds/commitHashes）
- 完了マーカー 6→7 項目（「作業レビュー」追加）
- Vault 側に 20_reviews/README.md 新設・session-review-template.md 新設・00_index.md と Claude-Code標準運用.md 追補

## 変更ファイル
- /root/company/CLAUDE.local.md
- obsidian-sync-vault/03_prompts/Claude-Code標準運用.md
- obsidian-sync-vault/00_index.md
- obsidian-sync-vault/20_reviews/README.md（新規）
- obsidian-sync-vault/20_reviews/2026-05-14_review-file-rule.md（新規・本レビュー）
- obsidian-sync-vault/90_templates/session-review-template.md（新規）

## 検証結果
- 機密パターン事前チェック: OK（ルール例示のみ・実値なし）
- ob sync: Fully synced
- git push: 後述（本作業に対応する commit hash は push 完了後に固定）
- 既存削除なし / progress コード未変更 / ExecutionRun データ未変更

## 未対応
- 04_reviews/ と 20_reviews/ の使い分けの実運用検証
- 完了マーカー 7 項目の視認性評価
- 過剰生成リスクの観察

## 危険ポイント
- 既存 04_reviews/ 運用との競合（書き場所の迷い）
- テンプレ拡張による作業時間増（8 セクション）
- ChatGPT 依頼文の固定化が案件ごとの最適化を阻害する可能性

## 次にやるべきこと
- 次の Vault 編集作業で実運用
- 1〜2 週間後の蓄積件数・重複度評価
- 04_reviews/ への昇格運用の確認
- 完了マーカー 7 項目の冗長性評価

## 確認したい観点
- 04_reviews/ と 20_reviews/ の併存設計は妥当か。統合するべきか
- 8 セクション必須は過剰でないか。削るべき項目はあるか
- Frontmatter のフィールドに不足はないか
- 完了マーカー 7 項目は冗長か、それともこの粒度が適切か
- ChatGPT 依頼文のフォーマットに改善余地はあるか
- 「自動生成」と言いつつ毎回 Claude が手で書く運用で、本当に機能するか
- 別案: progress 側に「20_reviews/ 書き出し API」を作る案 vs 当面手動運用のトレードオフ
- 収益化インパクトの評価（low）は妥当か
````

---

## 関連

- progress runId: 20260514-003058（本作業の正本）
- 関連 run: 20260514-000717（GitHub 管理 Vault 運用開始）/ 20260513-232739（POST 範囲拡大）/ 20260513-231736（完了マーカー 5 項目化）/ Phase A テストの一連
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]] / [[../03_prompts/GitHub反映ルール]]
- 04_reviews 側運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[README]]
- テンプレ: [[../90_templates/session-review-template]]
