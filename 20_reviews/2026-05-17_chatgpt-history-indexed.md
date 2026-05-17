---
date: 2026-05-17
task: ChatGPT履歴ZIP(健全再UP)の安全解析→索引・重要会話・要約のObsidian整理
runId: 20260517-185136
targetApp: company-meta
monetizationImpact: medium
theme: [workflow, obsidian, market-research]
relatedRunIds: [20260517-172645, 20260517-145608]
commitHashes: []   # レビュー対象の作業 commit / 関連 commit のみ。本ファイル自身の push commit は記録不要
# reviewFileCommit:
---

# 2026-05-17 ChatGPT履歴ZIP 安全解析→Obsidian索引化

> 1 作業 = 1 ファイル の自動生成レビュー。詳細ルールは `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール。

---

## 1. 作業目的

- 前回破損していたChatGPT履歴ZIPが完全版(613MB)で再アップロードされ健全化 → 全会話Markdown化せず、索引・重要会話抽出・要約に限定してObsidianナレッジ化する
- 方針: **Vaultを重くしないことを最優先**

## 2. 実施内容

- ZIP整合性確認(`unzip -t` = No errors)
- 一時ディレクトリ `/tmp/chatgpt-import-v2/` に **会話JSON+小メタのみ**展開(92MB)。画像647/PDF21/確定申告書類/明細CSV 約1GBは**非展開**
- 661会話を解析: タイトル/日時/本文抽出 → カテゴリ自動分類 → 重要度スコアリング → 機密スキャン
- `00_inbox/chatgpt-import/` に索引6 + `important/`統合要約8 を生成(計116KB)
- 「枝分かれ」重複会話(Obsidian活用法系6+/AI運用自動化系3+)を1要約に統合してVault軽量化
- 破損ZIP時の旧 `インポート概要.md` を廃止扱いに更新(経緯記録として残置)

## 3. 変更ファイル

| ファイル | 変更 |
|---|---|
| `00_inbox/chatgpt-import/00_インポート概要.md` | 新規(健全ZIP索引化概要) |
| `00_inbox/chatgpt-import/01_会話インデックス.md` | 新規(全661軽量索引・自動生成) |
| `00_inbox/chatgpt-import/02_重要会話候補.md` | 新規(high63メタ) |
| `00_inbox/chatgpt-import/03_カテゴリ別まとめ.md` | 新規 |
| `00_inbox/chatgpt-import/04_Git管理Vault反映候補.md` | 新規 |
| `00_inbox/chatgpt-import/05_次にやるToDo候補.md` | 新規 |
| `00_inbox/chatgpt-import/important/*.md` | 新規8件(重複統合要約・全文転載なし) |
| `00_inbox/chatgpt-import/インポート概要.md` | 更新(破損時旧概要を廃止扱い) |

## 4. 検証結果

- typecheck/build/lint: n/a(ファイル整理)
- 手動: unzip -t=No errors / 661会話・期間2023-04-05〜2026-05-16集計 / U+FFFD置換文字なし(文字化けなし) / 既存Vault構成は chatgpt-import/配下のみ追加で非破壊 / 元ZIP 613,455,646B 前後不変(非削除)
- ob sync: Fully synced(md のみ。613MB ZIPはObsidian Sync非対象でクラウド非肥大)
- 機密チェック: 確定申告書類/明細CSV/user.json(PII)/会話14件の認証記述 → 値・本文を一切非記載、種別のみ

## 5. 未対応

- chat.html/添付画像/確定申告書類: 意図的に非展開(重量・機密)
- 未分類226の精緻化: 低重要度中心で未実施
- high63のうちindividual要約は重複統合後8テーマに限定(残りは索引参照)

## 6. 危険ポイント

- **613MBのZIPが 00_inbox/ 内に存在** → ob syncクラウドは非対象だがローカルVaultディレクトリ/将来のgitが重くなる。Vault外移動を推奨(削除しない・ユーザー判断)
- 添付の財務・個人情報: 非展開で防御済
- ロールバック: `chatgpt-import/` 配下削除で原状復帰
- 既存機能影響: なし(新規追加のみ)

## 7. 次にやるべきこと

- 613MB ZIPをVault外(例 `/root/company/_archives/`)へ移動(削除しない・ユーザー判断)
- `04_Git管理Vault反映候補.md` 優先度=高(意思決定ログ/Opus47プロンプト方針)のobsidian-vault反映可否判断
- `05_次にやるToDo候補.md` 高優先(アプリ案工場Phase1/Codexレビュー常用/market-research-hub)のprogress起票判断

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: company-meta
作業: ChatGPT履歴ZIP(健全再UP)の安全解析→Obsidian索引化
runId: 20260517-185136
日付: 2026-05-17

## 作業目的
- 健全化したChatGPT履歴ZIP(613MB/661会話)を全文Markdown化せず索引・重要抽出・要約に限定

## 実施内容
- 一時dirに会話JSON+小メタのみ展開(画像/PDF/確定申告書類 約1GB非展開)
- 661会話を分類・重要度判定・機密スキャン
- 索引6+重複統合要約8を 00_inbox/chatgpt-import/ に生成(116KB)

## 検証結果
- unzip -t OK / 文字化けなし / 既存Vault非破壊 / 元ZIP非削除 / ob sync Fully synced
- 機密(確定申告書類/PII/会話14件)は値・本文非記載で種別のみ

## 未対応
- 未分類226精緻化未実施 / individual要約は重複統合後8に限定

## 危険ポイント
- 613MB ZIPが00_inbox内 → Vault外移動推奨(非削除)

## 確認したい観点
- 「全文Markdown化せず索引+統合要約」の粒度は妥当か
- 重複会話の統合判断は妥当か
- 機密(確定申告書類/PII)の非展開・非記載対応は十分か
- カテゴリ分類(未分類226)の扱いは妥当か
- Vault軽量化(116KB)とナレッジ価値のバランスは妥当か
````

---

## 関連

- progress runId: 20260517-185136(正本) / 関連: 20260517-172645(破損ZIP時)
- 索引: [[../00_inbox/chatgpt-import/00_インポート概要]]
- フォルダ運用: [[../20_reviews/README]]
