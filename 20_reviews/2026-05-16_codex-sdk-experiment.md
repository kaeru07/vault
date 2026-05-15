---
date: 2026-05-16
task: Codex SDK 最小実験 (読み取り専用 / experiments/codex-sdk)
runId: 20260516-020102
targetApp: progress
monetizationImpact: low
theme: [workflow, prompt-template]
relatedRunIds: [20260515-221729]
commitHashes: []
---

# 2026-05-16 Codex SDK 最小実験 (読み取り専用)

> commitHashes は空。理由: 指示で git push / commit を禁止しており、experiments/ は ny01 リポに意図的に未コミットのまま残すため。

---

## 1. 作業目的

- なぜ: VPS 上で Codex SDK を最小実験し、読み取り専用で使えるか確認する
- 背景: progress に Codex 連携実験機能（20260515-221729）を追加済み。SDK 方式での実現性を別系統で検証したい
- 期待効果: execution-runs のレビューを Codex に補助させる道筋を、本体無影響で確認

---

## 2. 実施内容

- 環境確認（Node v22.22.2 / npm 10.9.7 / Python 3.12.3 / codex 0.128.0 / login=Logged in using ChatGPT）
- Codex SDK 提供形態調査: `@openai/codex-sdk`(TS) npm v0.130.0 のみ公式 / pip `codex-sdk` 無し / `openai` pip は API キー前提で不使用
- 方針決定: Python → `codex exec` subprocess（`--sandbox read-only`）。npm/pip 追加導入不要
- `experiments/codex-sdk/` に 4 ファイル作成（progress 本体・データは非変更）
- 読み取り専用スクリプト: execution-runs.json 抽出 → codex でレビュー要約+改善 ToDo → 標準出力のみ

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| experiments/codex-sdk/check-env.md | 新規（環境確認結果） |
| experiments/codex-sdk/.gitignore | 新規（実験生成物のみ無視。本体 .gitignore は非変更） |
| experiments/codex-sdk/review_execution_runs.py | 新規（読み取り専用レビュー実験） |
| experiments/codex-sdk/README.md | 新規（利用可否・使い方・検証結果） |

progress 本体コード / project-tasks.json / execution-runs.json / .env は**一切変更なし**。

---

## 4. 検証結果

- 環境: 上記すべて確認済み。codex login 操作は**不要**（ログイン済み）
- `--dry-run`: 全 47 件中レビュー対象 **19 件**抽出を確認
- 実 codex（`--limit 2`）: read-only 実行、exit 0、各 Run の一行要約+リスク+改善 ToDo+全体優先 ToDo を日本語出力（8,369 tokens）
- `execution-runs.json`: 251220 bytes / May 16 01:50 のまま**不変** = 読み取り専用成立
- `experiments/` 配下に生成物（`__pycache__` 等）なし
- 機密パターン事前チェック: OK
- ob sync: 後述 / git push: 指示により progress 側は実施しない（n/a）

実行した検証コマンド:
```
node --version / npm --version / python3 --version
which codex && codex --version / codex login status
npm view @openai/codex-sdk version / npm view @openai/codex version
pip3 show codex-sdk / python3 -c "import openai"
python3 review_execution_runs.py --dry-run
python3 review_execution_runs.py --limit 2
ls -la <execution-runs.json>  (前後比較)
```

---

## 5. 未対応

- 結果の JSON 構造化 / needs_followup 自動振り分け（書き込みを伴うため意図的に保留）
- `@openai/codex-sdk`(TypeScript) の隔離検証
- `experiments/` を ny01 リポに commit するか / `.gitignore` に追加するか（ユーザー判断）
- progress 既存 Codex 連携機能（20260515-221729）との統合可否

---

## 6. 危険ポイント

- codex 呼び出しは ChatGPT アカウントのトークンを消費（今回 8,369 tokens）
- `experiments/` が ny01 リポの未追跡ファイルとして残る → 将来の progress 本体 commit 時に誤って巻き込むリスク。フォルダ内 `.gitignore` は同梱したが本体 `.gitignore` は非変更
- read-only サンドボックスだが codex 自体はモデル実行・ネットワークを行う（許容範囲かは運用判断）
- 既存 progress（3010）・データは無変更（ロールバック手段は experiments/ フォルダ削除のみ）

---

## 7. 次にやるべきこと

- 結果構造化と progress 既存 Codex 実験機能との統合方針（ユーザー判断）
- `experiments/` のリポ取り扱い決定（commit / gitignore 追加 / 別リポ）
- TypeScript SDK 検証を行うかの判断
- 観察項目: codex トークン消費量、レビュー精度

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress (experiments/codex-sdk)
作業: Codex SDK 最小実験 (読み取り専用)
runId: 20260516-020102
日付: 2026-05-16
GitHub: progress 側は push せず (指示通り) / obsidian-vault 側は別途

## 作業目的
- VPS で Codex SDK を読み取り専用で使えるか最小確認
- progress 本体に影響を出さない

## 実施内容
- 環境確認 (Node/npm/Python/codex/login)
- Codex SDK 提供形態調査 (TS公式のみ / Python公式無し)
- Python → codex exec subprocess (read-only) 方式採用
- experiments/codex-sdk に check-env.md/.gitignore/review_execution_runs.py/README.md
- execution-runs.json 抽出 → codex レビュー要約+ToDo (標準出力のみ・書込なし)

## 検証結果
- --dry-run: 47件中19件抽出
- 実codex --limit 2: read-only exit0 レビュー出力 (8369 tokens)
- execution-runs.json サイズ・mtime 不変 = 読み取り専用成立
- experiments配下に生成物なし

## Codex CLI/SDK 利用可否
- CLI: 利用可 (ChatGPTログイン済・APIキー不要)
- SDK: 公式TypeScriptのみ。Python公式無し → subprocess代替

## 未対応
- 結果JSON構造化 / needs_followup自動振り分け (書込保留)
- TS SDK 隔離検証 / experiments のリポ取扱い

## 危険ポイント
- codex はトークン消費
- experiments/ が ny01 未追跡で残る (誤commit注意)
- read-only だが codex 自体はネットワーク/モデル実行

## 確認したい観点
- 読み取り専用の担保方法は十分か (sandbox + 書込なし設計)
- subprocess 方式 vs TS SDK のどちらを本命にすべきか
- experiments/ のリポ管理方針
- progress 既存 Codex 機能との統合設計
- トークン消費を抑える抽出/プロンプト設計
````

---

## 関連

- progress runId: 20260516-020102（正本）
- 関連 run: 20260515-221729（progress Codex 連携実験機能）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
