---
type: monetization-blocker-list
title: candidate-007（vault-search-cheatsheet）公開ブロッカー一覧
date: 2026-05-25
issue: kaeru07/vault#61
scenario: candidate-007
status: 調査整理のみ（実装・公開はしない）
---

# candidate-007（vault-search-cheatsheet）公開ブロッカー一覧

> Issue #61 / #80。candidate-007 [[candidate-007]]（90_prototypes/vault-search-cheatsheet/）の**公開できない原因を特定**する。
> **読み取り調査のみ。実装・公開・approved 化・progress 投入はしない**（[[../収益化シナリオ承認フロー]] / /vloop 禁止事項）。
> candidate-007 は現在 `candidate`（chatgpt_pending 前段階）。本作業は**ChatGPT 方向性レビュー前の事前整理**。

> [!note] 用語注（Issue #69）
> - 公開ブロッカー = 公開を阻む問題（B1〜B9 で番号管理）
> - candidate / 有力候補 = 収益化する価値があるかもしれない案
> - chatgpt_pending = ChatGPT 方向性レビュー待ちの状態
> - approved / 承認済み = ChatGPT + あなたの判断で「実装着手 OK」になった状態
> - viewport 設定 = スマホで表示が崩れないようにする HTML の設定
> - Vault Search Cheatsheet = iPhone Obsidian + GitHub Vault の検索パターン早見表 HTML
> - 既存「Vault の見方ガイド」 = [[../../00_inbox/Vaultの見方_どこを見れば何がわかるか]] / 重複整理対象
> - 統合方針 A/B/C = 既存ガイドと本候補をどう統合するかの 3 案（A 併存 / B 既存索引化 / C 既存に統合）
> - Vercel / Netlify = 静的サイトを無料層で公開できる代表サービス
> - AdSense = Google の Web 広告 SDK
> - sticky header = スクロールしてもページ上部に固定表示されるヘッダ

## 調査メタ（妥当性）

- どこを調べたか: `90_prototypes/vault-search-cheatsheet/`（index.html / README.md）+ [[candidate-007]] 仕様 + [[../../00_inbox/Vaultの見方_どこを見れば何がわかるか]] 既存ガイド / 2026-05-24 / n=1 試作 + 1 既存資産
- 情報源種別: 内部一次（試作リポジトリ実体 + 既存 Vault 資産）+ 個人運用観察
- **未検証**:
  - iPhone Safari 実機での操作・表示は未確認
  - 公開先（Vercel / Netlify / GitHub Pages）の選定は未決定
  - 既存「Vault の見方ガイド」との重複整理方針が未確定
  - 広告アカウントの整備状況は未確認

## ブロッカー一覧（種別 × 優先度 × 対応コスト）

| #  | 種別      | 内容                                                                            | 状態       | 優先度   | 対応コスト                       |
| -- | ------- | ----------------------------------------------------------------------------- | -------- | ----- | --------------------------- |
| B1 | 技術      | iPhone Safari 実機で操作・表示確認していない（検索ボックス・テーブル・sticky header の挙動未検証）              | 未確認      | **高** | 低（実機で開く 1 回）                |
| B2 | 重複整理    | 既存「Vault の見方ガイド」との内容重複あり（**統合方針 A/B/C のいずれか決定が必要**）                          | 確認済（未整備） | **高** | 中（重複箇所洗い出し + 統合方針決定）        |
| B3 | 公開先     | デプロイ先未決定（Vercel / Netlify / GitHub Pages / Cloudflare Pages のいずれか）            | 未決定      | 中     | 低（静的構成のため数分で完了）             |
| B4 | コンテンツ   | 検索パターン 20 件のみ。**実運用で増やす運用が必要**                                                | 確認済（要整備） | 中     | 中（毎週 1〜2 件追加 / 運用ついで）       |
| B5 | UX      | iPhone Tips / GitHub Tips セクションが薄い（実機操作スクショなし）                                | 確認済（要拡張） | 中     | 中（スクショ取得 + キャプション追加）        |
| B6 | 広告      | AdSense 申請未着手（公開 + コンテンツ蓄積後でないと申請通らない）                                        | 未着手      | 低     | 中（申請まで時間がかかる）               |
| B7 | テンプレ販売  | 「Vault 検索チートシート Markdown 拡張版」テンプレが未作成                                         | 未着手      | 低     | 中（既存資産整理 + 拡張 3〜5 時間）       |
| B8 | note 化  | 「個人開発 Vault 運用ガイド」note の素材なし（既存資産流用可だが整理必要）                                   | 未着手      | 低     | 中（B2 統合後の整理 4〜6 時間）         |
| B9 | 規約      | 既存「Vault の見方ガイド」の公開可否（個人 Vault 運用ノウハウが含まれる）                                   | 未確認      | 中     | 低（公開可能範囲を明文化のみ）             |

> 種別の網羅: 技術(B1) / 重複整理(B2) / 公開(B3) / コンテンツ(B4,B5) / 広告 テンプレ note(B6,B7,B8) / 規約(B9)。candidate-005/006 と同レベルで網羅。

## 最初の解除対象（推奨順）

1. **B1 iPhone Safari 実機確認**（最優先・低コスト）— ユーザーが iPhone で検索ボックス・テーブル・sticky header のタップ可否を確認
2. **B2 統合方針決定**（高優先）— 既存「Vault の見方ガイド」との重複を整理し、A/B/C のいずれかを決定（ChatGPT 方向性レビュー時に質問）
3. **B3 デプロイ先決定** — Vercel 推奨（candidate-005/006 と同じ理由）
4. **B9 規約整理** — 公開可能範囲を明文化（個人 Vault 運用ノウハウの公開範囲確定）
5. B5 UX 拡充（iPhone / GitHub Tips セクションのスクショ追加）
6. B4 検索パターン蓄積運用ルール
7. B7 テンプレ拡張版作成
8. B8 note 化
9. B6 AdSense 申請

> B1→B2→B3→B9 が解ければ「公開できるか + 既存資産との関係」が確定する。B4〜B8 は磨き工程。

## candidate-005 / 006 との並走時の優先度

- candidate-007 は **`low` impact + 既存資産との重複整理**があるため candidate-005/006 公開後に着手
- 別市場（Vault / Obsidian ユーザー層 vs AI 開発者層）のため**相互送客は限定的**
- → candidate-005 → 006 → 007 の順で承認判断推奨（並走の選択は人間判断）

## 統合方針 A/B/C（ChatGPT 方向性レビュー時の主要論点）

| 案 | 説明 | メリット | デメリット |
|---|---|---|---|
| A | 既存ガイドを残し、本候補は HTML Web 版として併存 | 既存導線維持 | 重複が残り保守コスト 2 重 |
| B | 既存ガイドを Markdown 索引化し、本候補が実体ページ | 役割明確化 | 既存ガイド利用者の習慣変更が必要 |
| C | 既存ガイドへ統合し、本候補は HTML サンプルのみ残す | 重複ゼロ | HTML Web 版の独自価値が薄れる |

> → ChatGPT 方向性レビュー時に質問。デフォルト候補は B（役割明確化 + 既存ガイド利用者の影響を最小化）。

## 未対応点 / 仮説

- B1/B3 は**未確認**。実機確認とデプロイは ChatGPT 方向性承認後 + 人間判断後に実施
- B2 の統合方針は ChatGPT 方向性レビュー時の論点。デフォルト案 B（既存ガイド索引化）を推奨
- 「対応コスト 中」は実装規模を見ての推定（実数値未測定）
- candidate-007 は ChatGPT 方向性レビュー前段階。本ブロッカー解消の着手は **chatgpt_pending → approved 後**（AI が先行着手しない）

## 次の一手

1. ChatGPT が candidate-007 を方向性レビュー（[[../../20_reviews/candidate-007_ChatGPT承認パック]] を読む）
2. ユーザーが iPhone Safari で `90_prototypes/vault-search-cheatsheet/index.html` を実機確認（B1 解除）
3. ChatGPT 方向性承認後、人間が chatgpt_pending → approved 判断 + 統合方針 A/B/C 決定
4. approved 後、B2（統合方針実施）を最初の progress 投入 ToDo にする → [[candidate-007_7日実行プラン]] Day1 と一致

## 関連

- [[candidate-007]]（candidate 本体）
- [[candidate-007_7日実行プラン]]
- [[candidate-007_progress投入設計]]
- [[../../20_reviews/candidate-007_ChatGPT承認パック]]
- [[../../00_inbox/Vaultの見方_どこを見れば何がわかるか]]（既存資産・重複整理対象）
- [[candidate-005_公開ブロッカー]] / [[candidate-006_公開ブロッカー]]（並走候補・形式比較）
- [[../収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#61（前提 #55 / #58 / #63 / #80）
