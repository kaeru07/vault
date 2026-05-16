---
date: 2026-05-16
task: company 全体のサービスマップ作成 (docs/service-map.html + json)
runId: 20260516-113513
targetApp: company-meta
monetizationImpact: low
theme: [workflow, obsidian]
relatedRunIds: []
commitHashes: []
---

# 2026-05-16 company 全体サービスマップ作成

> commitHashes 空。理由: company は git 管理外フォルダのため docs/ は版管理対象外（バックアップは手動）。本レビュー md 自体は obsidian-vault 側 commit で push される。

---

## 1. 作業目的

- なぜ: 次回以降の Claude / ChatGPT / Codex / 人間の「読み解きコスト」を下げる
- 背景: company は多数アプリ + 運用ルール + Vault 連携が絡み、初見・数週間後に迷子になりやすい
- 期待効果: サービス単位の俯瞰 1 枚（HTML）と機械可読の前提情報（JSON）で、毎回ゼロから構造を再調査しなくて済む

---

## 2. 実施内容

- README だけでなく**コード・ディレクトリ・package.json・ルーティング・data 保存先**を実調査
- company トップ / 運用ファイル群 / apps 全 14+ / ny01 サブ 8 / progress 内部 / vault 2 系統を確認
- 7 サービスに分割: company-governance / progress / obsidian-sync / scrape-lab-v2 / codex-sdk-experiment / revenue-apps / support-apps
- `docs/service-map.json`（指定スキーマ完全準拠）と `docs/service-map.html`（横長 6 列フロー俯瞰）を新規作成
- 実装・既存機能・データ変更は**一切なし**。`.env` は値を出さずキー名のみ参照

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| /root/company/docs/service-map.json | 新規（productName/rootPath/purpose/overview/services[7]/dataFlow/runCommands/importantFiles/unknowns/recommendedReadingOrderForAI/summaryForNextPrompt） |
| /root/company/docs/service-map.html | 新規（ダーク横長: 入力元→運用ルール→画面→API→保存→自動処理/GitHub、カード+矢印、5色分け、CSS埋込・CDN不使用、パス表示） |

---

## 4. 検証結果

- JSON: `python3 json.load` 妥当 / 全 11 キー / services 7 件
- HTML: 外部 CDN 参照なし / `<style>` 埋込 / `</html>` 閉じ OK / HTML・JSON パス上部表示 / 色クラス c-y/c-b/c-g/c-r/c-p 全存在 / 13.6KB
- build/lint/typecheck: **実装変更ゼロのためプロダクト本体に対しては非対象**。指示「可能なら」に対し静的成果物の健全性検証で代替
- 機密パターン事前チェック: OK（.env 値・キー実値・トークンは記載なし。PROGRESS_DATA_PATH はキー名のみ）
- ob sync: 後述 / git push(vault): 後述

---

## 5. 未対応

- revenue-apps / support-apps の各個別アプリの画面・API・デプロイ先詳細（company レベルからは unknown。JSON の unknowns に明記。各リポで確認要）
- note と scrape-lab-v2 の統廃合方針（未決）
- progress 本番 3010 の Codex 連携反映（再起動・ユーザー判断）

---

## 6. 危険ポイント

- 調査のみ・**実装/データ/設定の変更ゼロ**（ロールバックは docs/ 削除のみ）
- company が git 管理外 → docs/ はバックアップ手動。誤削除注意
- 資料は 2026-05-16 時点のスナップショット。アプリ追加・状態変化で陳腐化する（app-index.md が状態の正本なので併読前提）

---

## 7. 次にやるべきこと

- 次回 AI 依頼時に `docs/service-map.json` を前提情報として貼る運用に乗せる
- revenue-apps を本格調査するときは各リポで個別 service-map を作る
- service-map の定期更新トリガー（アプリ追加・大きな構成変更時）を運用ルール化するか検討

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象: company 全体 (メタ作業 / docs/service-map)
作業: company 全体のサービスマップ作成
runId: 20260516-113513
日付: 2026-05-16
成果物: /root/company/docs/service-map.html, /root/company/docs/service-map.json

## 作業目的
- 次回以降の AI/人間の読み解きコスト削減。設計書ではなく「迷子にならない」内部俯瞰資料

## 実施内容
- company 全体を実調査(コード/ディレクトリ/package.json/ルーティング/data保存先)
- 7サービスに分割し service-map.json(機械可読) と service-map.html(横長俯瞰) を新規作成
- 実装変更なし・調査のみ

## 検証結果
- JSON: 妥当・全キー・services7
- HTML: CDN外部参照なし/style埋込/閉じタグOK/パス表示/5色分け
- 実装変更ゼロのため build/lint は本体非対象(静的健全性検証で代替)

## 未対応
- 個別 revenue/support アプリ詳細は unknown
- note × scrape-lab-v2 統廃合方針

## 危険ポイント
- 調査のみ・変更ゼロ
- company git管理外 / 資料はスナップショット(app-index.md 併読前提)

## 確認したい観点
- 7サービスの分割粒度は妥当か(過多/過少)
- dataFlow の本線記述に誤り・抜けはないか
- 「正本は3つ(execution-runs.json / _review_queue.md / app-index.md)」という整理は正しいか
- recommendedReadingOrderForAI は実用的か
- 陳腐化対策(更新トリガー運用)の要否
````

---

## 関連

- progress runId: 20260516-113513（正本）
- 成果物: /root/company/docs/service-map.html / service-map.json
- 関連アプリ: [[../02_apps/progress]] / [[../02_apps/scrape-lab-v2]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
