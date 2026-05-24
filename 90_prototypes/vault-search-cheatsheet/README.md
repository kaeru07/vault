---
title: Vault Search Cheatsheet — iPhone Obsidian + GitHub Vault 検索パターン早見表
type: prototype
issue: kaeru07/vault#61
relatedIssues: [kaeru07/vault#55, kaeru07/vault#58, kaeru07/vault#63]
created: 2026-05-24
updated: 2026-05-24
status: MVP モック完成（N-04 ID）
tags: [prototype, no-api, vault, search, obsidian]
---

# Vault Search Cheatsheet

> 試作ループ Phase6（N-04）。**iPhone Obsidian + GitHub Vault の検索パターン早見表**を静的 HTML 1 枚にまとめた。

## 構成

| ファイル | 役割 |
|---|---|
| `index.html` | UI 一式（検索ボックス + 20 件キーワード対応表 + iPhone/GitHub 検索 Tips + トラブル対応） |
| `README.md` | 本ファイル |

## 使い方

```bash
xdg-open /root/company/obsidian-vault/90_prototypes/vault-search-cheatsheet/index.html
# またはローカル配信
python3 -m http.server 8767 -d /root/company/obsidian-vault/90_prototypes/vault-search-cheatsheet
```

## 機能

- ページ内検索ボックスで 20 件のキーワード対応表を絞り込み
- iPhone Obsidian の検索 Tips（クイックスイッチャー / tag: / path: 等）
- GitHub Web の検索 Tips（filename: / path: / extension: 等）
- iPhone と GitHub で結果が違うときの対応フロー
- 完全静的 HTML（外部 CDN ゼロ・file:// 動作）

## 収益化導線

- アプリ広告（Obsidian / Vault ユーザ向け・低 CPC だが**読了率高**）
- テンプレ販売（「Vault 検索チートシート Markdown 拡張版」）
- note 販売（「個人開発 Vault 運用ガイド」）

## 既知制約

- キーワードリストは**個人運用に最適化**された 20 件のみ
- iPhone Obsidian / GitHub Web の検索仕様変更には手動メンテで追従

## 次の改善

1. iPhone 実機表示確認
2. 既存「Vault の見方ガイド」（`00_inbox/Vaultの見方_どこを見れば何がわかるか.md`）と内容の重複整理
3. キーワード追加（Phase A 運用で増えた語彙: progress / Activity Mining / Hermes 等）
4. 検索ボックスのフォーカスショートカット（<kbd>/</kbd> でフォーカスなど）

## 関連

- spec/source: [[../../05_monetization/idea_trace]]（案 N-04）
- 元になった Markdown: [[../../00_inbox/Vaultの見方_どこを見れば何がわかるか]]
- START HERE: [[../../00_START_HERE]]
- Issue: kaeru07/vault#61
