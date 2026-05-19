---
title: 収益化候補 承認判断ログ
type: monetization-decision-log
issue: kaeru07/vault#8
created: 2026-05-18
updated: 2026-05-18
status: active
---

# 収益化候補 承認判断ログ

> 収益化シナリオの **承認 / 却下 / 保留の判断履歴**を時系列で残し、後から「なぜそう判断したか」を追えるようにする。
> 状態遷移の正本は [[収益化シナリオ承認フロー]]。本ファイルは**判断の足跡（理由付き履歴）**。
> **AI は記録の追記までを行い、判断（approved/rejected/hold への遷移）は人間のみ**。

## 運用ルール

- 1 判断 = 1 行（テーブル）。判断のたびに**先頭（最新が上）**へ追記
- 記録できる遷移: `pending_approval → approved / rejected / hold` / `hold → pending_approval` / `reviewing → approved / rejected`
- **AI の役割**: 人間が判断したら、その内容をこのログへ追記（事実の記録）。AI 自身が承認・却下を決めない
- 判断理由は [[../90_templates/承認判断テンプレート]] の観点を埋めて記載
- 機密は値を書かない（[[収益化シナリオ承認フロー]] / GitHub反映ルール準拠）

## 判断履歴（最新が上）

| 日付 | シナリオ | 遷移 | 判断者 | 主理由（1行） | コマンド原文（[[ChatGPT承認コマンド標準]]） | 詳細リンク |
|---|---|---|---|---|---|---|
| (まだ承認判断は行われていない) | — | — | — | 全 candidate は pending_approval / candidate のまま | — | [[scenarios/candidate-001]] 他 |

> 例（フォーマット参考・実判断ではない）:
> `2026-05-XX | candidate-001 | pending_approval→approved | 人間(氏名/役割) | 既存資産流用で最速・収益化インパクトhigh | candidate-001 approve | 04_reviews/2026-05-XX-...md`

## 現状サマリー（2026-05-18 時点）

- candidate-001（麻雀何切るアプリ公開）: `pending_approval`（承認待ち・未判断）
- candidate-002（麻雀何切るShorts）: `candidate`（評価継続）
- candidate-003（Obsidianテンプレ）: `candidate`（要再調査）
- **approved / rejected / hold の判断は未実施**（律速＝人間承認）

## 未対応点 / 仮説
- まだ人間判断が1件も無いため履歴は空。最初の判断（candidate-001）が入った時点で運用が回り始める想定
- 判断者の表記粒度（氏名/役割/イニシャル）は未確定 → 仮に「役割（owner/pm 等）」で記す方針

## 次の一手
1. 人間が candidate-001 の承認/却下/保留を判断
2. 判断が出たら AI が本ログ先頭へ1行追記＋[[../90_templates/承認判断テンプレート]] で理由を別途記録
3. 月次で [[収益化ロードマップ]] と突合（[[../05_monetization 月次レビュー運用（Issue #11）]] と連携予定）

## 関連
- [[収益化シナリオ承認フロー]] / [[../90_templates/承認判断テンプレート]] / [[progress連携基準]]
- Issue: kaeru07/vault#8
