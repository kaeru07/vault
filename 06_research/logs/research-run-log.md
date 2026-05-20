---
title: research-run / idea-run 実行ログ（1 行 = 1 サイクル・最新が下）
type: monetization-research-log
issue: kaeru07/vault#37
relatedIssues: [kaeru07/vault#38, kaeru07/vault#39]
created: 2026-05-20
updated: 2026-05-20
status: active（実行のたびに追記）
---

# research-run / idea-run 実行ログ

> [[README]] の §3 フォーマットに従い 1 サイクル 1 行追記。**最新が下＝時系列**。
> 実行は未開始（[[../../05_monetization/cron_research-run_idea-run設計]] #36 の実装・cron 登録待ち）。

## 実行履歴

| 日付 | サイクル | 全件 | 成功 | 失敗 | 失敗源 | 終了コード | idea-run 進行 | 備考 |
|---|---|---|---|---|---|---|---|---|
| (未実行) | — | — | — | — | — | — | — | research-run / idea-run 未実装。本ログは形式固定のため設置 |
| 2026-05-21 | manual-mvp-08:32 | 1 | 1 | 0 | — | 0 | 進行 | Issue #44 MVP 1 サイクル / ai-news 1 源・15 件 / idea-run 10 案・上位 3 件抽出（candidate 化はせず）|

## 上位 3 件抽出（2026-05-21 MVP・candidate 化は未実施）

| 順位 | ideaId | 粗score | 案 | 既存 candidate 重複 | 判定 |
|---|---|---|---|---|---|
| 1 | 20260521-002 | 13 | CPU only transcription を nanikiru-shorts に組み込む（自分向け効率化） | なし | 安全弁適用: 根拠 2・人間レビューで再判断（candidate 化保留） |
| 2 | 20260521-001 | 11 | Qwen3.7-Max エージェント評価アプリ | なし | 同上 |
| 3 | 20260521-003 | 10 | AI モデルのトークン速度比較ツール | なし | 同上 |

> 安全弁: 調査根拠 1 源・n=15 で根拠 = 2 のため、ranking-rule §3 により candidate 化は保留。次サイクルで Reddit / iTunes Search を追加し根拠 ≥ 3 を満たした時点で candidate 化を検討（[[../../05_monetization/ranking-rule]]）。

> 最初のサイクル実行時は本テーブルに 1 行追加（人間運用 or research-run 自体が追記コマンドを実行）。

## サンプル行（参考・実履歴ではない）

```
| 2026-05-21 | daily-07:00 | 8 | 7 | 1 | x-trend | 1 | 進行 | レート超過 |
| 2026-05-21 | reboot     | 3 | 3 | 0 | — | 0 | 進行 | Phase 1 のみ |
```

## 関連

- [[README]]（ログ運用ルール）
- [[../../05_monetization/cron_research-run_idea-run設計]]（#36）
- Issue: kaeru07/vault#37 / #38（重複）
