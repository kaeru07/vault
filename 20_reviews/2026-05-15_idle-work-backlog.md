---
date: 2026-05-15
task: Claude/Codex 余剰リソース消化用 作業バックログ整理（棚卸し / 30 案 / Shorts / Play・Web / ASO / ToDo / ランキング 4 種）+ 圧縮版 TOP5 追補
runId: 20260515-000616
targetApp: company-meta
monetizationImpact: high
theme: [workflow, app-strategy, monetization, market-research]
relatedRunIds: [20260514-234723, 20260514-232120]
commitHashes: [7d70dd0]
---

# 2026-05-15 放置で進められる作業バックログ整理

> 1 作業 = 1 ファイル の自動生成レビュー。
> 詳細ルールは `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール。

---

## 1. 作業目的

- なぜこの作業をするのか: Claude / Codex の余剰リソースを使って深夜放置・大量並列で進められる作業の素材を網羅的に整理する
- 背景: 主力アプリの一部は「あと少しで公開可能」状態のまま塩漬けになっており（mahjong / ny-ai など）、片付け作業を機械化できるバックログを持っておくと意思決定が高速化する
- 期待効果: 「次に何やる?」と毎回考える時間を削減し、深夜放置 / 並列 Claude / Codex に投げて回せる ToDo を即座に取り出せるようにする

---

## 2. 実施内容

- 主な変更:
  - `06_research/idle-work-backlog-2026-05-15.md` を新規作成
  - 既存アプリ 17 件の棚卸し（収益化距離 + 放置向き度）
  - 収益化近い順ランキング
  - 即公開候補リスト
  - 既存資産活用軸の新規 30 案
  - Shorts 量産 80 本+ の元ネタ
  - Play 向き 8 案 / Web 向き 7 案
  - README 統一テンプレ + 改修順序
  - スクショ撮影プラン
  - SEO / ASO / ストア説明文案
  - 市場調査・スクレイピング案（倫理ガード明記）
  - Obsidian 追記案 + 新規研究ノート候補
  - progress 登録用 ToDo 30 件
  - ランキング 4 種（今すぐ TOP10 / 深夜放置 TOP10 / 収益化インパクト TOP10 / 1 日軽作業 TOP20）
- 関連調査・判断:
  - `apps/app-index.md` / `pm/active-projects.md` / `pm/priorities.md` を確認し最新の主力アプリ状態を反映
  - 前ターンの 30 案（[[local-first-app-ideas-2026-05-14]]）と被らない別 30 案を意識（既存資産横展開 / ニッチ深掘り軸）
  - 「未確認なのに OK 扱い禁止」「AI API 前提禁止」「大規模破壊リファクタ禁止」を全案で適用

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `obsidian-sync-vault/06_research/idle-work-backlog-2026-05-15.md` | 新規。既存棚卸し / 30 案 / Shorts / Play/Web / ASO / ToDo / ランキング 4 種 |

---

## 4. 検証結果

- typecheck: n/a（Markdown のみ）
- build: n/a
- lint: n/a
- 手動確認: 棚卸し表に既存 17 件揃っているか確認 / ランキング 4 種で各 TOP 10/20 揃っているか確認 / 禁忌（AI API・外部 API 前提）が混入していないか確認
- 機密パターン事前チェック: OK（具体的な API キー・認証情報は含まない / Vault パス例示のみ）
- ob sync: Fully synced（後述）
- git push: 後述

---

## 5. 未対応

- スキップした項目:
  - 各 ToDo を progress アプリへ実際に POST 登録すること（ユーザー判断後にバッチ実行する方が良いため、ここではリストアップのみ）
  - 個別 ASO 上位 20 アプリの実調査（次の深夜放置タスクで実施）
- 環境制約で実行できなかったこと: なし
- ユーザー判断待ち:
  - 今すぐ TOP 10 のうちどれを最初に投げるか（mahjong Vercel デプロイ / ny-ai URL 確認 / mahjong 問題追加が三大候補）
  - 30 案中の本命を前ターン分（朝散歩 / 庭タイムラプス / 打牌セルフ採点）と統合してどれから着手するか

---

## 6. 危険ポイント

- 既存機能への影響リスク: なし（Vault 内 Markdown 追加のみ）
- DB / 認証 / 本番 / 機密に触れたか: ノー
- ロールバック手段: `git log --follow 06_research/idle-work-backlog-2026-05-15.md` で容易に戻せる
- 観察すべき項目:
  - 30 案のいくつかが前ターン分と概念的に被る可能性（明示的に「別軸」と書いてあるが、本命選定時に統合議論を要する）
  - Shorts 量産案で「Mリーグ放送切り抜き」を権利 NG として明記したが、ユーザーが他媒体を提案する場合は別途権利確認

---

## 7. 次にやるべきこと

- フォローアップ作業:
  - 今すぐ TOP 10 から 1〜2 件を progress ToDo として実 POST 登録
  - 深夜放置 TOP 10 から 1 件を試験運用（推奨: mahjong 問題 10 問追加 → build/typecheck/lint で品質確認 → commit/push）
  - ny-ai Vercel URL 確認（15 分作業）→ `apps/app-index.md` 更新
- ユーザー判断待ち事項:
  - 「最初に着手する 1 件」の選定
  - 30 案の前ターンと今ターンを統合した本命候補の再選定
- 観察項目（数日〜数週間）:
  - 深夜放置で実行した作業の品質（build/typecheck/lint が機械判定で適切に止まるか）
  - ASO 上位 20 調査で何件着手判断材料が得られるか

---

## 8. ChatGPT レビュー依頼文

そのままコピペで ChatGPT に流せる形:

````text
以下は Claude Code の作業整理報告です。レビューしてください。

対象アプリ: company-meta（横断バックログ）
作業: Claude/Codex 余剰リソース消化用 作業バックログ整理
runId: 20260515-000616
日付: 2026-05-15
GitHub commit: （本ファイル push 時に確定）

## 作業目的
- 深夜放置・大量並列で進められる作業の素材を網羅
- 収益化・公開・SNS・市場調査を機械的に前進させる ToDo を取り出せる状態を作る

## 実施内容
- 既存アプリ 17 件の棚卸し表（収益化距離 + 放置向き度）
- 収益化近い順ランキング
- 即公開候補抽出
- 既存資産活用軸の新規 30 案
- Shorts 量産 80 本+ の元ネタ
- Play 向き 8 案 / Web 向き 7 案
- README 統一テンプレ + 改修順序
- スクショ案 / SEO/ASO / ストア説明文
- 市場調査 + スクレイピング案（倫理ガード明記）
- Obsidian 追記案 + 新規研究ノート候補
- progress 登録用 ToDo 30 件
- ランキング 4 種: 今すぐ TOP10 / 深夜放置 TOP10 / 収益化インパクト TOP10 / 1 日軽作業 TOP20

## 変更ファイル
- obsidian-sync-vault/06_research/idle-work-backlog-2026-05-15.md（新規）

## 検証結果
- 棚卸し表 17 件揃っている
- ランキング 4 種で各 TOP 10/20 揃っている
- 禁忌（AI API・外部 API 前提）が混入していない

## 未対応
- 個別 ASO 上位 20 アプリの実調査（次の深夜放置タスクで実施）
- progress への ToDo 実 POST 登録（ユーザー判断後）

## 危険ポイント
- 30 案が前ターンと一部重複の可能性 → 統合議論時に整理必要
- Shorts 量産で権利物の扱いに注意（自分の対局のみ）

## 次にやるべきこと
- 今すぐ TOP 10 から 1〜2 件選定 → progress ToDo 化
- 深夜放置 TOP 10 から 1 件試験運用
- ny-ai Vercel URL 確認

## 確認したい観点
- 棚卸し漏れ（Docker 訓練環境 / company-helper は粒度妥当か）
- ランキングの妥当性（収益化インパクト TOP の順位は現実的か）
- Shorts 量産案で権利 NG が明示されているか
- 30 案の中で「既存資産流用」と書いているが本当に流用効くか
- 禁忌（AI API・外部 API 前提）が混じっていないか
- 深夜放置 TOP 10 のうち「失敗時の被害」が本当に小さいか
- monetizationImpact: high の評価は妥当か
````

---

## 関連

- progress runId: 20260515-000616（正本）
- 関連 run: 20260514-234723（ローカル完結 30 案）/ 20260514-232120（claude-task-template 更新）
- 調査ファイル: [[../06_research/idle-work-backlog-2026-05-15]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- 関連アプリ: [[../02_apps/mahjong]] [[../02_apps/nanikiru-shorts]] [[../02_apps/shogi-trainer]] [[../02_apps/progress]] [[../02_apps/途中プロジェクト一覧]]
- 関連戦略: [[../05_monetization/収益化候補一覧]] [[../05_monetization/収益化ロードマップ]]
- フォルダ運用: [[../20_reviews/README]]
