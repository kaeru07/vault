---
title: 案件別ToDo一覧（案件別ToDo運用ルールの正本）
type: vault-todo-by-project
issue: kaeru07/vault#70
relatedIssues: [kaeru07/vault#69, kaeru07/vault#68]
created: 2026-05-24
updated: 2026-05-24
status: active（vloop ごとに更新）
tags: [入口, ToDo, 案件別]
---

# 案件別ToDo一覧

> [[案件別ToDo運用ルール]] に基づく案件別 ToDo の正本。
> vloop 開始時は `00_START_HERE → 本ページ → 次に実体化するToDo → ChatGPT 承認待ち → 関連案件ページ` の順で読む。

> [!important] このページの位置付け
> - **Issue は内部作業用**、本ページが**ユーザー向け正本**
> - vloop は本ページから読み始める（Issue 一覧からではない）
> - vloop 終了時は本ページへ進捗を必ず反映

> [!note] 用語注（Issue #69）
> 未対応 = open / 作業中 = in_progress / 成果物あり = artifact_exists / あなたの確認待ち = user_check / 完了 = done / 統合済み = merged / 停止中 = blocked / 有力候補 = candidate / 判断するための資料一式 = 承認パック / 大きな作業テーマ = Epic

---

## 1. 🗂 Vault 運用

主な保存先: `20_reviews/` 配下 / `00_START_HERE.md` / `90_templates/`

| ToDo | 状態 | 何をするか | 次に vloop がやること | ユーザー確認 |
|---|---|---|---|---|
| 本ページ（案件別ToDo一覧）整備 | **成果物あり** | 案件 6 件で ToDo を整理 | 各 vloop 終了時に更新 | iPhone Obsidian でリンクが開けるか |
| Vault 蓄積運用ルール準拠（#70/#68/#69/#63）| **成果物あり / 残あり** | やりっぱなし防止キュー + Mermaid テンプレ + 用語日本語化 | 残ページ用語日本語化（candidate 本体 5 件 + 補助 md）| 主要 6 ページの読みやすさ |
| 00_START_HERE 入口整備 | **成果物あり** | iPhone から 1 タップで主要ページへ | 案件別ToDo一覧へのリンク追加（本サイクル） | iPhone Safari でリンク確認 |
| 現在地図テンプレ反映 | **成果物あり / 残あり** | Mermaid テンプレ → 主要 4 ページ反映済 | candidate 本体への Mermaid 図追加 | 不要 |

関連: [[次に実体化するToDo]] / [[Vault蓄積運用ルール]] / [[../90_templates/現在地図テンプレ]]

---

## 2. 🀄 麻雀アプリ（candidate-001）

主な保存先: `02_apps/mahjong.md` / `05_monetization/scenarios/candidate-001*.md`

| ToDo | 状態 | 何をするか | 次に vloop がやること | ユーザー確認 |
|---|---|---|---|---|
| candidate-001 判断するための資料一式（承認パック）整備 | **成果物あり / user_check** | 5 ファイル完備（candidate-001 本体 + 公開ブロッカー + 7 日プラン + progress 投入設計 + ChatGPT 承認パック）| ChatGPT 方向性レビュー待ち | ChatGPT が approve / hold / reject 判断 |
| candidate-001 本体への用語注記追加 | **未対応** | 「承認パック → 判断するための資料一式」等の言い換え | 次サイクル | 不要 |
| approved 後の build 検証（B1）| **planned_only** | apps/mahjong で `npm run build` | 承認後 progress 投入時に着手 | 承認判断 + build 結果確認 |

関連: [[../02_apps/mahjong]] / [[../05_monetization/scenarios/candidate-001]] / [[../05_monetization/scenarios/candidate-001_ChatGPT承認パック]] / [[../05_monetization/scenarios/candidate-001_公開ブロッカー]] / [[../05_monetization/scenarios/candidate-001_7日実行プラン]] / [[../05_monetization/scenarios/candidate-001_progress投入設計]]

---

## 3. 🧪 トークン速度ツール（candidate-005）

主な保存先: `05_monetization/token-speed-tool.md` / `05_monetization/scenarios/candidate-005*.md` / `90_prototypes/token-speed-tool/`

| ToDo | 状態 | 何をするか | 次に vloop がやること | ユーザー確認 |
|---|---|---|---|---|
| candidate-005 試作モック | **成果物あり / user_check** | API なし MVP 静的 HTML + JSON サンプル 5 件 | ChatGPT 方向性レビュー待ち | iPhone Safari でブラウザ動作確認 |
| candidate-005 判断するための資料一式 | **成果物あり / user_check** | 4 ファイル完備（公開ブロッカー B1-B9 + 7 日プラン + progress 投入設計 + ChatGPT 承認パック）| ChatGPT 方向性レビュー待ち | ChatGPT が approve / hold / reject 判断 |
| candidate-005 本体への用語注記 | **未対応** | 言い換え + 内部名括弧補足 | 次サイクル | 不要 |
| 公開後の B4 実測データ蓄積 | **planned_only** | Claude Code / Codex / Gemini / ローカル LLM 実測 10-15 件追加 | 承認 + 公開後 | 不要（自分の利用ついで）|

関連: [[../05_monetization/token-speed-tool]] / [[../05_monetization/scenarios/candidate-005]] / [[../05_monetization/scenarios/candidate-005_ChatGPT承認パック]] / [[../90_prototypes/token-speed-tool/README]]

---

## 4. 🔁 複数案試作ループ（候補-006 / 007）

主な保存先: `05_monetization/試作ループ検証.md` / `05_monetization/idea_trace.md` / `90_prototypes/{llm-chooser,vault-search-cheatsheet}/`

| ToDo | 状態 | 何をするか | 次に vloop がやること | ユーザー確認 |
|---|---|---|---|---|
| 試作ループ実証（vloop1-2）| **完了** | 14 案追加 + 3 候補選定 + N-01 試作着地 + Phase6 で 2 案目試作 | n/a | n/a |
| N-03 LLM Chooser → candidate-006 起票 | **成果物あり**（本 vloop6 で起票）| candidate-004 形式で正規化（コンパクト版）| 判断するための資料一式は次サイクル候補 | ChatGPT 方向性レビュー後に判断 |
| N-04 Vault Search Cheatsheet → candidate-007 起票 | **成果物あり**（本 vloop6 で起票）| 同上 | 同上 | 既存 Vault 見方ガイドとの重複整理 |
| N-12 麻雀役一覧チートシート等 N-02/05/06/07/08/12/13/14 | **未対応 / 案のみ** | 静的 HTML 試作の追加検討 | 次サイクル以降 | 不要 |

関連: [[../05_monetization/試作ループ検証]] / [[../90_prototypes/llm-chooser/README]] / [[../90_prototypes/vault-search-cheatsheet/README]] / [[../05_monetization/idea_trace]] §8/§9

---

## 5. 💡 収益化案・案の追跡

主な保存先: `05_monetization/案の情報源と採用理由.md` / `05_monetization/idea_trace.md`

| ToDo | 状態 | 何をするか | 次に vloop がやること | ユーザー確認 |
|---|---|---|---|---|
| 全案 trace ハブ（10 案カード）| **成果物あり** | idea_trace.md + 案の情報源と採用理由.md（日本語エイリアス）| 候補-006/007 起票後に §8/§9 更新 | iPhone でリンク確認 |
| API なし早見表 | **成果物あり** | 9 案の API なし範囲一覧 | 候補-006/007 追加で 11 案へ | 不要 |
| Epic A 情報収集（cron 移行 3 日連続達成）| **user_check** | 人間が cron 投入判断 | n/a（人間判断）| cron 投入可否 |
| Epic B 案生成（40 案 + 上位 5 件）| **完了** | 収益化 6 軸スコアリング済 | 各源 n 増（30→100/日）は次サイクル候補 | 不要 |
| Hermes Agent × Codex 検討（#67）| **未対応 / 検討中** | 既存運用への組み込み検討 | 次サイクル: 議論型 Issue として ChatGPT と整理 | 範囲確認後の収益化インパクト判断 |
| Vault 全体棚卸し（#59）| **未対応** | 旧運用と新運用の統一 | Phase 分割計画（次サイクル）| Phase 計画確定 |

関連: [[../05_monetization/案の情報源と採用理由]] / [[../05_monetization/idea_trace]] / [[../05_monetization/epics]] / [[../05_monetization/cron移行判定基準]]

---

## 6. 🗳 レビュー・承認

主な保存先: `20_reviews/ChatGPT承認待ち.md` / `20_reviews/_review_queue.md` / 各 candidate 承認パック

| ToDo | 状態 | 何をするか | 次に vloop がやること | ユーザー確認 |
|---|---|---|---|---|
| ChatGPT 承認待ち.md に candidate-001 ブロック | **成果物あり / user_check** | 既存（vloop1 サイクル前から）| ChatGPT 方向性レビュー待ち | approve / hold / reject 判断 |
| ChatGPT 承認待ち.md に candidate-005 ブロック | **未対応**（意図的・pending_approval 昇格は人間判断後）| candidate-005 承認材料完備済 / 昇格判断後に追加 | 人間 pending_approval 昇格後に追加 | pending_approval 昇格可否 |
| _review_queue.md（レビューキュー）| **成果物あり** | vloop ごとに先頭追加 | 各 vloop 終了時に追加 | ChatGPT が先頭からレビュー |
| 候補-006/007 承認パック化 | **planned_only** | candidate-004 形式での補助 4 ファイル | 次サイクル | 不要 |

関連: [[ChatGPT承認待ち]] / [[_review_queue]] / [[candidate-001_ChatGPT承認パック]] / [[candidate-005_ChatGPT承認パック]]

---

## 🚨 ユーザー確認待ち（最優先で見るもの）

| 内容 | 確認場所 | 確認すること |
|---|---|---|
| candidate-001 / candidate-005 の判断するための資料一式 | [[ChatGPT承認待ち]] + 各承認パック | ChatGPT が approve / hold / reject 判断 |
| iPhone Obsidian 入口 | [[../00_START_HERE]] | リンクが iPhone Safari で開けるか |
| 試作モック 3 件（token-speed-tool / llm-chooser / vault-search-cheatsheet）| `90_prototypes/*/index.html` | ブラウザ動作確認 + iPhone 表示確認 |
| cron 投入判断 | [[../05_monetization/cron移行判定基準]] | research-run / idea-run の自動化 |

---

## 🆕 新しく発生した ToDo（本サイクル発生分）

- 候補-006 / 007 の判断するための資料一式（4 ファイル × 2 = 8 ファイル）→ planned_only / 次サイクル
- 残ページ用語日本語化（candidate 本体 5 件 + 補助 md）→ planned_only / 次サイクル
- 案件別ToDo一覧 自体の vloop ごと更新運用 → 継続運用

---

## 関連

- [[案件別ToDo運用ルール]]（本ページの根拠ルール）
- [[Vault蓄積運用ルール]]（Issue ではなく Vault が正本）
- [[次に実体化するToDo]]（やりっぱなし防止キュー）
- [[../00_START_HERE]]
- [[ChatGPT承認待ち]]
- [[../05_monetization/idea_trace]]
- [[../05_monetization/案の情報源と採用理由]]
- Issue: kaeru07/vault#70 / #69 / #68 / #63
