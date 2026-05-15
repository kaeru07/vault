---
status: active
last_touched: 2026-05-16
monetization_impact: medium
theme: [market-research, app-strategy, workflow]
phase: v1-done
url: http://localhost:3030
repo: https://github.com/kaeru07/scrape-lab-v2
repoVisibility: private
managementState: github-managed
---

# scrape-lab-v2（調査・収集・差分検知アプリ）

> URL 単位の手動スクレイピング・差分検知・調査結果ストレージ。
> 既存 Scrape Lab（note アプリ）を継ぎ足さず、v2 として新設。将来の市場調査 Hub の基盤候補。

---

## 目的

- URL 単位の手動スクレイピングと履歴蓄積
- 前回取得との差分検知（変化の早期把握）
- 調査結果を JSON / Markdown 化し、Claude / Codex / ChatGPT へレビュー連携
- DB を持たずファイルベースで「後から AI が読みやすい」構造を維持
- 市場調査 Hub への流用を見据えた最小コア

---

## 起動方法

```
cd /root/company/apps/scrape-lab-v2
npm install
npm run dev      # http://localhost:3030
npm run lint
npm run build
```

- ポート 3030（progress 3010 / 既存と衝突しない）
- 環境変数・.env 不要（外部 AI API 連携なし）

---

## ディレクトリ

```
src/
  app/
    layout.tsx / page.tsx / globals.css
    runs/page.tsx                 run一覧
    runs/[runId]/page.tsx         run詳細
    api/scrape/route.ts           POST 取得
    api/runs/route.ts             一覧
    api/runs/[runId]/route.ts     詳細
    api/runs/[runId]/export/route.ts JSON/Markdown DL
  components/  ScrapeForm / RunList / RunDetail / CopyBox
  lib/         types / url-validator(SSRF) / static-scraper(Cheerio)
               / browser-scraper(設計枠) / storage / diff / markdown / paths / id
data/
  runs/YYYY-MM-DD/<runId>.json    run実体
  pages/<urlHash>/history.ndjson  URLごと履歴
  pages/<urlHash>/meta.json       URLメタ
```

---

## データ保存場所

- run 実体: `data/runs/YYYY-MM-DD/<runId>.json`
- URL 履歴: `data/pages/<urlHash>/history.ndjson`
- URL メタ: `data/pages/<urlHash>/meta.json`
- `data/runs` / `data/pages` / `data/exports` は **.gitignore 済み**（GitHub には載らない）
- エクスポート（JSON / Markdown）は API でオンザフライ生成しダウンロード（ファイルは残さない）

---

## 現在の v1 機能

- URL 入力フォーム（iPhone 対応 / 44px タップ領域）
- static fetch + Cheerio 取得（title / description / 本文 / リンク / meta / 見出し）
- browser モードは **設計枠のみ**（playwright 未導入）
- SSRF ブロック（localhost / 127.0.0.1 / private IP / .local / .internal / file:// / 非 http(s)）
- タイムアウト 15s（60s まで指定可）/ レスポンス 5MB 上限 / 本文 20,000 字上限
- 差分検知（title / description / body 文字数差 / links 追加・削除）
- run 一覧 / 詳細画面
- JSON / Markdown エクスポート
- Claude/Codex レビュー用コピー欄
- 取得失敗時も run として保存し原因表示

---

## 未対応

- browser モード本体（JS レンダリング必要サイト）
- 本文の行レベル diff（現状は文字数差のみ）
- テーマ別調査（キーワード → URL リスト → 一括取得）
- runs のフィルタ・検索
- Obsidian / Vault への自動書き出し
- 取得スケジューリング・通知

---

## 次の v2 候補（整理のみ・未実装）

| 候補 | 内容 | 依存・注意 |
|---|---|---|
| browser mode | playwright-core + @sparticuz/chromium で動的サイト対応 | 依存導入が必要。設計枠は実装済み |
| 行レベル diff | 本文を行単位で比較し変更箇所を可視化 | 差分解釈の精度向上 |
| キーワード→URL一括調査 | テーマ入力 → URL リスト → 一括取得 → サマリ | 市場調査 Hub の中核機能 |
| Obsidian 自動書き込み | 取得結果を `06_research/` 等へ Markdown 出力 | 機密ガード必須 |
| 市場調査 Hub 連携 | 調査結果を収益化候補・競合分析へ接続 | app-strategy と連動 |
| Claude/Codex 定期実行 | 取得結果の自動レビュー | **cron/自動ループは現運用ルールで要慎重判断** |

> いずれも本フェーズでは実装しない。優先度はユーザー判断。

---

## 市場調査 Hub との関係

- scrape-lab-v2 は「URL 単位の取得 + 差分 + 履歴」という**最小コア**を担う
- 市場調査 Hub は「キーワード/テーマ → 複数 URL → 取得 → 横断サマリ → 収益化判断」の上位レイヤー
- v1 のファイル構造（`data/runs` / `data/pages`）はそのまま Hub のデータ源として再利用できる設計
- Hub 連携の入口は「キーワード→URL一括調査」候補（上表）

---

## 収益化可能性

- monetizationImpact: **medium**
- 直接課金プロダクトではなく、**市場調査・競合分析の内製基盤**としての価値
- 競合/トレンドの定点観測 → アプリ戦略（app-strategy）の意思決定を高速化
- 将来、調査レポートの自動生成まで繋げれば外部提供の芽もあるが現時点は内部効率化が主目的

---

## GitHub 管理状態

- repo: https://github.com/kaeru07/scrape-lab-v2
- visibility: **private**
- branch: main
- 初回 commit: `2e3e761`（v1 全体 / 2026-05-16）
- 管理状態: **github-managed**（local-only → git-only → github-managed）

---

## 関連

- 作業レビュー: [[../20_reviews/2026-05-15_scrape-lab-v2-init]]
- レビューキュー: [[../20_reviews/_review_queue]]
- progress runId: 20260515-123610（v1 作成）/ 20260516 系（後処理）
