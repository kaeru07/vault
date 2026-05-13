# 04_reviews — 作業レビュー記録

Claude Code（自己 / Codex / ChatGPT / ultrareview）の作業を 1 件単位でレビューし、時系列で蓄積する。**差し戻し・棄却したものほど残す価値がある**。

## ファイル命名

`YYYY-MM-DD-<short-title>.md` 形式。例:

- `2026-05-13-progress-execution-runs-api.md`
- `2026-05-13-nanikiru-shorts-shorts-quota.md`

同日複数件は連番にせず、タイトルで区別する。

## レビュー手順

1. `90_templates/claude-review-template.md` をコピーして `04_reviews/<file>.md` を作る
2. メタ情報（日付・対象アプリ・runId・関連プロンプト）を埋める
3. **着手前提（5項目）** が事前に提示されていたか確認
4. 実装レビュー → 検証レビュー → セキュリティ → 残課題 → 結論の順で記入
5. 結論: 採用 / 修正後採用 / 差し戻し / 棚上げ のいずれかを明記

## 書く内容

| セクション | 内容 |
|---|---|
| メタ | 日付 / 対象 / runId / プロンプト / レビュアー |
| 着手前提 5項目 | 何を直すか / どこを触るか / どこを触らないか / 完了条件 / 検証方法 |
| 変更内容 | 変更ファイル / 設計妥当性 / 既存への影響 / 不要な抽象化 |
| 検証 | typecheck / build / 動作確認 / エラー警告 |
| セキュリティ | 機密情報の混入 / 環境変数 / 入力バリデーション |
| 残課題 | 次にやるべきこと |
| 結論 | 採用 / 修正後採用 / 差し戻し / 棚上げ + 理由 |

## 書かない内容

- 機密情報（API キー / .env / 認証情報）
- 作業ログ全文（`engineering/reports/focus-work-*.md` が正本）
- 単に「OK」だけのレビュー → 何が OK だったかを 1 行でも残す

## 運用ルール

- 既存ファイルは削除しない。差し戻し・棄却された記録も残す
- 大規模変更は ultrareview の結果リンクをここに貼る（ultrareview 本体は別場所）
- progress アプリの `runId` を必ず控えておく（後から ExecutionRun と紐付けるため）

## 関連

- [[../03_prompts/claude-code-standard]]
- [[../02_apps/progress]]
- [[review-template]]
