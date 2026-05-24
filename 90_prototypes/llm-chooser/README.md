---
title: LLM Chooser — Claude / Codex / Gemini / ローカル 使い分けチャート
type: prototype
issue: kaeru07/vault#61
relatedIssues: [kaeru07/vault#63]
created: 2026-05-24
updated: 2026-05-24
status: MVP モック完成（N-03 ID）
tags: [prototype, no-api, llm, chooser]
---

# LLM Chooser

> 試作ループ Phase6（N-03）。**質問に答えて使うべき LLM を提案する 1 枚物の判定フロー**。

## 構成

| ファイル | 役割 |
|---|---|
| `index.html` | UI 一式（クイズ + 全 LLM 早見表 + 機能比較表） |
| `README.md` | 本ファイル |

## 使い方

```bash
# ブラウザで直接開く（CDN ゼロ）
xdg-open /root/company/obsidian-vault/90_prototypes/llm-chooser/index.html
# またはローカル配信
python3 -m http.server 8766 -d /root/company/obsidian-vault/90_prototypes/llm-chooser
```

iPhone Safari でも動作（viewport 設定済み）。

## 機能

- 質問 4 種から枝分岐 → 4 候補（Claude / Codex / Gemini / ローカル）のいずれかを提案
- 各 LLM のカード（得意 / 苦手 / 代替候補）
- 機能比較表（9 項目 × 4 LLM）
- 全部 HTML+CSS+JS インライン・外部 CDN ゼロ

## 収益化導線

- アプリ広告（無料層）
- AI 開発者層の Shorts 送客（「あなたに合う LLM は？」1 分動画）
- テンプレ販売（「LLM 使い分けチートシート Markdown」）

## 既知制約

- 判定基準は**個人観察ベース**（実測ではない）
- LLM の性能比較数値は手入力（API ベンチではない）
- モデル更新に追従するには手動メンテ必要

## 次の改善

1. iPhone 実機表示確認
2. 各 LLM カードに「最新モデル名」を入れて自動更新化（モデル変動が早いので URL リンクのみが現実的）
3. token-speed-tool（候補-005）と相互リンク
4. ChatGPT に観点レビュー依頼

## 関連

- spec/source: [[../../05_monetization/idea_trace]]（案 N-03）
- 類似試作: [[../token-speed-tool/README]]（候補-005）
- Issue: kaeru07/vault#61
