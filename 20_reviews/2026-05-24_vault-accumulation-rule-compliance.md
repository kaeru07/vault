---
date: 2026-05-24
task: Vault 蓄積運用ルール準拠 Epic（#70 次に実体化するToDo + #63 案の情報源と採用理由 + #68 現在地図テンプレ + #69 用語日本語化 一体実装）
runId: 20260524-vloop4-vault-accumulation-rule
targetApp: company-meta / obsidian-vault
monetizationImpact: medium
theme: [obsidian, workflow, app-strategy, monetization]
relatedRunIds: [20260524-200532, 20260524-194403, 20260524-185950]
commitHashes: []
---

# Vault 蓄積運用ルール準拠 Epic 一体実装

## 1. 作業目的

- ユーザーが GitHub から git pull で持ってきた新運用ルール「Vault 蓄積運用ルール」（[[Vault蓄積運用ルール]]）に従い、Issue ではなく Vault が正本となる構造へ移行する
- 新 Issue #68 / #69 / #70（および補強 #63）を 1 サイクルで一体実装する
- 「やりっぱなし」を防ぐ次サイクル優先キュー（#70）を作る
- iPhone Obsidian で読みやすいユーザー向け日本語に主要ページを置換する（#69 部分実施）
- Mermaid 図サマリーの標準テンプレを作って Epic ステータスに反映する（#68）
- 案の情報源と採用理由ページを idea_trace の日本語エイリアスとして作る（#63 補強）

## 2. 実施内容

- `20_reviews/次に実体化するToDo.md` 新規（Issue #70）/ 直近実体化完了 + 次サイクル優先キュー + ユーザー確認待ち + やりっぱなし防止ルール
- `05_monetization/案の情報源と採用理由.md` 新規（Issue #63 補強 / idea_trace の日本語入口）/ 早見表 + API なし表 + 各案カードへの中継リンク
- `90_templates/現在地図テンプレ.md` 新規（Issue #68）/ 標準色 + 状態ラベル + 3 種テンプレ（フェーズ進行 / 分岐 / 1 枚図サマリー） + 使い方
- `05_monetization/epics.md` Mermaid 図に classDef（状態色分け）追加 + Epic 試作ループ + Vault 蓄積運用ルール準拠 Epic 追加（Issue #68 Phase 2 反映実証）
- `00_START_HERE.md` 全面更新（Issue #69）: 用語の言い換え注記追加 + 入口 4 → 5 件（次に実体化するToDo を最優先に）+ candidate-005 セクション + 試作モック 3 件 + 日本語化 + チェックリスト 8 → 12 件
- `05_monetization/scenarios/README.md` 用語の言い換え注記 + candidate-005 並走候補セクション追加（Issue #69）
- レビューファイル本ファイル新規 + `_review_queue.md` 先頭追加

## 3. 変更ファイル

| ファイル | 変更内容 | Issue |
|---|---|---|
| `20_reviews/次に実体化するToDo.md` | 新規 | #70 |
| `05_monetization/案の情報源と採用理由.md` | 新規（idea_trace 日本語エイリアス）| #63 / #69 |
| `90_templates/現在地図テンプレ.md` | 新規（Mermaid 標準テンプレ）| #68 |
| `05_monetization/epics.md` | Mermaid classDef + Epic 追加 | #68 Phase 2 |
| `00_START_HERE.md` | 全面更新（日本語化 + 5 入口）| #69 / #70 |
| `05_monetization/scenarios/README.md` | 用語注記 + candidate-005 並走候補 | #69 |
| `20_reviews/2026-05-24_vault-accumulation-rule-compliance.md` | 本ファイル新規 | — |
| `20_reviews/_review_queue.md` | 先頭追加 | — |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| Vault 蓄積運用ルール準拠 | ✅ 「実体ページ未作成 ToDo」を `次に実体化するToDo.md` で見える化 |
| 用語日本語化（#69）| ⏳ 主要 3 ページ（00_START_HERE / scenarios/README / 案の情報源と採用理由）に部分適用 / 残りは次サイクル |
| Mermaid 図テンプレ（#68）| ✅ テンプレ作成 + epics.md に反映 / 他ページは次サイクル |
| 次サイクル優先キュー（#70）| ✅ 作成・主要 5 件 + ユーザー確認待ち + やりっぱなし防止ルール |
| 案の情報源と採用理由（#63）| ✅ idea_trace の日本語エイリアスとして新規作成 |
| 機密スキャン | ✅ 実値なし |
| build / typecheck / lint | n/a（Markdown のみ）|
| ob sync | 次フェーズで実行（最終応答前）|
| GitHub push | 次フェーズで実行 |

## 5. 未対応

- #69 用語日本語化の**残りページ**（idea_trace.md / 試作ループ検証.md / 各 candidate-XXX.md 等）→ 次サイクル
- #68 Mermaid テンプレの**残りページへの反映**（Issue完了判定ルール / idea_trace 等）→ 次サイクル
- iPhone 実機表示確認（00_START_HERE 新項目含む）→ ユーザー操作
- #67 Hermes Agent × Codex 検討着手 → 次サイクル
- #59 Vault 全体棚卸し Epic → 次サイクル候補（大規模）
- 既存 done だが open のまま Issue（#50/#51 等）のバッチ整理 → 人間判断

## 6. 危険ポイント

- ❌ なし（破壊的変更なし / 既存ファイル削除なし / DB 操作なし / 認証情報なし / 外部公開判断なし）
- 既存 Mermaid 図の書き換え（epics.md）は内容を保持しつつ classDef 追加のみ（情報の損失なし）
- 00_START_HERE.md 全面更新は**新項目追加 + 用語補足**のみ。既存リンクは全て保持

## 7. 次にやるべきこと

1. ChatGPT が 4 件レビュー（_review_queue.md 先頭の 2026-05-24_vault-accumulation-rule-compliance）
2. ユーザーが iPhone Obsidian で 00_START_HERE → 次に実体化するToDo → 案の情報源と採用理由 への遷移確認
3. 次サイクルで #69 残ページ日本語化（idea_trace.md / 試作ループ検証.md 等）
4. 次サイクルで #68 Mermaid テンプレを Issue完了判定ルール / idea_trace に反映
5. 次サイクルで #67 Hermes Agent × Codex 検討着手
6. ChatGPT が candidate-001 / candidate-005 の方向性レビュー（既存待ち）

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です（4 サイクル目・本日 4 回目）。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: vloop 2026-05-24 Vault 蓄積運用ルール準拠 Epic（#70 + #63 + #68 + #69 一体実装）

## できるようになったこと
- ユーザーが pull で持ってきた新ルール「Vault 蓄積運用ルール」に準拠するための主要ファイル整備
- 次に実体化するToDo.md: planned_only / artifact_exists / user_check / done の状態分類 + 次サイクル優先キュー
- 案の情報源と採用理由.md: idea_trace の日本語エイリアス + 早見表 + API なし表
- 現在地図テンプレ.md: Mermaid 標準テンプレ + 状態色分け + 3 種テンプレ
- epics.md に classDef 追加で Mermaid 図テンプレ反映実証
- 00_START_HERE / scenarios/README に日本語注記 + 入口 5 件化

## 確認したい観点
- 「次に実体化するToDo」の分類（planned_only / in_progress / artifact_exists / user_check / done）は妥当か
- 「案の情報源と採用理由」を idea_trace のエイリアスとして用意する判断は妥当か（idea_trace を改名する方が良いか）
- 現在地図テンプレの色分け（緑 done / 黄 user_check / 橙 open / 青 merged / 赤 blocked / 紫 next / 水 working）は読みやすいか
- 00_START_HERE の入口 5 件（次に実体化 / ChatGPT 承認待ち / candidate-001 / 有力候補一覧 / Epic 進捗）の優先順は妥当か
- 用語日本語化の進め方（本サイクル主要 3 ページ + 次サイクル残りページ）は妥当か
- 「Vault 蓄積運用ルール」準拠が今後の全 vloop の前提になる理解で良いか

参考リンク:
- 20_reviews/次に実体化するToDo.md
- 05_monetization/案の情報源と採用理由.md
- 90_templates/現在地図テンプレ.md
- 05_monetization/epics.md（Mermaid 図 classDef 反映）
- 00_START_HERE.md（全面更新）
- 05_monetization/scenarios/README.md（用語注記 + candidate-005 並走候補）
```

## 関連

- [[Vault蓄積運用ルール]]（本サイクルの根拠ルール）
- 前サイクル: [[2026-05-24_candidate-005-approval-pack]]（vloop3）
- 前々サイクル: [[2026-05-24_candidate-005-and-n03-n04-prototypes]]（vloop2）
- 前々々サイクル: [[2026-05-24_idea-trace-and-token-speed-prototype]]（vloop1）
- [[次に実体化するToDo]]
- [[../05_monetization/案の情報源と採用理由]]
- [[../90_templates/現在地図テンプレ]]
- Issue: kaeru07/vault#70 / #63 / #68 / #69
