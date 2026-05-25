---
type: monetization-execution-plan
title: candidate-007（vault-search-cheatsheet）承認後 7 日実行プラン
date: 2026-05-25
issue: kaeru07/vault#61
scenario: candidate-007
status: 計画のみ（承認後に着手・progress 投入は人間）
---

# candidate-007（vault-search-cheatsheet）承認後 7 日実行プラン

> Issue #61 / #80。candidate-007 が **approved になった後**すぐ動ける状態を作る計画。
> **計画のみ。approved 化・progress 投入・本番実装はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> 起点は [[candidate-007_公開ブロッカー]] の解除順（B1→B2→B3→B9→B5→B4→B7→B8→B6）。

> [!note] 用語注（Issue #69）
> - approved / 承認済み = ChatGPT + あなたの判断で「実装着手 OK」になった状態
> - Day0 = 承認判断の日 / Day1 = 承認翌日（実作業 1 日目）
> - B1〜B9 = 公開ブロッカー（公開を阻む問題）の種別番号
> - progress 投入 = progress（作業履歴アプリ・localhost:3010）への ToDo 追加
> - Vercel = 静的サイト・Next.js を無料層でホストできる代表サービス
> - 統合方針 A/B/C = 既存「Vault の見方ガイド」と本候補をどう統合するかの 3 案
> - Vault Search Cheatsheet = iPhone Obsidian + GitHub Vault の検索パターン早見表 HTML
> - sticky header = スクロールしてもページ上部に固定表示されるヘッダ
> - note 化 = note.com 等で有料記事として情報をまとめて販売すること

## 前提（Day0・人間 + ChatGPT）

- candidate-007 が `approved`（ChatGPT 方向性承認 → 人間 status 確定）
- **統合方針 A/B/C が決定済**（Day0 までに ChatGPT 方向性レビューで決定）
- 作業は `90_prototypes/vault-search-cheatsheet/` 配下で完結（既存 vault リポジトリ内・別アプリ作成不要）
- 公開先 Vercel 想定（無料層・静的構成と相性最良）

## 7 日プラン（1 日単位 ToDo / 完了条件 / 依存）

| Day | ToDo | 完了条件 | 依存 |
|---|---|---|---|
| Day1 | B1: iPhone Safari 実機確認 + B9 規約明文化 | iPhone で検索ボックス・テーブル・sticky header 動作確認 / 「個人 Vault 運用ノウハウの公開可能範囲」1 行記録 | 承認(Day0) + 統合方針決定 |
| Day2 | B2: 既存「Vault の見方ガイド」との重複整理（決定した統合方針 A/B/C を実施） | 重複箇所の整理完了 / 統合方針通りの状態に到達 | Day1 |
| Day3 | B3: Vercel デプロイ（無料層 / GitHub 連携） | 本番 URL 確定 / iPhone でも本番 URL で動く | Day2 |
| Day4 | B5: iPhone Tips / GitHub Tips セクション拡充（スクショ + キャプション 5 枚以上） | スクショ + キャプション 5 件以上が `index.html` に反映 | Day3 |
| Day5 | B4: 検索パターン蓄積運用ルール（毎週 1〜2 件追加する仕組み） | 蓄積運用ルール 1 ページ + 既存 20 件の補強 | Day2（並行可）|
| Day6 | B7: 「Vault 検索チートシート Markdown 拡張版」テンプレドラフト（販売はしない） | 拡張版 Markdown 雛形完成 | Day4 |
| Day7 | B8: 「個人開発 Vault 運用ガイド」note 1 本目ドラフト + 公開可否レビュー | note ドラフト + 公開 Go/No-Go 判断材料 1 枚集約 | Day1-6 |

## 最初の 3 タスク（progress 投入候補・承認後のみ）

1. **B1 + B9 iPhone 実機確認 & 規約明文化**（Day1）
2. **B2 重複整理（統合方針 A/B/C 実施）**（Day2）
3. **B3 Vercel デプロイ**（Day3）

> これらは **approved 後に人間が progress 投入**する候補。AI は送らない（[[../progress連携基準]]）。

## 依存関係（要約）

- Day0(承認 + 統合方針決定) → Day1(B1+B9) が全ての起点
- Day2(B2 重複整理) は統合方針決定が前提
- Day5(B4 運用ルール) は Day2 後に並行可
- Day6(B7 テンプレ)・Day7(B8 note) は Day4 後

## 未対応点 / 仮説

- 各 Day の所要は標準的な軽作業前提の仮置き
- B2 の統合方針は ChatGPT 方向性レビュー時に決定される前提（決定が遅れたら Day2 以降を保留）
- candidate-007 は ChatGPT 方向性レビュー前段階のため本プランは待機状態。承認まで Day1 に進めない（AI 先行着手しない）

## 次の一手

1. ChatGPT が candidate-007 を方向性レビュー + 統合方針 A/B/C 決定（[[../../20_reviews/candidate-007_ChatGPT承認パック]]）
2. ユーザーが iPhone 実機で `90_prototypes/vault-search-cheatsheet/index.html` 動作確認
3. 承認後、人間が最初の 3 タスクを progress 投入 → Claude が /focus か /vloop で実行
4. Day1 から本プランで実行（90_prototypes/vault-search-cheatsheet 配下）

## 関連

- [[candidate-007]] / [[candidate-007_公開ブロッカー]] / [[candidate-007_progress投入設計]]
- [[../../20_reviews/candidate-007_ChatGPT承認パック]]
- [[../../90_prototypes/vault-search-cheatsheet/README]]
- [[../../00_inbox/Vaultの見方_どこを見れば何がわかるか]]（既存資産・重複整理対象）
- [[candidate-005_7日実行プラン]] / [[candidate-006_7日実行プラン]]（形式参考・並走候補）
- Issue: kaeru07/vault#61（前提 #55 / #58 / #63 / #80）
