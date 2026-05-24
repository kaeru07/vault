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

## ✅ 直近 vloop（vloop1-3）で実体化完了したもの

| Issue | 元の ToDo | 状態 | 実体ファイル / 場所 |
|---|---|---|---|
| #60 | トークン速度ツール APIなし試作 | **artifact_exists + user_check** | [[../05_monetization/token-speed-tool]] + [[../90_prototypes/token-speed-tool/README]] + 承認材料 4 ファイル + [[candidate-005_ChatGPT承認パック]] |
| #61 | 複数案試作ループ | **artifact_exists + user_check** | [[../05_monetization/試作ループ検証]] + [[../90_prototypes/llm-chooser/README]] + [[../90_prototypes/vault-search-cheatsheet/README]] |
| #62 | トークン速度ツール trace | **artifact_exists** | [[../05_monetization/token-speed-tool]] Part1 + [[../05_monetization/idea_trace]] §2 |
| #63 | 全アプリ案情報源ページ | **artifact_exists** | [[../05_monetization/idea_trace]]（10 案カード）+ 本サイクルで [[../05_monetization/案の情報源と採用理由]] 別名エイリアス追加 |

> [!note] **user_check = あなたの確認待ち**
> 上記 #60 / #61 は Claude 側の作業は完了しているが、ユーザーが iPhone 実機 / ブラウザで確認するまで「完全 done」にはしない。

---

## 🔥 次に Claude/vloop が手を動かすべき ToDo（優先順）

### 優先 1: #68 現在地図テンプレ + 主要ページ反映

| 項目 | 内容 |
|---|---|
| 状態 | **planned_only**（本サイクルで in_progress → artifact_exists へ移行予定）|
| 何をやるのか | Mermaid flowchart の現在地図テンプレを作る + done/user_check/open を一目で分かる表記に統一 + 主要ページに反映 |
| なぜ必要なのか | ユーザーが「全 Epic で人間判定待ちフェーズ」と書かれた図に違和感を持っていた。状態分類が混同されている |
| 今どの状態か | テンプレ未作成・本サイクル着手 |
| 次に Claude が何をすべきか | 90_templates/ 配下に現在地図テンプレ作成 → 00_START_HERE / epics.md / Issue完了判定ルール に反映 |
| ユーザー確認が必要か | テンプレ確定後にユーザー視点でレビュー |

### 優先 2: #69 内部用語のユーザー向け日本語化

| 項目 | 内容 |
|---|---|
| 状態 | **planned_only**（本サイクルで一部 in_progress 予定・全置換は次サイクル以降）|
| 何をやるのか | 「承認パック」「公開ブロッカー」「progress 投入」等の内部用語を、ユーザー向け表現を主にし内部名を括弧で補足する |
| なぜ必要なのか | iPhone Obsidian で読む際に意味が伝わりにくい |
| 今どの状態か | Issue #69 本文に置換マッピング表あり。実置換未着手 |
| 次に Claude が何をすべきか | 本サイクルは主要 5-6 ページ（00_START_HERE / scenarios/README / idea_trace / epics / 次に実体化するToDo）のみ反映。残りは次サイクル |
| ユーザー確認が必要か | 主要ページ反映後、iPhone Obsidian で読みやすさレビュー |

### 優先 3: #67 Hermes Agent × Codex を運用に組み込み検討

| 項目 | 内容 |
|---|---|
| 状態 | **planned_only / 検討中**（本サイクルでは着手しない）|
| 何をやるのか | Hermes Agent × Codex を市場調査 → 実装 → 改善サイクルにどう組み込むか整理 |
| なぜ必要なのか | 既存 Progress / Vault 運用に取り込めれば収益化サイクル高速化 |
| 今どの状態か | Issue #67 本文に検討範囲明示・実検討未着手 |
| 次に Claude が何をすべきか | 次サイクルで議論型 Issue として ChatGPT と整理（vloop 単独では結論出ない可能性） |
| ユーザー確認が必要か | ChatGPT と Claude で範囲確定後、ユーザーが収益化インパクト判断 |

### 優先 4: #59 Vault 全体棚卸し（旧運用と新運用統一）

| 項目 | 内容 |
|---|---|
| 状態 | **planned_only**（大規模 Epic / Phase 分割推奨）|
| 何をやるのか | Vault 全体を棚卸しし、旧運用と新運用を統一する |
| なぜ必要なのか | フォルダ構造・テンプレ・運用ルールに新旧混在の可能性 |
| 今どの状態か | 着手していない |
| 次に Claude が何をすべきか | Phase 分割（フォルダ別 / テンプレ別 / 用語別 = #69 と一部統合可）して計画化 |
| ユーザー確認が必要か | Phase 計画確定時にユーザーが優先順位判断 |

### 優先 5: N-03 / N-04 candidate 化判断

| 項目 | 内容 |
|---|---|
| 状態 | **artifact_exists**（試作は完成済）/ candidate 化判断 planned_only |
| 何をやるのか | N-03 LLM Chooser / N-04 Vault Search Cheatsheet を candidate-006 / candidate-007 として scenarios 正規化 |
| なぜ必要なのか | 試作完成後、収益化判断を済ませて承認待ちフローに乗せる |
| 今どの状態か | 試作完成（[[../90_prototypes/llm-chooser/README]] / [[../90_prototypes/vault-search-cheatsheet/README]]）/ candidate 化判断未着手 |
| 次に Claude が何をすべきか | ChatGPT が試作レビュー後、candidate-006 / 007 として scenarios 化（N-03 判定基準客観化要 / N-04 既存 Vault 見方ガイドとの重複整理要）|
| ユーザー確認が必要か | ChatGPT レビュー結果を見てユーザーが candidate 化進めるか判断 |

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
