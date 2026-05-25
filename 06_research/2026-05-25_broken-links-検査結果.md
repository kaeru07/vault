---
type: vault-audit
title: Vault 全 .md 横断「壊れたリンク」検査結果（Issue #59 残）
issue: kaeru07/vault#59
date: 2026-05-25
status: 検査結果のみ（破壊的削除はしない・修正は次サイクル以降）
tags: [vault, リンク検査, 監査, Issue59, 棚卸し]
---

# Vault 全 .md 横断「壊れたリンク」検査結果

> Issue #59 残作業: Vault 全体の Markdown ファイル横断で**実在しないリンク先**を検出。
> 本ページは**検査結果と修正優先度の整理**のみ。実修正は次サイクル以降（修正方針はユーザー / ChatGPT 判断）。

> [!note] 用語注（Issue #69）
> - 壊れたリンク = `[[link]]` 形式で書かれているが、リンク先のファイルが Vault 内に実在しないリンク
> - 偽陽性 = テンプレ内のプレースホルダ `link` / `<note-name>` / `{{slug}}` 等で、実害がないもの
> - 統合元 = 別ページに統合され、独立した実体はないファイル名（リンクを書き直すべき）

---

## 1. 検査メタ

- 対象: `/root/company/obsidian-sync-vault` 配下の全 `.md` ファイル
- 除外: `.obsidian/` / `.claude/` / `attachments/` / `.git/`
- 検査スクリプト: `/tmp/link_check.py`（Python / `[[link]]` パターン抽出 + ファイル名 / 相対パス両方で実在判定）
- 検査日時: 2026-05-25
- 検査結果サマリー:

| 項目 | 件数 |
|---|---|
| 対象 .md ファイル数 | 162 |
| `[[link]]` 出現総数 | 1,454 |
| 壊れたリンクを含むファイル数 | 66 |
| 壊れたリンク総数 | 202 |
| うちプレースホルダ（無視可）| 17 |
| うち実体不在（要対応）| **185** |

---

## 2. 修正優先度カテゴリ

### A. 過去存在していた運用ルールページが消えている（最優先）

これらは過去存在していたか、別ページに統合された運用ルール。多数のページから参照されているため、**実体を作成 or リンク先を書き換え**が必要。

| 不在ターゲット | 参照数 | 推測される統合先 / 対応 |
|---|---|---|
| `Vault蓄積運用ルール` | 11 | 過去存在 → 案件別ToDo運用ルール / Vault全体棚卸し 等に分散統合済の可能性 |
| `案件別ToDo運用ルール` | 9 | 過去存在 → 案件別ToDo一覧 内に統合済の可能性 |
| `../05_monetization/ChatGPT承認ゲート標準` | 13 + 7 + 3（`../ChatGPT承認ゲート標準`） = 23 | 過去存在 → 各 candidate 承認パックに統合済の可能性 |
| `../05_monetization/ChatGPT承認コマンド標準` | 3 | 同上 |

→ **対応案**: 統合先が確定したら、過去のリンクを `[[統合先]]` に一斉書き換え。または「廃止 / 統合済」リダイレクトページを 1 つ作成して指す。

### B. 過去調査ファイルが消えている

| 不在ターゲット | 参照数 | 推測される対応 |
|---|---|---|
| `../06_research/2026-05-22_上位5案追加調査` | 14 + 7 + 1 = 22 | 過去存在 → アーカイブ化 / 内容統合済 / 再作成判断 |
| `../../06_research/2026-05-18_収益化定期調査_初回` | 4 + 1 = 5 | 同上 |
| `../../20_reviews/2026-05-19_承認候補選定` | 4 + 3 = 7 | 同上 |
| `../decision-log` / `../05_monetization/decision-log` / `decision-log` | 7 + 6 + 1 = 14 | 意思決定ログ / 過去存在 |
| `../progress追加ToDo案` / `progress追加ToDo案` | 11 + 2 + 1 = 14 | 過去設計 |

→ **対応案**: 内容が他ページに統合済なら統合先を確定してリンク書き換え。再作成が必要なら別タスク化。

### C. vloop ログ / コマンド参照

| 不在ターゲット | 参照数 | 対応 |
|---|---|---|
| `../vloop` / `../03_prompts/claude-commands/vloop` 等 | 8 + 1 + 2 + 1 + 1 = 13 | vloop コマンドの正本パス確認 → リンク統一 |
| `vloop_2026-05-24_0048` | 6 | 過去 vloop ログ → アーカイブ済 or 名前変更 |

→ **対応案**: vloop コマンドの正本パスを 1 つに定め、すべての参照を統一。

### D. 設計 / 資産系の不在ページ

| 不在ターゲット | 参照数 | 対応 |
|---|---|---|
| `../既存資産_収益転用候補` | 4 + 1 = 5 | 過去存在 → 別ページに統合の可能性 |
| `../ranking-rule` | 4 | 過去ルール |
| `../03_prompts/AgentSDKクレジット活用方針` | 4 + 1 = 5 | 過去設計 |
| `../03_prompts/Codexレビュー常用運用` | 3 + 1 = 4 | 過去ルール |
| `../05_monetization/cron移行判定基準` | 4 + 1 = 5 | 過去設計 |
| `../03_prompts/Epic単位運用` | 2 | 過去ルール |
| `../05_monetization/案工場_完全自動化フロー` | 2 + 1 = 3 | 過去設計 |
| `../既存資産台帳` | 2 | 過去存在 |
| `../90_templates/chatgpt-approval-template` | 3 | 過去テンプレ |

→ **対応案**: 全体的に「過去存在 → 統合 or 削除」の判断が必要。実体作成は内容が必要なものに限る。

### E. テンプレ内プレースホルダ（無視可・対応不要）

| 不在ターゲット | 参照数 | 性格 |
|---|---|---|
| `link` | 7 | テンプレの `[[link]]` プレースホルダがそのまま残った例 |
| `YYYY-MM-DD_<slug>` | 3 | レビューテンプレ内の placeholder |
| `note-name` / `<note-name>` | 1 + 1 = 2 | テンプレ placeholder |
| `サンプル` | 1 | サンプル例示 |
| `...` | 1 | 省略表示 |
| `リンク or URL` | 1 | 案内文 |
| `important/` | 1 | フォルダ参照（Markdown placeholder）|

→ **対応案**: 偽陽性として除外。テンプレの placeholder は意図的なもの。

### F. データファイル参照（.ndjson 等）

| 不在ターゲット | 参照数 | 対応 |
|---|---|---|
| `idea_pool/2026-05-21.ndjson` / `../idea_pool/2026-05-21.ndjson` | 3 + 1 = 4 | 実体は `.ndjson` で .md ではない → `[[link]]` で参照すべきではない（Obsidian は .md のみ）/ 削除 or 通常リンク化 |
| `idea_pool/_README` | 2 | フォルダ README 参照 |

→ **対応案**: `.ndjson` は `[[link]]` の対象外。`![[link]]` ではなく `[name](path)` の通常 Markdown リンクに書き換え。

---

## 3. 修正方針案（次サイクル以降）

### 案 X: 最低限の修正のみ（推奨 / 安全側）

- カテゴリ A の運用ルールページのみ「廃止 / 統合済」リダイレクト 1 ページ作成（`20_reviews/廃止ページ一覧.md`）
- カテゴリ E（テンプレ placeholder）は対応不要
- カテゴリ F は通常リンクに書き換え（個別対応 / 4 件のみ）

### 案 Y: 全件対応（網羅 / 大規模）

- すべての壊れたリンク（185 件）を 1 件ずつ修正
- 統合元 / アーカイブ済 / 再作成のいずれかを判断
- 大規模作業（推定 4-8 時間）

### 案 Z: 削除中心（破壊的）

- 壊れたリンクを単純に削除（リンク先をテキストに置き換え）
- 情報損失リスクあり / 元のページに戻ったときに辿れなくなる

→ **Claude 推奨**: 案 X（最低限の修正のみ）+ カテゴリ F の 4 件のみ次サイクルで実施。カテゴリ A-D の網羅修正は ChatGPT 方向性レビューで方針決定後に実施。

---

## 4. 個別ファイル別 broken link 分布（上位 20 ファイル）

| ファイル | broken link 数 |
|---|---|
| 20_reviews/ChatGPT承認待ち.md | 13 |
| 20_reviews/candidate-001_ChatGPT承認パック.md | 17 |
| 20_reviews/candidate-005_ChatGPT承認パック.md | 8 |
| 05_monetization/scenarios/candidate-001_progress投入設計.md | 5 |
| 05_monetization/scenarios/candidate-005.md | 7 |
| 05_monetization/idea_trace.md | 9 |
| 05_monetization/epics.md | 5 |
| 05_monetization/scenarios/candidate-005_progress投入設計.md | 8 |
| 06_research/hermes-agent-codex-組み込み検討.md | 11 |
| 03_prompts/claude-commands/logs/vloop_2026-05-24_2202.md | 4 |
| 03_prompts/Claude×vloop×Obsidian×Progress関係図.md | 3 |
| 05_monetization/scenarios/candidate-001.md | 3 |
| 05_monetization/scenarios/candidate-001_7日実行プラン.md | 6 |
| 05_monetization/scenarios/candidate-001_公開ブロッカー.md | 4 |
| 05_monetization/scenarios/candidate-006_progress投入設計.md | 5 |
| 05_monetization/scenarios/candidate-007_progress投入設計.md | 5 |
| 20_reviews/Vault全体棚卸し.md | 3 |
| 20_reviews/案件別ToDo一覧.md | 6 |
| 20_reviews/次に実体化するToDo.md | 3 |
| 90_templates/session-review-template.md | 1 |

> **観察**: ChatGPT 承認パック系（candidate-001/005）と過去設計ページの参照が集中。**運用ルールページの統合先を 1 つ決めれば 50 件以上が一括解消**できる可能性。

---

## 5. ChatGPT 方向性レビュー時の質問

```
質問: 壊れたリンク 185 件の修正方針（案 X / Y / Z）はどれを採用すべきか？

Claude 推奨: 案 X（最低限の修正のみ）
根拠: 安全側 / 情報損失最小 / カテゴリ A 統合先確定で大量解消可能

確認したいこと:
1. 過去存在していた運用ルール（Vault蓄積運用ルール / 案件別ToDo運用ルール / ChatGPT承認ゲート標準 / decision-log / progress追加ToDo案 等）は統合済か / 再作成が必要か
2. 過去調査ファイル（2026-05-22_上位5案追加調査 / 2026-05-19_承認候補選定）はアーカイブされたか
3. カテゴリ F（.ndjson 等のデータファイル参照）は通常 Markdown リンクに変えてよいか
4. カテゴリ E（テンプレ placeholder）は無視で良いか
```

---

## 6. 完了条件（本ページ）

- 全 .md 横断 grep 検査の実施 ✅
- 壊れたリンク 185 件の検出 ✅
- カテゴリ A-F の分類 ✅
- 修正優先度の提示 ✅
- 修正方針案 X/Y/Z の比較 ✅
- ChatGPT 方向性レビュー時の質問 ✅
- カテゴリ F（.ndjson 等）の次サイクル実施候補確定 ⏳ ChatGPT 判断後

→ 完了条件 6/7 達成（カテゴリ F 実施は ChatGPT 判断後）

---

## 7. 未対応 / 仮説

- 「Vault蓄積運用ルール」等の統合先が確定していない（仮説: 案件別ToDo運用ルール / Vault全体棚卸し 等に分散統合済）
- 修正作業は次サイクル以降（本サイクルは検査結果整理まで）
- カテゴリ F の `.ndjson` 通常リンク化は破壊的でない安全な変更だが、次サイクルでまとめて実施

## 8. 次の一手

1. ChatGPT 方向性レビューで案 X/Y/Z の選択 + カテゴリ A 統合先確定
2. 次サイクル vloop12 でカテゴリ F の `.ndjson` 通常リンク化（4 件）
3. 統合先確定後、カテゴリ A の一斉書き換え（推定 50 件以上一括解消）

## 関連

- [[Vault全体棚卸し]] — Issue #59 残作業の親
- [[../00_inbox/Vaultの見方_どこを見れば何がわかるか]] — 本サイクルで簡略化済
- [[../20_reviews/案件別ToDo一覧]] — A セクション残作業の起点
- Issue: kaeru07/vault#59 / #69
