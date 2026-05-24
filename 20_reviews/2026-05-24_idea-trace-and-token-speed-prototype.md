---
date: 2026-05-24
task: 試作ループ Epic（#60/#61/#62/#63）一体実装 — idea_trace ハブ + token-speed-tool 仕様/trace + MVP モック + 試作ループ検証
runId: 20260524-vloop-prototype-loop-epic
targetApp: company-meta / obsidian-vault
monetizationImpact: high
theme: [monetization, prototype, workflow, obsidian, app-strategy, market-research]
relatedRunIds: [20260524-vloop-0048]
commitHashes: []
---

# 試作ループ Epic 一体実装 — idea_trace + token-speed-tool 試作

## 1. 作業目的

- 前サイクル末（vloop_2026-05-24_0048）で「次サイクルで #60-#63 を一体実装」と決めた残 open Epic を完了させる
- 案生成だけで止まらず、**実際に小さく作って試すループ**を 1 サイクルで一巡させる
- 全アプリ案の「情報源 → 気づき → 案 → 候補 → 試作 → 判断」を 1 ページで追える状態を作る（Issue #63）
- トークン速度ツールの着想経路と APIなし MVP を**仕様 + 試作の両方**で着地させる（Issue #60/#62）
- 試作ループの実行可能性を 1 件以上の現物で実証する（Issue #61）

## 2. 実施内容

- `05_monetization/idea_trace.md` を新規作成（カード形式 8 案登録 + 凡例 + APIなし早見表 + 追加テンプレ + mermaid 流れ図）
- `05_monetization/token-speed-tool.md` を新規作成（Part 1 trace + Part 2 MVP 仕様、両 Issue を 1 ファイル集約）
- `90_prototypes/token-speed-tool/` ディレクトリ新規作成
  - `index.html`（UI 一式・HTML+CSS+JS インライン・外部 CDN ゼロ）
  - `sample-data.json`（初期 5 件サンプル）
  - `README.md`（使い方・既知制約・次の改善）
- `05_monetization/試作ループ検証.md` を新規作成（14 案追加・3 候補選定・試作 1 件着地・Phase1-5 完了条件チェック）
- `00_START_HERE.md` に idea_trace / token-speed-tool / 試作ループ検証への入口リンク 3 件追加
- `_review_queue.md` の未レビュー先頭に本レビューを追加

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `05_monetization/idea_trace.md` | 新規（8 案カード + APIなし早見表 + 追加テンプレ）|
| `05_monetization/token-speed-tool.md` | 新規（Part1 trace #62 + Part2 仕様 #60）|
| `05_monetization/試作ループ検証.md` | 新規（#61 Phase1-5）|
| `90_prototypes/token-speed-tool/index.html` | 新規（MVP モック本体）|
| `90_prototypes/token-speed-tool/sample-data.json` | 新規（サンプル 5 件）|
| `90_prototypes/token-speed-tool/README.md` | 新規（試作の使い方）|
| `00_START_HERE.md` | idea_trace / モック / 試作ループ への入口 3 件追加 |
| `20_reviews/2026-05-24_idea-trace-and-token-speed-prototype.md` | 本ファイル新規 |
| `20_reviews/_review_queue.md` | 未レビュー先頭に本レビュー追加 |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| HTML 構文 | ✅ 手動確認（`<!DOCTYPE html>` + UTF-8 + viewport）|
| JSON 妥当性 | ✅ `sample-data.json` parse 可能 |
| 静的構成成立 | ✅ 外部 CDN ゼロ・file:// フォールバックあり |
| API 呼び出しゼロ | ✅ fetch は同梱 JSON のみ |
| 機密情報スキャン | ✅ `api[_-]?key`/`sk-`/`Bearer`/`password`/`secret`/`token`/`-----BEGIN` 実値なし（ルール文中の例示のみ）|
| build / typecheck / lint | n/a（HTML+CSS+JS のみ・ビルドツールなし）|
| iPhone 実機表示 | ⏳ 次サイクルでユーザー確認 |
| ob sync | 次フェーズで実行（最終応答前）|
| GitHub push | 次フェーズで実行 |

## 5. 未対応

- iPhone 実機表示確認（ユーザーが iPhone Obsidian + Web ブラウザで開いて確認）
- token-speed-tool を scenarios 配下に candidate-005 として正規化（次サイクル候補）
- N-03 / N-04 の HTML 試作（次サイクル候補）
- ChatGPT 承認パック化（next）

## 6. 危険ポイント

- ❌ なし（既存ファイル削除なし / DB 操作なし / 認証情報なし / 本番環境変更なし）
- 既存ファイルへの変更は `00_START_HERE.md` の追加のみ（既存リンク削除なし）

## 7. 次にやるべきこと

1. ChatGPT が candidate-001 を方向性承認（Epic C 残・既存）
2. **ChatGPT が本サイクルの試作ループ Epic 完了を方向性レビュー**（idea_trace 構造 / token-speed-tool MVP / 試作ループ判断）
3. ユーザーが iPhone Obsidian で `00_START_HERE` から idea_trace / token-speed-tool 試作を確認
4. ユーザーがブラウザで `90_prototypes/token-speed-tool/index.html` を開き、サンプル 5 件表示 + 操作確認
5. 次サイクルで token-speed-tool を candidate-005 として scenarios に正規化検討
6. 次サイクルで N-03 / N-04 静的 HTML 試作着手

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: vloop 2026-05-24 試作ループ Epic 一体実装（Issue #60/#61/#62/#63 を 1 サイクルで完了）

## できるようになったこと
- 全アプリ案の「情報源 → 気づき → 案 → 候補 → 試作 → 判断」を 1 ページで追える `idea_trace.md` ができた
- トークン速度ツール案の着想経路（HN 起点 + Claude/Codex 体感 + ローカル LLM ニーズ）が時系列で追える
- APIなしで成立する範囲と有料 API 必要範囲が早見表で区別された
- token-speed-tool の静的 HTML モック（fetch は同梱 JSON のみ・外部 API ゼロ）がサンプル 5 件で比較表示できる
- 試作ループ（案 → 試作 → テスト → 判断）を 1 件（N-01）で一巡実証できた
- 14 案を新規追加し 3 候補（N-01/N-03/N-04）を選定、候補-005 相当に到達した

## 確認したい観点
- idea_trace のカード形式（凡例 + APIなし早見表 + 追加テンプレ）は ChatGPT / 他者から見て読みやすいか
- token-speed-tool.md の Part1 trace（情報源・着想理由・判断履歴）+ Part2 MVP 仕様 を 1 ファイル集約する構造は妥当か（分けるべきか）
- 静的 HTML モック（外部 CDN ゼロ / file:// フォールバック / インラインサンプル）の構成は AI 開発者層向け MVP として十分か
- 体感スコア計算式（tokensPerSec×0.4 + (10/TTFT)×4 + outputChars/100×0.2）の重みは妥当か
- N-01 を candidate-005 として scenarios 配下に正規化する次サイクル方針は妥当か
- 14 案追加で「APIなし優先」に偏った選定は妥当か（多様性は十分か）
- 試作ループ検証で 2 案目の試作を見送り 1 案目を仕上げた判断は妥当か（任意項目だが）

参考リンク:
- 05_monetization/idea_trace.md
- 05_monetization/token-speed-tool.md
- 90_prototypes/token-speed-tool/README.md
- 05_monetization/試作ループ検証.md
```

## 関連

- [[../05_monetization/idea_trace]]
- [[../05_monetization/token-speed-tool]]
- [[../05_monetization/試作ループ検証]]
- [[../90_prototypes/token-speed-tool/README]]
- [[../00_START_HERE]]
- 前回 vloop サマリー: [[../03_prompts/claude-commands/logs/vloop_2026-05-24_0048]]
- Issue: kaeru07/vault#60 / #61 / #62 / #63
