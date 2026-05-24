---
title: START HERE — iPhone Obsidian からまず開くページ
type: vault-entry-en
issue: kaeru07/vault#56
relatedIssues: [kaeru07/vault#57, kaeru07/vault#58, kaeru07/vault#59]
created: 2026-05-23
updated: 2026-05-23
tags: [入口, START_HERE]
status: active
---

# 🚪 START HERE

> [!important] iPhone Obsidian で迷ったらここから開く
> 英数字ファイル名のため iPhone 検索でも見つけやすい。各リンクは**ファイル直リンク**でフォルダリンクではない。

---

## ✅ 今やることへ直リンク（4 つだけ）

> [!todo] ① 承認判断する → [[20_reviews/ChatGPT承認待ち|🗳 ChatGPT 承認待ち]]
> candidate-001 の承認コマンドはここで発行（candidate-001 approve / hold / reject）

> [!todo] ② candidate-001 を読む → [[20_reviews/candidate-001_ChatGPT承認パック|📦 candidate-001 承認パック §1-§14]]
> 市場 / 実装 / 収益 / 着手可否を 1 ファイルで判断できる

> [!todo] ③ 全候補を一覧する → [[05_monetization/scenarios/README|📋 candidate 一覧]]
> 4 件（001/002/003/004）+ candidate-001 補助 3 ファイルへ直リンク

> [!todo] ④ 全体進捗を見る → [[05_monetization/epics|🎯 Epic A/B/C/D 進捗]]
> Epic ごとの現在地と人間判定待ち項目

---

## 📚 さらに掘る（必要なら）

> [!note] 全アプリ案の trace（Issue #63）
> [[05_monetization/idea_trace|🧭 idea_trace — 全案の情報源・着想理由・採用判断]] — 情報源 → 気づき → 案 → 候補 → 試作 → 判断を 1 ページで追える

> [!note] candidate-005（承認材料 4 ファイル完備）
> [[05_monetization/scenarios/candidate-005|💎 candidate-005 LLM トークン速度ベンチ可視化（APIなし）]] — scoreTotal 28/40 / [[20_reviews/candidate-005_ChatGPT承認パック|📦 承認パック §1-§14]] / [[05_monetization/scenarios/candidate-005_公開ブロッカー|B1-B9]] / [[05_monetization/scenarios/candidate-005_7日実行プラン|Day1-7]] / [[05_monetization/scenarios/candidate-005_progress投入設計|progress 投入]] / [[90_prototypes/token-speed-tool/README|MVP モック]] / [[05_monetization/token-speed-tool|仕様 + trace]]

> [!note] 試作モック 3 件（Issue #60 / #61）
> - [[90_prototypes/token-speed-tool/README|🧪 token-speed-tool]] — APIなしで動く LLM ベンチ可視化ツール
> - [[90_prototypes/llm-chooser/README|🎯 LLM Chooser]] — Claude/Codex/Gemini/ローカル 使い分けチャート（N-03）
> - [[90_prototypes/vault-search-cheatsheet/README|🔍 Vault Search Cheatsheet]] — iPhone+GitHub Vault 検索パターン早見表（N-04）

> [!note] 試作ループ検証（Issue #61）
> [[05_monetization/試作ループ検証|🔁 試作ループ検証]] — 14 案追加 / 3 候補選定 / N-01 試作着地

> [!note] Vault の見方ガイド（収益化フロー判断手順）
> [[00_inbox/Vaultの見方_どこを見れば何がわかるか|📖 Vault の見方ガイド]] — candidate / idea_pool / research / vloop 用語解説

> [!note] Vault 全体構造索引
> [[00_index|🗂 00_index]] — フォルダ別・テンプレ・運用 QR

> [!note] 元ネタ（市場調査）
> [[06_research/2026-05-22_上位5案追加調査|🔬 上位 5 案追加調査]] — Epic B 収益化スコアリング根拠

> [!note] 自動化進捗
> [[06_research/logs/research-run-log|⚙ research-run 実行ログ]] — cron 移行 3 日連続達成（2026-05-23）

> [!note] 関係図
> [[03_prompts/Claude×vloop×Obsidian×Progress関係図|🗺 Claude × vloop × Obsidian × Progress 関係図]]

---

## 🔍 iPhone Obsidian の検索キーワード

> [!tip] 日本語ファイル名は検索でヒットしないことがある
> 以下の英数字 / 短いキーワードを推奨:

| 探すもの | 検索キーワード |
|---|---|
| 本ファイル | `00_START_HERE` または `START` |
| 全体索引 | `00_index` または `index` |
| 承認待ち | `承認待ち` または `ChatGPT` |
| candidate 詳細 | `candidate-001` または `candidate` |
| candidate 一覧 | `scenarios` または `candidate` |
| Epic 進捗 | `epics` |
| 見方ガイド | `Vaultの見方` または `00_inbox` |

---

## ☑ ユーザー確認用チェックリスト（Issue #57）

> [!check] iPhone Obsidian で順に確認
> - [ ] 検索で `00_START_HERE` → 本ファイルが開く
> - [ ] [[00_index]] へ遷移できる
> - [ ] [[00_inbox/Vaultの見方_どこを見れば何がわかるか]] へ遷移できる
> - [ ] [[20_reviews/ChatGPT承認待ち]] へ遷移できる
> - [ ] [[20_reviews/candidate-001_ChatGPT承認パック]] へ遷移できる
> - [ ] [[05_monetization/scenarios/README]] へ遷移できる（candidate 一覧）
> - [ ] [[05_monetization/scenarios/candidate-001]] へ遷移できる
> - [ ] [[05_monetization/epics]] へ遷移できる

> [!warning] 開けないリンクがある場合
> `obsidian-sync-vault` 側にファイル未反映の可能性。Claude に「Issue #56 逆反映ルール再実行」と依頼してください（[[03_prompts/同期導線_sync-vault逆反映|逆反映ルール]]）。

---

## 🔄 同期導線（運用方針）

> [!info] 2 つの Vault の関係
> - **稼働 Vault**: `obsidian-sync-vault`（ob sync で iPhone 同期）
> - **GitHub 管理 Vault**: `obsidian-vault`（git remote: vault.git）
> - **公式運用**: sync-vault → obsidian-vault に rsync ミラー（一方向）
> - **逆方向**: ChatGPT が GitHub 直接 commit したら個別コピー（[[03_prompts/同期導線_sync-vault逆反映]]）

詳細: [[03_prompts/GitHub反映ルール]] / `/root/company/CLAUDE.local.md` § GitHub 管理 Vault 反映ルール

---

## 📎 関連

- [[00_index]] — Vault 全体構造索引
- [[00_inbox/Vaultの見方_どこを見れば何がわかるか]] — 収益化フロー見方ガイド
- [[20_reviews/README]] — ChatGPT 承認入口
- [[03_prompts/Claude×vloop×Obsidian×Progress関係図]] — 1 枚図
- Issue: kaeru07/vault#56 / #57 / #58 / #59
