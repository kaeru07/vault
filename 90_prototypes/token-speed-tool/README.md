---
title: Token Speed Tool — APIなし MVP モック
type: prototype
issue: kaeru07/vault#60
relatedIssues: [kaeru07/vault#62, kaeru07/vault#61, kaeru07/vault#63]
created: 2026-05-24
updated: 2026-05-24
status: MVP モック完成
tags: [prototype, no-api, llm, benchmark]
---

# Token Speed Tool — APIなし MVP モック

> Issue #60 の Phase 3「最小モック / 試作」着地点。
> 静的 HTML + JSON のみで構成し、外部 API を呼ばずに tokens/sec / TTFT / 体感スコアを比較する。

## ファイル構成

| ファイル | 役割 |
|---|---|
| `index.html` | UI 一式（HTML + CSS + JS インライン） |
| `sample-data.json` | 初期サンプルベンチ 5 件（Claude / Codex / Gemini / Ollama / GPT-4o） |
| `README.md` | 本ファイル（使い方・既知制約・次の改善） |

## 使い方

### 1. ローカル表示

```bash
cd /root/company/obsidian-vault/90_prototypes/token-speed-tool
python3 -m http.server 8765
# ブラウザで http://localhost:8765/index.html を開く
```

> [!note] file:// で直接開いた場合
> `fetch('sample-data.json')` が CORS で失敗するため、自動的にインライン定義のサンプルにフォールバックする。挙動は同じ。

### 2. 操作フロー

1. ページを開くと**サンプル 5 件**が表示される（localStorage 空のとき）
2. `[+ 新規追加]` で自分のベンチ結果を追加
3. `[Export JSON]` で全データを JSON 出力
4. `[Import JSON]` で過去 export を再読込
5. **ログ貼付エリア**に Claude Code / Codex の出力をペースト → `[解析してフォームに反映]` で部分抽出

### 3. 体感スコアの計算式

```
uxScore = min(100, tokensPerSec × 0.4 + (10 / TTFT) × 4 + (outputChars / 100) × 0.2)
```

| 要素 | 重み | 意図 |
|---|---|---|
| tokensPerSec × 0.4 | 40% | 生成速度（基本指標） |
| (10 / TTFT) × 4 | 40% | 初回応答の速さ（体感に強く効く） |
| outputChars / 100 × 0.2 | 20% | 出力量（少なすぎるとスコアが伸びない） |

## 実装した機能

- [x] 入力フォーム（モデル / 環境 / 入出力 / 時間 / source / sourceType / confidence / メモ）
- [x] サンプルデータ表示
- [x] 比較テーブル（uxScore 降順）
- [x] 体感スコア自動計算
- [x] JSON エクスポート / インポート
- [x] localStorage 保存
- [x] tokens/sec 比較グラフ（CSS 横棒）
- [x] ログ貼付解析（最小: model / first token / total / output 抽出）
- [x] iPhone 縦表示（viewport + レスポンシブ）
- [x] confidence バッジ（high=緑 / medium=黄 / low=赤）

## 既知制約

- **CDN を読み込まない**（Chart.js などは未使用 / 静的 HTML 単独で動く設計）
- **公開ベンチ自動取得は未実装**（規約リスクのため意図的に未対応）
- **ユーザー間共有は未対応**（静的構成のため）
- **ログ貼付解析は最小**（Claude Code / Codex 風フォーマットの一部のみ）

## iPhone 表示の確認観点

- 入力フォームが縦 1 列で並ぶこと
- 比較テーブルが横スクロール可能なこと
- Export / Import ボタンが指でタップ可能なサイズ（44pt 相当以上）であること
- グラフが画面幅に収まること

## 次の改善（次サイクル以降）

1. グラフを Chart.js 化（CDN 利用）して時系列表示
2. ログ貼付解析の対応フォーマット拡張（Ollama / llama.cpp / OpenAI Playground / Anthropic API レスポンス）
3. 公開ベンチ取り込みインポーター（手動 CSV / JSON）
4. iPhone 実機での操作確認
5. note 化用「2026 年 Q2 LLM ベンチ結果まとめ」（手動執筆）
6. ChatGPT 承認パック化（candidate-005 相当として scenarios 配下へ）

## 関連

- 仕様: [[../../05_monetization/token-speed-tool]]
- trace: 同上 Part 1
- idea_trace: [[../../05_monetization/idea_trace]] §2
- 試作ループ: [[../../05_monetization/試作ループ検証]]
- Issue: kaeru07/vault#60 / #62 / #61 / #63
