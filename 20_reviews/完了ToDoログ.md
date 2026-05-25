---
title: 完了 ToDo ログ（案件別ToDo一覧から移動した完了項目）
type: vault-completed-log
issue: kaeru07/vault#74
relatedIssues: [kaeru07/vault#80, kaeru07/vault#70]
created: 2026-05-25
updated: 2026-05-25 (vloop11)
status: active（vloop ごとに追加）
tags: [完了, ログ, 履歴]
---

# 完了 ToDo ログ

> Issue #74 / #80。[[案件別ToDo一覧]] から完了 ToDo を移動して履歴化するページ。
> 案件別ToDo一覧 は**実行ToDo / あなた確認待ち / 承認待ち** のみを保持し、完了は本ページへ。

> [!note] 移動ルール
> - 状態が **done** または **artifact_exists** で次工程の依存がなくなったら本ページへ移動
> - **user_check** は本ページには移さない（あなたが確認するまで案件別ToDo一覧に残す）
> - 各エントリは「達成日 / 案件 / 達成内容 / 成果物リンク / runId」を最低限記録

---

## 2026-05-24（vloop1-7 達成分）

### Vault 運用基盤系

| 達成日        | 案件       | 達成内容                                               | 成果物                                                                                                           | runId             |
| ---------- | -------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | ----------------- |
| 2026-05-24 | Vault 運用 | やりっぱなし防止キュー新規（#70）                                 | [[次に実体化するToDo]]                                                                                               | 20260524-vloop4   |
| 2026-05-24 | Vault 運用 | 現在地図テンプレ新規（#68 Phase 1）                            | [[../90_templates/現在地図テンプレ]]                                                                                  | 20260524-vloop4   |
| 2026-05-24 | Vault 運用 | 案件別ToDo一覧 新規（#70 / 新運用ルール準拠）                       | [[案件別ToDo一覧]]                                                                                                 | 20260524-vloop6   |
| 2026-05-24 | Vault 運用 | 00_START_HERE 入口 6 件化（vloop6）                      | [[../00_START_HERE]]                                                                                          | 20260524-vloop6   |
| 2026-05-24 | Vault 運用 | Mermaid 状態色分けテンプレを主要 4 ページに反映（#68 Phase 2 partial） | epics / Issue完了判定ルール / idea_trace / 試作ループ検証                                                                   | 20260524-vloop4-5 |
| 2026-05-24 | Vault 運用 | 主要 8 ページに用語日本語化注記追加（#69 partial）                   | 00_START_HERE / scenarios/README / 案の情報源と採用理由 / idea_trace / Issue完了判定ルール / 試作ループ検証 / 案件別ToDo一覧 / 次に実体化するToDo | 20260524-vloop4-5 |

### 試作ループ系

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-24 | 複数案試作 | idea_trace 全案ハブ新規（#63 / 10 案カード）| [[../05_monetization/idea_trace]] | 20260524-vloop1 |
| 2026-05-24 | 複数案試作 | 案の情報源と採用理由 日本語エイリアス新規（#63 補強）| [[../05_monetization/案の情報源と採用理由]] | 20260524-vloop4 |
| 2026-05-24 | トークン速度 | token-speed-tool 仕様 + trace 1 ファイル集約（#60 + #62）| [[../05_monetization/token-speed-tool]] | 20260524-vloop1 |
| 2026-05-24 | トークン速度 | token-speed-tool 静的 HTML MVP モック（外部 CDN ゼロ・サンプル 5 件）| [[../90_prototypes/token-speed-tool/README]] | 20260524-vloop1 |
| 2026-05-24 | 複数案試作 | 試作ループ検証 Phase1-6（14 案追加 + 3 候補 + 試作 1 件着地 / vloop2 で 2 案目達成）| [[../05_monetization/試作ループ検証]] | 20260524-vloop1-2 |
| 2026-05-24 | 複数案試作 | N-03 LLM Chooser 静的 HTML 試作 | [[../90_prototypes/llm-chooser/README]] | 20260524-vloop2 |
| 2026-05-24 | 複数案試作 | N-04 Vault Search Cheatsheet 静的 HTML 試作 | [[../90_prototypes/vault-search-cheatsheet/README]] | 20260524-vloop2 |

### candidate 正規化系

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-24 | トークン速度 | candidate-005 として scenarios 正規化（28/40）| [[../05_monetization/scenarios/candidate-005]] | 20260524-vloop2 |
| 2026-05-24 | トークン速度 | candidate-005 判断するための資料一式 4 ファイル完備 | [[candidate-005_ChatGPT承認パック]] + [[../05_monetization/scenarios/candidate-005_公開ブロッカー]] + [[../05_monetization/scenarios/candidate-005_7日実行プラン]] + [[../05_monetization/scenarios/candidate-005_progress投入設計]] | 20260524-vloop3 |
| 2026-05-24 | 複数案試作 | N-03 を candidate-006 として scenarios 正規化（26/40 コンパクト版）| [[../05_monetization/scenarios/candidate-006]] | 20260524-vloop6 |
| 2026-05-24 | 複数案試作 | N-04 を candidate-007 として scenarios 正規化（28/40 / 既存資産との統合方針 A/B/C）| [[../05_monetization/scenarios/candidate-007]] | 20260524-vloop6 |
| 2026-05-24 | 収益化案 | scenarios/README 候補全件 5 → 7 件体制 | [[../05_monetization/scenarios/README]] | 20260524-vloop6 |

### 検討・調査系

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-24 | 収益化案 | Hermes Agent × Codex 組み込み検討材料整備（#67 / 完了条件 6/6）| [[../06_research/hermes-agent-codex-組み込み検討]] | 20260524-vloop7 |

## 2026-05-25（vloop8 達成分）

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-25 | Vault 運用 | Vault 全体棚卸し（#59 / Phase 1-5 / 完了条件 8/8 / 破壊的削除ゼロ）| [[Vault全体棚卸し]] | 20260525-vloop8 |

## 2026-05-25（vloop9 達成分 / Issue #80 実行）

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-25 | Vault 運用 | 完了ToDoログ.md 新規（#74）| 本ページ | 20260525-vloop9 |
| 2026-05-25 | Vault 運用 | 案件別ToDo一覧 全面リファクタ（#76 / 4 セクション構造）| [[案件別ToDo一覧]] | 20260525-vloop9 |
| 2026-05-25 | Vault 運用 | START_HERE 導線整理（#78 / 1-2 タップ化）| [[../00_START_HERE]] | 20260525-vloop9 |
| 2026-05-25 | Vault 運用 | 次に実体化するToDo.md を「参照のみ」化（#76）| [[次に実体化するToDo]] | 20260525-vloop9 |

## 2026-05-25（vloop9 続編 達成分 / A セクション実体化）

### Vault 運用 / 用語日本語化系

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-25 | Vault 運用 | 旧運用フォルダ個別ファイル注記追加（A-4-1 / #59 残作業解消 / review-template.md は既に旧版注記済でスキップ）| 04_reviews/README / 04_reviews/Claude作業レビュー運用 / 07_tasks/inbox/2026-05-17-progress-radar-gantt / chatgpt/README | 20260525-vloop9 |
| 2026-05-25 | 麻雀 | candidate-001 本体 + 補助 3 ファイル用語注記追加（A-1 / #69 残）| [[../05_monetization/scenarios/candidate-001]] + 公開ブロッカー / 7日実行プラン / progress投入設計 | 20260525-vloop9 |
| 2026-05-25 | トークン速度 | candidate-005 本体 + 補助 3 ファイル用語注記追加（A-2 / #69 残）| [[../05_monetization/scenarios/candidate-005]] + 公開ブロッカー / 7日実行プラン / progress投入設計 | 20260525-vloop9 |

### 複数案試作（candidate-006 / 007）補助ファイル新規

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-25 | 複数案試作 | candidate-006（llm-chooser）補助 4 ファイル新規作成（A-3-1）| [[../05_monetization/scenarios/candidate-006_公開ブロッカー]] / [[../05_monetization/scenarios/candidate-006_7日実行プラン]] / [[../05_monetization/scenarios/candidate-006_progress投入設計]] / [[candidate-006_ChatGPT承認パック]] | 20260525-vloop9 |
| 2026-05-25 | 複数案試作 | candidate-007（vault-search-cheatsheet）補助 4 ファイル新規作成（A-3-1）| [[../05_monetization/scenarios/candidate-007_公開ブロッカー]] / [[../05_monetization/scenarios/candidate-007_7日実行プラン]] / [[../05_monetization/scenarios/candidate-007_progress投入設計]] / [[candidate-007_ChatGPT承認パック]] | 20260525-vloop9 |

---

## 2026-05-25（vloop10 達成分 / A セクション残実体化）

### Vault 運用 / 図表化系

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-25 | Vault 運用 | #68 Mermaid テンプレを candidate-001/005/006/007 本体へ反映（4 件 状態色分け Mermaid 追加）| [[../05_monetization/scenarios/candidate-001]] / [[../05_monetization/scenarios/candidate-005]] / [[../05_monetization/scenarios/candidate-006]] / [[../05_monetization/scenarios/candidate-007]] | 20260525-vloop10 |
| 2026-05-25 | Vault 運用 | chatgpt/ フォルダ運用ルール README v1 確定（暫定 → 補助フォルダ役割確定 / 案件別ToDo一覧 A-4 解消）| [[../chatgpt/README]] | 20260525-vloop10 |

### 候補-006 / 007 方針整理系

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-25 | 複数案試作 | N-03 LLM Chooser 判定基準客観化方針 1 ページ完成（3 層構造 / ChatGPT 方向性レビュー材料）| [[../06_research/2026-05-25_llm-chooser-判定基準客観化方針]] | 20260525-vloop10 |
| 2026-05-25 | 複数案試作 | N-04 Vault Search Cheatsheet 統合方針 A/B/C 比較整理（Claude 推奨案 B / ChatGPT 質問テンプレ §7）| [[../06_research/2026-05-25_vault-search-cheatsheet-統合方針比較]] | 20260525-vloop10 |

---

## 2026-05-25（vloop11 達成分 / Issue #59 残実体化）

### Vault 整備系

| 達成日 | 案件 | 達成内容 | 成果物 | runId |
|---|---|---|---|---|
| 2026-05-25 | Vault 運用 | Vaultの見方ガイド簡略化（1 分索引 + 最新候補状況 + 分割方針追加 / 破壊的削除なし / #59 本文指摘解消）| [[../00_inbox/Vaultの見方_どこを見れば何がわかるか]] | 20260525-vloop11 |
| 2026-05-25 | Vault 運用 | 全 .md 横断「存在しないパス案内」grep 検査（162 ファイル / 1,454 リンク中 185 件検出 / カテゴリ A-F 分類）| [[../06_research/2026-05-25_broken-links-検査結果]] | 20260525-vloop11 |

---

## ログ運用ルール

1. vloop が完了 ToDo を生成したら**本ページに追記**（同サイクル内で実施）
2. user_check / partial_done は本ページに移さない（案件別ToDo一覧に残す）
3. **削除はしない** — 履歴として保持
4. 月次でセクション分けする（最新月が上）

## 関連

- [[案件別ToDo一覧]]（実行 ToDo / あなた確認待ち / 承認待ち の正本）
- [[案件別ToDo運用ルール]]
- [[Vault蓄積運用ルール]]
- [[次に実体化するToDo]]（参照のみ / vloop8 以前の互換）
- Issue: kaeru07/vault#74 / #80 / #70
