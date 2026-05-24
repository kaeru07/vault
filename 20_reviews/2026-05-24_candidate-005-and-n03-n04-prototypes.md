---
date: 2026-05-24
task: 試作ループ Epic 拡張 — candidate-005 正規化 + N-03/N-04 静的 HTML 試作 + idea_trace/Phase6 反映
runId: 20260524-vloop-candidate005-n03-n04
targetApp: company-meta / obsidian-vault
monetizationImpact: high
theme: [monetization, prototype, app-strategy, obsidian, market-research]
relatedRunIds: [20260524-185950, 20260524-vloop-prototype-loop-epic]
commitHashes: []
---

# 試作ループ Epic 拡張 — candidate-005 正規化 + N-03/N-04 試作

## 1. 作業目的

- 前サイクル末（vloop_2026-05-24_1852）で「次サイクル候補」として挙げた以下 3 件を 1 サイクルで一体実装する
  - token-speed-tool を candidate-005 として scenarios 正規化
  - N-03 Claude/Codex/Gemini 使い分けチャート 静的 HTML 試作
  - N-04 Vault Search Cheatsheet 静的 HTML 試作
- 試作ループの「1 サイクルで 2 案目まで進める」任意要件を達成する
- idea_trace ハブを 10 案体制に拡張し、候補-005 + N-03/N-04 を反映する

## 2. 実施内容

- `05_monetization/scenarios/candidate-005.md` を candidate-004 と同形式で新規作成（scoreTotal 28/40 / 収益化 6 軸 25/30 / status: candidate / approved にしない）
- `05_monetization/scenarios/README.md` の candidate 全件テーブルを 4 → 5 件に拡張
- `90_prototypes/llm-chooser/` 新規（index.html + README.md / CDN ゼロ・file:// 動作・クイズ 4 種 + 全 LLM カード + 機能比較表 9 項目 × 4 LLM）
- `90_prototypes/vault-search-cheatsheet/` 新規（index.html + README.md / 検索ボックス + 20 件キーワード対応表 + iPhone/GitHub Tips + トラブル対応）
- `05_monetization/idea_trace.md` を更新（§2 候補-005 リンク反映 + §8 N-03 カード + §9 N-04 カード + APIなし早見表 4 件追加 + 案件数 8→10）
- `05_monetization/試作ループ検証.md` に Phase6 追記（vloop2 達成項目・新しい仮説）
- `00_START_HERE.md` に candidate-005 + 試作モック 3 件のセクション追加
- `_review_queue.md` の未レビュー先頭に本レビューを追加

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `05_monetization/scenarios/candidate-005.md` | 新規（candidate 正規化） |
| `05_monetization/scenarios/README.md` | candidate 全件テーブル 4→5 件 |
| `90_prototypes/llm-chooser/index.html` | 新規（N-03 MVP モック本体） |
| `90_prototypes/llm-chooser/README.md` | 新規（N-03 試作の使い方） |
| `90_prototypes/vault-search-cheatsheet/index.html` | 新規（N-04 MVP モック本体） |
| `90_prototypes/vault-search-cheatsheet/README.md` | 新規（N-04 試作の使い方） |
| `05_monetization/idea_trace.md` | §2 候補-005 反映 + §8/§9 追加 + 早見表更新 |
| `05_monetization/試作ループ検証.md` | Phase6 追記 |
| `00_START_HERE.md` | candidate-005 + 試作モック 3 件入口追加 |
| `20_reviews/2026-05-24_candidate-005-and-n03-n04-prototypes.md` | 本ファイル新規 |
| `20_reviews/_review_queue.md` | 未レビュー先頭に追加 |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| HTML 構文（3 ファイル） | ✅ 手動確認 |
| API 呼び出しゼロ | ✅ fetch なし・外部 CDN ゼロ・file:// 動作 |
| 機密スキャン | ✅ `api[_-]?key`/`sk-`/`Bearer`/`password`/`secret`/`-----BEGIN` 実値なし |
| candidate-005 approved 化禁止ルール遵守 | ✅ status: candidate（pending_approval にもしない）|
| build / typecheck / lint | n/a（静的構成・ビルドツールなし）|
| iPhone 実機表示 | ⏳ 次サイクルでユーザー確認 |
| ob sync | 次フェーズで実行（最終応答前）|
| GitHub push | 次フェーズで実行 |

## 5. 未対応

- iPhone 実機表示確認（3 試作とも・ユーザー操作待ち）
- candidate-005 の 7 日実行プラン化（次サイクル候補）
- candidate-005 の公開ブロッカー整理（次サイクル候補）
- candidate-005 の ChatGPT 承認パック化（次サイクル候補）
- N-03 判定基準の客観化（個人観察ベース → ChatGPT レビュー後に再調整）
- N-04 と既存「Vault の見方ガイド」の内容重複整理

## 6. 危険ポイント

- ❌ なし（破壊的変更なし / 既存ファイル削除なし / DB 操作なし / 認証情報なし / 外部公開判断なし）
- candidate-005 を AI 判断で approved 化していない（**ルール厳守**）
- ChatGPT 承認待ちへの追加はしていない（候補-005 はまだ scenarios 正規化までで pending_approval ですらない）

## 7. 次にやるべきこと

1. ChatGPT が candidate-005 の方向性レビュー（scoreTotal 28/40 + 6 軸 25/30 / candidate-001 との並走判断 / approved 化可否）
2. ChatGPT が N-03 判定フロー妥当性レビュー（個人観察ベースの判定基準）
3. ChatGPT が N-04 と既存 Vault 見方ガイドの重複整理方針レビュー
4. ユーザーが iPhone Safari で 3 試作（token-speed-tool / llm-chooser / vault-search-cheatsheet）を開いて操作確認
5. 次サイクルで candidate-005 の 7 日実行プラン + 公開ブロッカー + ChatGPT 承認パック化
6. 次サイクルで N-03 / N-04 の candidate 化判断（追加情報蓄積後）

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です（2 サイクル目・前回続報）。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: vloop 2026-05-24 試作ループ Epic 拡張 — candidate-005 正規化 + N-03/N-04 静的 HTML 試作
GitHub commits: 次サイクル末で push

## できるようになったこと
- token-speed-tool（idea_trace §2）を candidate-005 として scenarios 配下に正規化（scoreTotal 28/40 / 6 軸 25/30 / candidate-001 並走判断付き）
- N-03 LLM Chooser を静的 HTML で実装（クイズ 4 種 + 4 LLM カード + 比較表 9 項目 × 4 LLM・外部 CDN ゼロ）
- N-04 Vault Search Cheatsheet を静的 HTML で実装（検索ボックス絞り込み + 20 件キーワード対応表 + iPhone/GitHub Tips）
- idea_trace を 8→10 案体制に拡張・APIなし早見表 4 件追加
- 試作ループ検証 Phase6 で「1 サイクルで 2 案目試作」任意要件を達成
- 00_START_HERE に candidate-005 + 試作モック 3 件の入口追加

## 確認したい観点
- candidate-005 のスコアリング（28/40 / 6 軸 25/30）は妥当か。candidate-001 との並走判断は妥当か
- candidate-005 を approved 化せず candidate のまま留めた判断は妥当か（ルール「AI 判断で approved 化禁止」遵守）
- N-03 LLM Chooser の判定基準（個人観察ベース）は他者から見て妥当か。客観化の必要性はあるか
- N-04 Vault Search Cheatsheet と既存「Vault の見方ガイド」（00_inbox/）の重複は許容範囲か
- 1 サイクルで 3 ファイル（candidate-005.md + 2 試作）を仕上げた判断は妥当か（粒度として）
- iPhone 実機表示はまだ未確認だが、これは次サイクルにすべきユーザータスクとして切り出して問題ないか

参考リンク:
- 05_monetization/scenarios/candidate-005.md
- 90_prototypes/llm-chooser/README.md
- 90_prototypes/vault-search-cheatsheet/README.md
- 05_monetization/idea_trace.md（§2 + §8 + §9 更新）
- 05_monetization/試作ループ検証.md（Phase6 追記）
```

## 関連

- 前サイクル: [[2026-05-24_idea-trace-and-token-speed-prototype]]（vloop1）
- [[../05_monetization/scenarios/candidate-005]]
- [[../05_monetization/idea_trace]]
- [[../05_monetization/試作ループ検証]]
- [[../90_prototypes/llm-chooser/README]]
- [[../90_prototypes/vault-search-cheatsheet/README]]
- [[../90_prototypes/token-speed-tool/README]]
- Issue: kaeru07/vault#60 / #61 / #62 / #63
