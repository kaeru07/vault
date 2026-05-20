---
title: ChatGPT レビュー時の標準手順（GitHub を読んでからレビューする）
type: review-ops
issue: kaeru07/vault#40
relatedIssues: [kaeru07/vault#41, kaeru07/vault#21]
created: 2026-05-20
updated: 2026-05-20
status: active（ChatGPT 側運用ルール / Claude/vloop の実行対象ではない）
target_actor: ChatGPT
---

# ChatGPT レビュー時の標準手順

> Issue #40。ユーザーが「レビューして」と依頼したときに、ChatGPT が**推測ではなく GitHub/Vault の実データを確認してからレビューする**ための手順。
> **本ルールの実行主体は ChatGPT**（Issue #41 で「Claude/vloop 実行対象外」と整理）。
> Claude/vloop はこのルールに従わない（読まないし実行しない）。

## 1. レビューの主体分担（前提）

| 主体 | 役割 |
|---|---|
| **Claude / vloop** | GitHub/Vault 上の作業・実装・整理・commit/push |
| **ChatGPT** | GitHub を読んでレビュー → 判断・承認・次 ToDo 起票 |
| **人間** | 最終承認・status 確定・公開/課金判断 |

本ルールは **ChatGPT 側の運用ルール**。Issue #40 / #41 は Claude/vloop の消化対象ではない（[[../03_prompts/claude-commands/vloop]] § Claude 実行対象外 Issue）。

## 2. レビュー前の標準確認手順（6 ステップ）

ChatGPT は「レビューして」と言われたら、**必ず以下を順番に GitHub で確認**してから本文レビューに入る:

### Step 1. GitHub の直近 commit を確認
- URL: `https://github.com/kaeru07/vault/commits/main`
- 直近 5〜10 commit を読む
- commit message から「何が変わったか」の概要を掴む

### Step 2. 該当 Issue の状態を確認
- レビュー対象が特定 Issue のとき: `https://github.com/kaeru07/vault/issues/<N>`
- Issue 本文 / 完了条件 / 既存コメント（Claude/vloop の完了報告）を読む
- 関連 Issue（#XX）があれば併せて確認

### Step 3. 変更ファイルを確認
- 該当 commit の diff を読む: `https://github.com/kaeru07/vault/commit/<hash>`
- 変更行数が多い場合は frontmatter とセクション見出しを先に把握
- 機密混入の有無を最低限スキャン

### Step 4. 必要なら関連 Markdown を確認
- ファイル本体: `https://github.com/kaeru07/vault/blob/main/<path>`
- 関連 `[[link]]` 先（前提ルール・テンプレ等）を辿る
- `_review_queue.md` も参照（未チェック項目から対象 Issue を辿るのも可）

### Step 5. 完了条件を満たしているか確認
- Issue 本文「完了条件」を 1 つずつチェック
- 「設計のみ」「実装は人間承認後」等の保留条件があるかを判定
- 「実稼働」「実取得」等の動的条件は未充足のままでも OK か判定

### Step 6. 作成物の場所と使い方をレビューに含める
- 後述「§4 レビュー結果に必須の 8 項目」を埋める
- 場所（パス）と使い方（誰がどう使うか）が分かる形でレビューする

## 3. レビュー結果に必須の 8 項目

レビューの最後に**必ず**以下 8 項目を明記する:

| 項目 | 書く内容 |
|---|---|
| 1. 何ができたか | 成果物 1 行サマリー |
| 2. どこで見れるか | GitHub URL or Vault パス |
| 3. 何に使うか | 用途・効く運用 |
| 4. どう使うか | 具体的な使い方 |
| 5. 良い点 | 内容上の強み（具体的に） |
| 6. 気になる点 | リスク / 抜け / 後で困る箇所 |
| 7. 次に見るファイル | 関連リンク（`[[link]]`） |
| 8. 次にやること | フォローアップ ToDo / 承認判断項目 |

> Issue #21（[[../04_reviews/Claude作業レビュー運用]] §成果物紹介）の 7 項目を拡張し、「良い点 / 気になる点」を追加した形。

## 4. レビューしないもの（範囲外）

ChatGPT は以下を独断で実行しない:

- 直接 commit / push（GitHub Web UI 経由で edit しない）
- Issue を独断で close（人間判断）
- candidate を approved に上げる（人間のみ）
- progress 直接投入（[[../05_monetization/progress連携基準]]）
- 外部公開 / 課金 / 広告操作（人間のみ）

判断材料を整理して**人間へ渡す**ところまで。

## 5. 推奨フロー（ユーザーが「レビューして」と言ったとき）

```
ユーザー: 「レビューして」（対象不明）
   ↓
ChatGPT: 「対象は？」と聞く前に 20_reviews/_review_queue.md の未チェック先頭を見る
   ↓ 未チェック [ ] 行がある
ChatGPT: その対象を Step 1〜6 で確認 → 8 項目で報告
   ↓ ユーザーが追加対象指定
ChatGPT: その Issue/commit を Step 1〜6 で確認 → 8 項目で報告
```

> 確認質問の前に**まず GitHub を読む**。読んだ上で不足があれば確認する。

## 6. 反映箇所（Issue #40 の「反映候補」対応）

Issue #40 の「反映候補」と本ファイルの対応:

| Issue 反映候補 | 本ファイルとの関係 |
|---|---|
| `20_reviews/Claude作業レビュー運用.md` | Claude 側の作業レビュー運用（既存・Issue #21 で 7 項目化）。**主体が Claude**なので本ファイルとは別 |
| `90_templates/session-review-template.md` | Claude が作る作業レビューのテンプレ（既存・Issue #21 で §9 成果物紹介）。**主体が Claude** |
| `03_prompts/claude-commands/vloop.md` | Claude/vloop の実行ルール。本ファイルは**読まない側**（実行対象外）。vloop に「Claude 実行対象外 Issue」セクションを追加（Issue #41 対応） |
| `03_prompts/Claude-Code標準運用.md` | Claude の標準運用。本ファイルとは主体が違う |
| **本ファイル** | **ChatGPT 側の標準手順**。Issue #40 / #41 の責務分離後の格納先 |

## 7. 完了条件と現状（Issue #40）

| Issue #40 完了条件 | 現状 | 達成手段 |
|---|---|---|
| レビュー時の GitHub 確認手順が明記 | **完了**（§2 Step 1〜6） | 本ファイル |
| 成果物の場所と使い方を含めるレビュー形式が明記 | **完了**（§3 8 項目） | 本ファイル |
| commit/push 済 | 本ファイル commit/push 済 | — |

3 項目すべて充足。

## 関連

- [[claude-commands/vloop]]（Claude 実行対象外 Issue セクションを参照）
- [[../04_reviews/Claude作業レビュー運用]]（Claude 側レビュー運用・主体違い）
- [[../90_templates/session-review-template]]（Claude 側テンプレ）
- [[../20_reviews/_review_queue]]（レビュー入口）
- Issue: kaeru07/vault#40 / 関連 #41 / #21
