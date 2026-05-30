---
title: 案件別ToDo一覧（実行 ToDo / あなた確認待ち / 承認待ち の正本）
type: vault-todo-by-project
issue: kaeru07/vault#80
relatedIssues: [kaeru07/vault#74, kaeru07/vault#76, kaeru07/vault#77, kaeru07/vault#78, kaeru07/vault#79, kaeru07/vault#70]
created: 2026-05-24
updated: 2026-05-30 (vloop #91 反映)
status: active（vloop ごとに更新 / 本ページが正本）
tags: [入口, ToDo, 案件別, 正本]
---

# 案件別ToDo一覧（正本）

> **このページが ToDo の正本です。**
> Issue は内部作業用 / 本ページがユーザー向け正本（[[Vault蓄積運用ルール]] / [[案件別ToDo運用ルール]]）

> [!important] vloop 開始時の読み順
> 1. [[../00_START_HERE]]
> 2. **本ページ（案件別ToDo一覧）** ← ここから ToDo を選ぶ
> 3. [[ChatGPT承認待ち]]（承認判断時のみ）

> [!note] 構造（Issue #76 / #80）
> 4 セクション固定:
> - **A. 実行ToDo** — Claude / vloop が次に手を動かす対象（承認不要）
> - **B. あなた確認待ち（user_check）** — Claude 側完了・あなたの確認で前進
> - **C. 承認待ち（approval）** — ChatGPT 方向性レビュー + あなた approve / hold / reject 判断
> - **D. 完了移動候補** — 達成済で次回 vloop で [[完了ToDoログ]] へ移す候補
>
> **承認が必要なものだけチェックボックス** `[ ]` を使う / 実行 ToDo はチェックボックスなし

> [!note] 用語注（Issue #69）
> 有力候補 = candidate / 判断するための資料一式 = 承認パック / 公開前に解決が必要な問題 = ブロッカー / 承認後にToDoへ追加する流れ = progress 投入 / vloop = Claude がまとめて作業を進める仕組み

---

# A. 実行 ToDo（Claude / vloop が次に手を動かす対象 / 承認不要）

> 優先順位順。次の vloop はここから上から処理する。
> **重複統合済**（同目的の旧 ToDo は merged 扱い・本ページの 1 行に集約）

## 1. 🀄 麻雀アプリ（candidate-001）

| 優先  | 案件  | ToDo                                                         | 状態                                     | 次の 1 アクション |
| --- | --- | ------------------------------------------------------------ | -------------------------------------- | ---------- |
| —   | 麻雀  | candidate-001 本体への用語注記追加（#69 残）                              | **done**（vloop9 / 2026-05-25）          | D セクションへ移動 |
| —   | 麻雀  | candidate-001 補助 md への用語注記（公開ブロッカー / 7 日プラン / progress 投入設計） | **done**（vloop9 / 2026-05-25・3 ファイル完了） | D セクションへ移動 |

## 2. 🧪 トークン速度ツール（candidate-005）

| 優先 | 案件 | ToDo | 状態 | 次の 1 アクション |
|---|---|---|---|---|
| — | トークン速度 | candidate-005 本体への用語注記（#69 残）| **done**（vloop9 / 2026-05-25）| D セクションへ移動 |
| — | トークン速度 | candidate-005 補助 md への用語注記（公開ブロッカー / 7 日プラン / progress 投入設計）| **done**（vloop9 / 2026-05-25・3 ファイル完了）| D セクションへ移動 |

## 3. 🔁 複数案試作（candidate-006 / 007）

| 優先 | 案件 | ToDo | 状態 | 次の 1 アクション |
|---|---|---|---|---|
| **A-3-1** | 候補-006/007 | **candidate-006 / 007 の判断するための資料一式（補助 4 ファイル × 2 = 8 ファイル）作成** | **done**（vloop9 / 2026-05-25・8 ファイル完了）| ChatGPT 方向性レビュー送付 |
| — | N-03 | LLM Chooser 判定基準客観化検討 | **done**（vloop10 / 2026-05-25・3 層構造方針 1 ページ完成）| ChatGPT 方向性レビュー時に質問 §5 を確認 |
| — | N-04 | Vault Search Cheatsheet と既存「Vault の見方ガイド」統合方針決定 | **artifact_exists**（vloop10 / 2026-05-25・A/B/C 比較整理 + Claude 推奨案 B 完成・方針決定は ChatGPT 待ち）| ChatGPT 方向性レビュー時に統合方針 A/B/C 決定 |

## 4. 🗂 Vault 運用

| 優先 | 案件 | ToDo | 状態 | 次の 1 アクション |
|---|---|---|---|---|
| **A-4-1** | Vault | 旧運用フォルダ個別ファイルへの注記追加（04_reviews/ 3 件 + 07_tasks/inbox/ 1 件 + chatgpt/ README）| **done**（vloop9 / 2026-05-25・review-template.md は既に旧版注記済でスキップ）| D セクションへ移動 |
| — | Vault | 全 .md ファイル横断「存在しないパス案内」grep 検査 | **done**（vloop11 / 2026-05-25・検査結果 185 件 / カテゴリ A-F 分類 / 推奨案 X）| ChatGPT 方向性レビュー後にカテゴリ F 修正 |
| — | Vault | `Vaultの見方_どこを見れば何がわかるか` の分割 / 簡略化（#59 本文指摘）| **done**（vloop11 / 2026-05-25・1 分索引 + 最新候補状況 + 分割方針追加）| 分割実施は ChatGPT / ユーザー判断 |
| — | Vault | chatgpt/ フォルダの運用ルール確定 | **done**（vloop10 / 2026-05-25・README v1 確定）| D セクションへ移動 |
| — | Vault | #68 Mermaid テンプレを candidate 本体へ反映 | **done**（vloop10 / 2026-05-25・candidate-001/005/006/007 本体に 4 件反映）| D セクションへ移動 |

## 5. 💡 収益化案

| 優先 | 案件 | ToDo | 状態 | 次の 1 アクション |
|---|---|---|---|---|
| — | 収益化 | Hermes Agent 最小運用フロー実行（candidate-001 approved 後）| blocked（candidate-001 承認待ち）| 承認後に着手 |

## 6. 🏭 AI工場オペレーションセンター（Issue #91）

| 優先 | 案件 | ToDo | 状態 | 次の 1 アクション |
|---|---|---|---|---|
| — | AI工場 | #90 前段完了内容と #91 本文を確認し、承認不要範囲 / 承認待ち範囲を Vault 正本へ反映 | **done**（vloop 2026-05-30 / [[2026-05-30_issue-91-ops-center-next-phase]]） | D セクションへ移動 |
| **A-6-1** | AI工場 | Decision Log 読み戻し導線の実装設計を、pm2/cron 実操作なしで具体化 | open（承認不要範囲 / 実装は ny01 側） | 次回 vloop で ny01 側の安全なローカル実装を検討 |

---

# B. あなた確認待ち（user_check / Claude 完了済 / あなたの確認で前進）

| 案件 | 内容 | 確認場所 | 確認すること |
|---|---|---|---|
| 麻雀 | candidate-001 試作 / MVP | apps/mahjong（リポジトリ）| build / Vercel デプロイ状態（B1 / B2）|
| トークン速度 | candidate-005 試作 | [[../90_prototypes/token-speed-tool/README]] | iPhone Safari でサンプル 5 件動作確認 |
| 複数案試作 | N-03 試作 | [[../90_prototypes/llm-chooser/README]] | iPhone Safari でクイズ動作確認 |
| 複数案試作 | N-04 試作 | [[../90_prototypes/vault-search-cheatsheet/README]] | iPhone Safari で検索ボックス動作確認 |
| Vault | 00_START_HERE 入口 | [[../00_START_HERE]] | iPhone Obsidian で全リンクが開けるか |
| Vault | Vault 全体棚卸し（#59）| [[Vault全体棚卸し]] | 旧運用 / 新運用 / 暫定 / 削除候補分類の妥当性 |
| Vault | 「無題のフォルダ」削除 | iPhone Obsidian | フォルダ削除（Claude は実施しない）|
| 収益化案 | cron 投入判断 | [[../05_monetization/cron移行判定基準]] | research-run / idea-run 自動化開始可否 |

---

# C. 承認待ち（ChatGPT 方向性レビュー + あなた approve / hold / reject 判断）

> 承認が必要なものだけチェックボックス `[ ]` を使う。
> 完了したら `[x]` にして D セクションへ移動。

## 候補-001 / 005 / 006 / 007 の方向性承認

- [ ] **candidate-001 方向性レビュー**（[[candidate-001_ChatGPT承認パック]] §1-§14）→ approve / hold / reject
- [ ] **candidate-005 方向性レビュー**（[[candidate-005_ChatGPT承認パック]] §1-§14）→ approve / hold / reject
- [ ] **candidate-006 方向性レビュー**（[[candidate-006_ChatGPT承認パック]] §1-§14 / vloop9 完成）→ approve / hold / reject
- [ ] **candidate-007 方向性レビュー**（[[candidate-007_ChatGPT承認パック]] §1-§15 / 既存資産統合方針 A/B/C 含む / vloop9 完成）→ approve / hold / reject

## 運用ルール / 検討材料

- [ ] **Hermes Agent 公式仕様確認**（[[../06_research/hermes-agent-codex-組み込み検討]] §9）→ 採用判断 OK / NG
- [ ] **候補-005 pending_approval 昇格判断**（resources 4 ファイル完備）→ 昇格する / hold
- [ ] **candidate-005 ChatGPT 承認待ち.md への追加判断**（pending_approval 昇格後）→ Claude が追加 / 追加しない
- [ ] **#91 自動起動トリガ承認**（VPS 起動時 / 毎朝 11:00 / Claude 上限回復後）→ 実装する / hold
- [ ] **#91 pm2 / cron / systemd 実操作承認** → 実操作する / 設計のみ継続
- [ ] **#91 外部公開・認証情報利用を伴う連携承認** → 実装する / hold

---

# D. 完了移動候補（次回 vloop で [[完了ToDoログ]] へ移す候補）

> 状態が done になり、user_check も終わったものを移動。本セクションは vloop ごとに整理する。

## vloop11 完了分（次回 vloop12 で [[完了ToDoログ]] へ移動）

- **Vaultの見方ガイド簡略化**（1 分索引 + 最新候補状況 + 分割方針追加 / 破壊的削除なし）
- **全 .md 横断「存在しないパス案内」grep 検査**（185 件検出 / カテゴリ A-F 分類 / 06_research/2026-05-25_broken-links-検査結果.md）

## 2026-05-30 vloop 完了分（次回 vloop で [[完了ToDoログ]] へ移動）

- **Issue #91 実行境界の Vault 正本反映**（#90 前段完了確認 + #91 本文確認 + 承認不要 / 承認待ち境界を [[2026-05-30_issue-91-ops-center-next-phase]] に固定）

## vloop10 完了分（次回 vloop11 で [[完了ToDoログ]] へ移動 → vloop11 で実施済）

- **#68 Mermaid テンプレを candidate-001/005/006/007 本体へ反映**（4 ファイル状態色分け Mermaid 追加）
- **N-03 LLM Chooser 判定基準客観化方針**（3 層構造 1 ページ完成 / 06_research/2026-05-25_llm-chooser-判定基準客観化方針.md）
- **N-04 Vault Search Cheatsheet 統合方針 A/B/C 比較整理**（Claude 推奨 B / 06_research/2026-05-25_vault-search-cheatsheet-統合方針比較.md）
- **chatgpt/ フォルダ運用ルール README v1 確定**（暫定 → 補助フォルダ役割確定）

## vloop9 完了分（次回 vloop10 で [[完了ToDoログ]] へ移動 → vloop10 で実施済）

- **A-4-1 旧運用フォルダ個別ファイル注記追加**（04_reviews/ 3 件 + 07_tasks/inbox/ 1 件 + chatgpt/ README）→ Issue #59 残作業を解消
- **A-1 candidate-001 本体・補助 3 ファイル用語注記追加**（#69 残）
- **A-2 candidate-005 本体・補助 3 ファイル用語注記追加**（#69 残）
- **A-3-1 candidate-006 / 007 補助 4 ファイル × 2 = 8 ファイル作成**（公開ブロッカー / 7 日プラン / progress 投入設計 / ChatGPT 承認パック）

## 既存（前回 vloop までの完了分）

- Issue #59 Vault 全体棚卸し（user_check 残・vloop8 完了）→ Vault 全体棚卸し.md user_check 解除後に移動
- Issue #67 Hermes Agent × Codex 検討材料整備（vloop7 完了 / 採用判断は別 ToDo）→ ChatGPT 確認後に移動
- Issue #61 試作ループ検証（vloop1-6 完了）→ ChatGPT 確認後に移動

---

## 既存運用との関係

- 本ページが ToDo の**正本**（[[案件別ToDo運用ルール]]）
- [[次に実体化するToDo]] は**参照のみ**（vloop8 以前の互換 / 非推奨）
- [[完了ToDoログ]] が完了履歴の正本
- [[ChatGPT承認待ち]] は C 承認待ちセクションの詳細版（候補-001 のみ）

---

## 関連

- [[案件別ToDo運用ルール]]（本ページの正本ルール）
- [[Vault蓄積運用ルール]]（Issue ではなく Vault が正本）
- [[完了ToDoログ]]（完了履歴）
- [[次に実体化するToDo]]（参照のみ / 非推奨）
- [[../00_START_HERE]]
- [[ChatGPT承認待ち]]
- [[Vault全体棚卸し]]
- Issue: kaeru07/vault#80 / #74 / #76 / #77 / #78 / #79 / #70
