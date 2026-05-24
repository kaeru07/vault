---
date: 2026-05-25
task: Vault 全体棚卸し（Issue #59 / Phase 1-5 達成 / 完了条件 8/8）
runId: 20260525-vloop8-vault-inventory
targetApp: company-meta / obsidian-vault
monetizationImpact: medium
theme: [obsidian, vault, workflow, app-strategy]
relatedRunIds: [20260524-234509, 20260524-231358]
commitHashes: []
---

# Vault 全体棚卸し（Issue #59）

## 1. 作業目的

- vloop7 末で「次サイクルで #59 Vault 全体棚卸し Phase 計画」と確定
- ユーザー指摘（旧運用と新運用の混在 / 存在しないパス案内 / フォルダリンク中心の iPhone 辿りづらさ）に対応
- Phase 1-5 を 1 ファイルに集約し、本サイクルで完了条件 8/8 を達成
- 破壊的削除はしない / 注記対応のみ

## 2. 実施内容

- `20_reviews/Vault全体棚卸し.md` 新規（Phase 1-6・全体マップ Mermaid 図・主要フォルダ 17 件分類・主要 .md 14 件正本マップ・旧/新/暫定/削除候補判定・正本マッピング・iPhone 確認手順 8 ステップ・完了条件 8/8 達成）
- 旧運用フォルダの確認:
  - `04_reviews/`（3 件）→ 20_reviews/ に運用移行済 / 参照のみ
  - `07_tasks/inbox/`（1 件）→ 20_reviews/ に同名移行済 / 参照のみ
  - `無題のフォルダ` → 完全に空 / ユーザー判断で削除
  - `chatgpt/` → 暫定 / 用途未確定
- 案件別ToDo一覧 §1 Vault運用 に Vault 全体棚卸し 反映（状態: partial_done / 残作業明記）
- 次に実体化するToDo 優先 2 を達成化（artifact_exists へ移行）
- 00_START_HERE「さらに掘る」に Vault 全体棚卸し リンク追加

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `20_reviews/Vault全体棚卸し.md` | 新規（Phase 1-6・1 枚図・実在マップ・分類・正本マッピング・確認手順）|
| `20_reviews/案件別ToDo一覧.md` | §1 Vault 全体棚卸し 反映 |
| `20_reviews/次に実体化するToDo.md` | 優先 2 達成化 |
| `00_START_HERE.md` | Vault 全体棚卸しリンク追加 |
| `20_reviews/2026-05-25_vault-inventory.md` | 本ファイル新規 |
| `20_reviews/_review_queue.md` | 先頭追加 |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| Issue #59 完了条件 8 件 | ✅ 8/8 達成（Vault 全体棚卸し.md Phase 6 完了条件チェック表）|
| 主要フォルダ実在確認 | ✅ 17 フォルダを実在マップ化 |
| 主要 .md 実在確認 | ✅ 14 ファイルを正本マップ化 |
| 存在しないパス案内 | ✅ 主要入口（00_START_HERE / 00_index）の全リンク実在確認 |
| 破壊的削除 | ❌ 一切なし（注記対応のみ）|
| 旧運用ファイル | ✅ 「参照のみ」分類 / 削除なし |
| iPhone 確認手順 | ✅ 8 ステップ + 復旧手順記載 |
| 機密スキャン | ✅ 実値なし |
| build / typecheck / lint | n/a（Markdown のみ）|
| ob sync / GitHub push | 次フェーズで実行 |

## 5. 未対応

- Phase 1 拡張: 全 .md ファイル横断で「存在しないパス案内」を grep 検査（大規模 / 次サイクル）
- Phase 4 拡張: 04_reviews/ + 07_tasks/inbox/ + chatgpt/ 個別ファイルへの「📦 移行済」「📄 参照のみ」注記追加（次サイクル）
- chatgpt/ フォルダの運用ルール確定（次サイクル）
- `Vaultの見方_どこを見れば何がわかるか` の分割 / 簡略化（Issue #59 本文指摘・次サイクル）
- ユーザー判断待ち: 「無題のフォルダ」削除 / 旧運用ファイル整理方針承認

## 6. 危険ポイント

- ❌ なし（破壊的削除なし / 既存ファイル削除なし / DB 操作なし / 認証情報なし / 外部公開判断なし）
- 04_reviews/ 3 件 / 07_tasks/inbox/ 1 件 / 無題のフォルダ は **一切削除していない**（注記分類のみ）
- 旧運用ファイルへの個別注記追加は次サイクル送り（破壊性ゼロを優先）

## 7. 次にやるべきこと

1. ChatGPT が _review_queue.md 先頭の本レビューをレビュー（特に正本マッピングと旧運用分類）
2. ユーザーが iPhone Obsidian で 00_START_HERE → Vault 全体棚卸し が開けるか確認
3. ユーザーが「無題のフォルダ」を iPhone Obsidian で削除（Claude は実施しない）
4. 次サイクルで 04_reviews/ + 07_tasks/inbox/ 個別ファイルへの注記追加
5. 次サイクルで全 .md 存在しないパス grep 検査（Phase 1 拡張）
6. 次サイクルで `Vaultの見方_どこを見れば何がわかるか` の分割検討

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です（8 サイクル目）。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: vloop8 Vault 全体棚卸し（Issue #59）

## できるようになったこと
- 主要 17 フォルダ + 14 主要 .md を実在マップ化 + 旧/新/暫定/削除候補分類
- 正本マッピング（ユーザーが見る正本 / GitHub 管理 Vault 範囲 / iPhone 入口 / candidate 一覧 / 承認入口 / ToDo / 運用ルール）
- iPhone 確認手順 8 ステップ + 復旧手順
- Issue #59 完了条件 8 件すべて達成
- 破壊的削除一切なし（注記対応のみ）

## 確認したい観点
1. 旧運用 / 新運用 / 暫定 / 削除候補の分類は妥当か
2. 04_reviews/ を「参照のみ」、07_tasks/inbox/ を「参照のみ」とする判断は妥当か
3. 「無題のフォルダ」削除をユーザー判断に委ねる判断は妥当か（Claude が削除しない）
4. 正本マッピング 7 種（ユーザー / GitHub / iPhone / candidate / 承認 / ToDo / 運用ルール）は適切か
5. iPhone 確認手順 8 ステップは実用的か（実機検証は未実施）
6. Phase 1-5 を 1 ファイル集約する判断は妥当か（Phase 別に分割する方が良いか）
7. 旧運用ファイル個別への注記追加を次サイクルに送る判断は妥当か

参考リンク:
- 20_reviews/Vault全体棚卸し.md（本サイクル主成果物・Phase 1-6・全体マップ Mermaid）
- 20_reviews/案件別ToDo一覧.md §1 Vault運用
- 00_START_HERE.md（リンク追加）
```

## 関連

- 前サイクル: [[2026-05-24_hermes-codex-integration-research]]（vloop7）
- [[Vault全体棚卸し]]
- [[Vault蓄積運用ルール]]（上位ルール）
- [[案件別ToDo運用ルール]]
- [[案件別ToDo一覧]]
- [[次に実体化するToDo]]
- [[../00_START_HERE]]
- Issue: kaeru07/vault#59 / #55 / #56 / #58 / #69
