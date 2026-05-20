---
title: daily 研究データ 保存規約
type: monetization-research-spec
issue: kaeru07/vault#30
created: 2026-05-20
updated: 2026-05-20
status: active（規約固定・以後この形式で蓄積する）
relatedIssues: [kaeru07/vault#25, kaeru07/vault#26, kaeru07/vault#28]
---

# daily 研究データ 保存規約

> Issue #30。情報収集前に保存形式を固定し、後工程（#26 案生成 / #27 昇格 / #28 統合）を安定化させる。
> 本ファイルが **daily/ 配下の保存規約の正本**。既存設計（#25 トレンド収集エンジン / #26 idea_pool / #28 完全自動化フロー）と一致させる。

## 1. ディレクトリ構成

```
06_research/daily/
├── README.md                        ← 本ファイル（規約）
├── 2026-05-20/                      ← 日付ごとに 1 フォルダ（JST 起算）
│   ├── google-trends.ndjson
│   ├── play-ranking.ndjson
│   ├── app-store-ranking.ndjson
│   ├── reddit.ndjson
│   ├── youtube-search.ndjson
│   ├── x-trend.ndjson
│   ├── product-hunt.ndjson
│   ├── ai-news.ndjson
│   └── summary.md                   ← 当日の要約（#26 案生成の入力）
└── 2026-05-21/...
```

- 日付フォーマット: `YYYY-MM-DD`（JST 0:00 起算）
- 1 日 1 フォルダ。1 源 1 ファイル（NDJSON）
- `summary.md` は当日の上位語・トレンドを 1 ページに要約（#26 入力）

## 2. NDJSON スキーマ（1 行 = 1 トレンド）

```
{
  "collectedAt": "2026-05-20T06:00:00+09:00",
  "source": "google-trends|play-ranking|app-store-ranking|reddit|youtube-search|x-trend|product-hunt|ai-news",
  "term": "トレンド語または項目タイトル",
  "metric": {
    "rank": 0,
    "score": 0,
    "volume": null
  },
  "url": "https://公開URL",
  "category": "任意（カテゴリ名 / null）",
  "raw": "要約（機密・個人情報除外 / 200 文字以内）",
  "dedupKey": "正規化キー（小文字化 + 記号除去 + 同一語統合）"
}
```

### フィールド規約

| フィールド | 必須 | 型 | 規約 |
|---|---|---|---|
| collectedAt | 必須 | string | ISO 8601 + JST（`+09:00`）。秒精度 |
| source | 必須 | enum | 8 種（上記 enum 文字列のみ。新規追加は別途決定） |
| term | 必須 | string | 1〜200 文字。前後空白 trim |
| metric.rank | 任意 | int | 順位（1〜N）。取れなければ `0` |
| metric.score | 任意 | int / float | 各源固有のスコア。取れなければ `0` |
| metric.volume | 任意 | int / null | 検索ボリューム等。**推測値は入れない。不明は null** |
| url | 必須 | string | 公開 URL。トラッキング ID は除去 |
| category | 任意 | string / null | カテゴリ名（free text） |
| raw | 必須 | string | 200 文字以内要約。**機密・個人情報を含めない**（GitHub反映ルール準拠） |
| dedupKey | 必須 | string | `term` 正規化（小文字化 + 記号除去 + 連続スペース統合） |

## 3. dedupKey 正規化ルール（実装者向け仕様）

```
1. NFKC 正規化（全角 → 半角、合成文字統合）
2. 小文字化（lowercase）
3. 記号除去（[!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~] を削除）
4. 連続空白を半角 1 個に圧縮
5. 前後空白 trim
```

例: `"麻雀!! 何切る? (AI)"` → `"麻雀 何切る ai"`

## 4. サンプル 1 件（必須・本規約の参照実装）

`06_research/daily/_sample.ndjson` を以下の 1 行 NDJSON とする（実データではない・規約サンプル）。

```
{"collectedAt":"2026-05-20T06:00:00+09:00","source":"play-ranking","term":"麻雀何切る AI","metric":{"rank":3,"score":0,"volume":null},"url":"https://play.google.com/store/apps/details?id=example.mahjong.nanikiru","category":"カードゲーム","raw":"麻雀の何切る問題を AI が解説するアプリ。広告モデル。市場上位継続","dedupKey":"麻雀何切る ai"}
```

> 本サンプルは規約検証用の架空例。実際の収集は #25 実装後・人間承認後に開始。

## 5. summary.md フォーマット（#26 入力）

```markdown
---
date: YYYY-MM-DD
sources_collected: [google-trends, play-ranking, ...]
total_items: 0
---

# YYYY-MM-DD daily summary

## 上位語（dedupKey 集約後・上位 20）
1. <term> （source / rank / volume）
2. ...

## ソース別件数
| source | 件数 | 失敗 |
|---|---|---|

## 異常検知
- レート超過 / 規約変更兆候 / 取得 0 件 等
```

## 6. 機密・個人情報ガード

以下を保存しない（[[../../03_prompts/GitHub反映ルール]] 機密スキャン準拠）:

- API キー / `Bearer ` / `sk-` / OAuth トークン
- パスワード / `password` / `secret` / `token` 値
- SSH 鍵 / 証明書本文（`-----BEGIN`）
- 個人情報（氏名 / メール / 電話 / 住所）
- 認証必要ページの取得内容（公開ページのみ対象）

`raw` フィールドに混入する可能性があるため、収集スクリプト側で **保存前に機密スキャン**を実行する規約。

## 7. ファイル粒度と上限

- 1 ファイル: 最大 1000 行（1000 トレンド / 源 / 日）
- 超過時: `google-trends_2.ndjson` のように連番分割
- 1 日合計: 8 源 × 1000 = 上限 8000 行（実際は数十〜数百想定）

## 8. 既存設計との整合

- [[../トレンド収集エンジン設計]]（#25）: スキーマ完全一致（本規約が確定版）
- [[../../05_monetization/idea_pool/_README]]（#26）: 本 NDJSON を入力に 30 案生成
- [[../../05_monetization/案工場_完全自動化フロー]]（#28）: Step 1 出力 = 本規約準拠

## 未対応点 / 仮説

- 実収集は未実施（#25 実装後）。本規約は**事前固定**として運用、実装時に微修正が出たら本ファイルを正本として更新
- 8 源以外の追加は decision-log に 1 行残してから本規約に追記する仮説
- `metric.volume` が取れない源（Reddit / X 等）は `null` 固定（推測値禁止）

## 次の一手

1. 本規約に従い `_sample.ndjson` を本フォルダに同時 commit（規約検証用）
2. #25 実装着手時は本規約を必ず参照（実装者が独自にスキーマを定義しない）
3. 微修正があれば本ファイルを更新 → decision-log に 1 行
