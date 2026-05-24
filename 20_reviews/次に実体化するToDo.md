---
title: 次に実体化するToDo（やりっぱなし防止キュー）
type: vault-priority-queue
issue: kaeru07/vault#70
relatedIssues: [kaeru07/vault#63, kaeru07/vault#68, kaeru07/vault#69]
created: 2026-05-24
updated: 2026-05-24
status: active（vloop ごとに更新）
tags: [優先キュー, planned_only, 次に実体化]
---

# 次に実体化するToDo（やりっぱなし防止キュー）

> Issue #70 に基づく次サイクル優先キュー。
> 「ToDo を作っただけ」「方針だけ決めた」状態のものを一覧化し、次に Claude/vloop が手を動かす対象を明示する。

> [!important] このページの読み方
> - **planned_only** = まだ実体ファイルがない / Issue だけ存在
> - **in_progress** = 作成中 / 一部完成
> - **artifact_exists** = 実体ファイルあり（path 明記）
> - **user_check** = あなた（ユーザー）の確認待ち
> - **done** = 完了
> - 「次にやる順」は Claude が機械的に決める。実体化したらこのページから消す / done 欄に移す

---

## ✅ 直近 vloop（vloop1-6）で実体化完了したもの

| Issue | 元の ToDo | 状態 | 実体ファイル / 場所 |
|---|---|---|---|
| #60 | トークン速度ツール API なし試作 | **artifact_exists + user_check** | [[../05_monetization/token-speed-tool]] + [[../90_prototypes/token-speed-tool/README]] + 判断するための資料一式 4 ファイル + [[candidate-005_ChatGPT承認パック]] |
| #61 | 複数案試作ループ | **artifact_exists + user_check**（vloop6 で candidate-006 / 007 起票）| [[../05_monetization/試作ループ検証]] + [[../90_prototypes/llm-chooser/README]] + [[../90_prototypes/vault-search-cheatsheet/README]] + [[../05_monetization/scenarios/candidate-006]] + [[../05_monetization/scenarios/candidate-007]] |
| #62 | トークン速度ツール trace | **artifact_exists** | [[../05_monetization/token-speed-tool]] Part1 + [[../05_monetization/idea_trace]] §2 |
| #63 | 全アプリ案情報源ページ | **artifact_exists** | [[../05_monetization/idea_trace]]（10 案カード）+ [[../05_monetization/案の情報源と採用理由]] 日本語エイリアス |
| #70 | やりっぱなし防止キュー + 案件別 ToDo 一覧 | **artifact_exists**（vloop6 で案件別ToDo一覧追加）| 本ページ + [[案件別ToDo一覧]] |
| #68 | 現在地図テンプレ | **artifact_exists**（Phase1 完了 + Phase2 を 4 ページ反映: epics / Issue完了判定ルール / idea_trace / 試作ループ検証）| [[../90_templates/現在地図テンプレ]] |
| #69 | 内部用語日本語化 | **partial_done**（主要 8 ページに用語注記追加: 00_START_HERE / scenarios/README / 案の情報源と採用理由 / idea_trace / Issue完了判定ルール / 試作ループ検証 / 案件別ToDo一覧 / 次に実体化するToDo）| 上記 8 ページ |
| **新規 vloop6** | **N-03 / N-04 candidate 化判断** | **artifact_exists**（vloop6 で candidate-006 / 007 起票）| [[../05_monetization/scenarios/candidate-006]] / [[../05_monetization/scenarios/candidate-007]] |

> [!note] **user_check = あなたの確認待ち**
> 上記 #60 / #61 は Claude 側の作業は完了しているが、ユーザーが iPhone 実機 / ブラウザで確認するまで「完全 done」にはしない。

---

## 🔥 次に Claude/vloop が手を動かすべき ToDo（優先順）

### 優先 1（vloop7 で完了）: ~~#67 Hermes Agent × Codex を運用に組み込み検討~~ → 検討材料完備

| 項目 | 内容 |
|---|---|
| 状態 | **artifact_exists（検討材料整備完了）**（vloop7）|
| 達成内容 | [[../06_research/hermes-agent-codex-組み込み検討]] — 5 段階運用ループ + 既存資産接続点 + 試用対象 candidate-001 + 最小運用フロー + 収益化インパクト比較 + Hermes 採用閾値 |
| 残作業 | candidate-001 が approved 後に最小運用フロー実行（vloop スコープ外・人間判断 + API 課金）|
| 推奨 | 本サイクルでは検討材料整備のみ / 採用判断は candidate-001 approved 後 |

### 優先 2（vloop8 で達成）: ~~#59 Vault 全体棚卸し~~ → Phase 1-5 達成（完了条件 8/8）

| 項目 | 内容 |
|---|---|
| 状態 | **artifact_exists**（vloop8 で Phase 1-5 完了）|
| 達成内容 | [[Vault全体棚卸し]] — 主要フォルダ実在マップ + 旧/新/暫定/削除候補分類 + 正本マッピング + iPhone 確認手順 + 全体マップ Mermaid + 完了条件 8/8 達成 |
| 残作業 | 個別ファイルへの「📦 移行済」「📄 参照のみ」注記追加（次サイクル）+ 全 .md 存在しないパス grep 検査（次サイクル）+ chatgpt/ フォルダの運用ルール確定（次サイクル）|
| ユーザー判断待ち | 「無題のフォルダ」削除（iPhone Obsidian 操作）+ `Vaultの見方_どこを見れば何がわかるか` 分割可否 |

### 優先 3（vloop6 で完了）: ~~N-03 / N-04 有力候補化判断~~ → candidate-006 / 007 起票済

| 項目 | 内容 |
|---|---|
| 状態 | **artifact_exists**（vloop6 で起票完了）|
| 達成内容 | [[../05_monetization/scenarios/candidate-006]]（LLM Chooser / 26-40）+ [[../05_monetization/scenarios/candidate-007]]（Vault Search Cheatsheet / 28-40）|
| 残作業 | candidate-006 / 007 の補助 4 ファイル × 2 = 8 ファイル（次サイクル候補 = 新しい優先 6）|

### 優先 6（vloop6 で新規発生）: candidate-006 / 007 の判断するための資料一式

| 項目 | 内容 |
|---|---|
| 状態 | **planned_only**（候補本体は完成 / 補助 4 ファイル × 2 は未着手）|
| 何をやるのか | candidate-006 / 007 に公開ブロッカー + 7 日プラン + progress 投入設計 + ChatGPT 承認パック を各 4 ファイルずつ作成（計 8 ファイル）|
| なぜ必要なのか | candidate-005 と同水準まで持っていく / ChatGPT が方向性レビューできる状態にする |
| 今どの状態か | 候補本体のみ完成 / 補助 4 ファイル未着手 |
| 次に Claude が何をすべきか | 次サイクルで candidate-005 と同形式で 8 ファイル作成 |
| ユーザー確認が必要か | ChatGPT 方向性レビュー時に判断 |

### 優先 4（vloop5 追加）: #69 残ページ完全日本語化

| 項目 | 内容 |
|---|---|
| 状態 | **partial_done**（vloop4+5 で主要 6 ページ反映 / 残り低優先ページ）|
| 何をやるのか | candidate-001/002/003/004/005 本体 + epics 本文 + 各補助 md（公開ブロッカー / 7 日プラン / progress 投入設計）への用語注記追加 |
| なぜ必要なのか | iPhone Obsidian での読解容易性向上 |
| 今どの状態か | 主要 6 ページ反映済（00_START_HERE / scenarios/README / 案の情報源と採用理由 / idea_trace / Issue完了判定ルール / 試作ループ検証） |
| 次に Claude が何をすべきか | candidate 本体 5 件 + 補助 md 多数。1 サイクルで全件は重いため次サイクルで実施 |
| ユーザー確認が必要か | 残ページ反映後、iPhone でレビュー |

### 優先 5（vloop5 追加）: #68 残ページへの Mermaid 反映

| 項目 | 内容 |
|---|---|
| 状態 | **partial_done**（vloop4+5 で 4 ページ反映: epics / Issue完了判定ルール / idea_trace / 試作ループ検証）|
| 何をやるのか | 残りの主要 Mermaid 図に classDef 色分けを追加 / 新規 Mermaid 必要箇所への図追加 |
| なぜ必要なのか | 状態色分けの統一性向上 |
| 今どの状態か | 4 ページ反映済。candidate 系・vloop サマリー過去分は未反映 |
| 次に Claude が何をすべきか | 直近の vloop サマリーで本テンプレを採用継続。candidate 本体への Mermaid 追加は次サイクル |
| ユーザー確認が必要か | 反映後の見やすさレビュー |

---

## 🧑 ユーザー確認待ち（あなたが見ると進むもの）

| 対象 | 何を確認 | 場所 |
|---|---|---|
| candidate-001 / candidate-005 | ChatGPT が判断するための資料一式（承認パック）を読んで approve / hold / reject | [[ChatGPT承認待ち]] / [[candidate-001_ChatGPT承認パック]] / [[candidate-005_ChatGPT承認パック]] |
| iPhone Obsidian 入口 | 00_START_HERE のリンクが全て iPhone Safari で開けるか | [[../00_START_HERE]] |
| 試作モック 3 件 | ブラウザで開いてサンプル動作確認 + iPhone Safari 表示確認 | `90_prototypes/{token-speed-tool,llm-chooser,vault-search-cheatsheet}/index.html` |
| cron 投入判断 | research-run / idea-run の自動化 | [[../05_monetization/cron移行判定基準]] |
| 既存 done だが open のまま Issue（#18-#65 多数）| Issue 一覧の close 判断 | GitHub Issue 一覧 |

---

## 🗂 やりっぱなし防止のルール（Issue #70 より）

1. ToDo 化したら **次回 vloop の Step 2 で本ページを確認**
2. 実体ファイルができたら本ページから消す（または「実体化完了」セクションへ移す）
3. user_check 状態は**自動的に done にしない**（ユーザー確認まで保持）
4. 新ルール「[[Vault蓄積運用ルール]]」§ 1. ToDo を追加したとき に従って本ページに残す

---

## 📎 vloop 終了時の必須出力（本ページとの連動）

- 今回新しく実体化した ToDo（本ページから消した / 完了に移した）
- 今回新しく planned_only として追加した ToDo
- 次サイクルで最優先のもの 1-2 件
- ユーザー確認待ちで放置されているもの

---

## 関連

- [[Vault蓄積運用ルール]]（本ページの根拠ルール）
- [[../00_START_HERE]]（iPhone 入口）
- [[Issue完了判定ルール]]（状態 6 分類 + レビュー 4 分類）
- [[../05_monetization/idea_trace]]（全案ハブ）
- [[ChatGPT承認待ち]]（candidate 承認入口）
- Issue: kaeru07/vault#70 / #63 / #68 / #69 / #67 / #59
