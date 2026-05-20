---
title: research-run / idea-run ログ運用と失敗時継続ルール
type: monetization-research-spec
issue: kaeru07/vault#37
relatedIssues: [kaeru07/vault#38, kaeru07/vault#36, kaeru07/vault#39]
created: 2026-05-20
updated: 2026-05-20
status: active（ルール固定）
---

# research-run / idea-run ログ運用と失敗時継続ルール

> Issue #37（および完全重複の Issue #38）。research-run の一部源が失敗しても全体停止せず**成功した源だけで idea-run へ進める**ためのルールとログ形式。
> [[../../05_monetization/cron_research-run_idea-run設計]]（#36）と一体運用。

## 1. 失敗時継続ルール（厳守）

| 状況 | 動作 |
|---|---|
| 1 源失敗 | **継続**。他源は正常取得。失敗源はログに記録し次回再試行 |
| 一部源（過半数未満）失敗 | **継続**。成功源で idea-run へ |
| 過半数失敗（4 / 8 源以上失敗） | **継続**（警告）。idea-run へは進むが progress に警告 POST |
| **全源失敗** | **停止**（idea-run はスキップ）。失敗ログ + progress に failed POST |
| 機密混入検出（保存前スキャンで陽性） | **当該源を停止**（全体は継続）。混入内容は記録せずパターン名のみログ |
| 規約抵触兆候（HTTP 403 / robots.txt 変更） | **当該源を停止**（全体は継続）。人間判断待ち |

> 「過半数失敗」のしきい値 4 / 8 は仮置き。Phase 1（3 源）運用時は 2 / 3 で同等扱い。

## 2. 再試行ルール

- 失敗源は**次回 cron（翌日）で自動再試行**（即時リトライしない）
- 3 日連続失敗 → 当該源を**自動的に skip リストへ追加**（次回以降取得試みなし）
- skip リスト復帰は人間判断（規約変更や対策実装後）

## 3. ログ保存先と形式

### 場所
- `06_research/logs/research-run-log.md`（1 サイクル 1 行追記）
- `06_research/daily/YYYY-MM-DD_status.md`（当日サマリー・1 ファイル / 日）
- `/var/log/research-run/YYYY-MM-DD.log`（VPS 側 stdout/stderr）

### research-run-log.md フォーマット（1 行 = 1 サイクル）

```markdown
| 日付 | サイクル | 全件 | 成功 | 失敗 | 失敗源 | 終了コード | idea-run 進行 | 備考 |
|---|---|---|---|---|---|---|---|---|
| 2026-05-20 | daily-07:00 | 8 | 7 | 1 | x-trend | 1 | 進行 | レート超過 |
| 2026-05-20 | reboot     | 3 | 3 | 0 | — | 0 | 進行 | Phase 1 のみ |
| 2026-05-21 | daily-07:00 | 8 | 0 | 8 | (全源) | 2 | 停止 | ネットワーク断・人間確認 |
```

### YYYY-MM-DD_status.md フォーマット（1 日サマリー）

```markdown
---
date: YYYY-MM-DD
research_run:
  attempted: 8
  succeeded: 7
  failed: 1
  failed_sources: [x-trend]
  exitCode: 1
idea_run:
  ran: true
  inputItems: 152
  ideas: 30
  promoted: 5
  exitCode: 0
---

# YYYY-MM-DD status

## 失敗源詳細
- x-trend: HTTP 429（レート超過）/ 次回再試行対象

## 異常検知
- 規約抵触兆候: なし
- 機密混入: なし
- 過半数失敗: なし

## progress POST
- runId: 20260520-070500（targetApp: research-engine / runStatus: partial）
```

## 4. 機密ガード（ログ書き込み前必須）

- 失敗理由本文に**生データを含めない**（API レスポンス Body をそのまま貼らない）
- 含めて良い: HTTP ステータス / エラーカテゴリ名 / レート関連メトリクス
- 含めない: トークン / Cookie / 認証ヘッダ / 個人情報

機密パターン（[[../../03_prompts/GitHub反映ルール]] §機密スキャン）を保存前に必ず通す。

## 5. progress 連携

- research-run / idea-run の 1 サイクル = 1 ExecutionRun として progress へ POST
- `runStatus`:
  - 全成功 → `completed`
  - 1 源以上失敗（過半数未満）→ `partial`
  - 過半数以上失敗 or 全失敗 → `failed`
- `nextActions`: 失敗源の手動復旧コマンドを記載

## 6. 完了条件と現状（Issue #37 / #38）

| 完了条件 | 現状 | 達成手段 |
|---|---|---|
| 失敗時の継続ルールが明記 | **完了**（§1 / §2） | 本ファイル |
| 成功/失敗ログ形式が決まっている | **完了**（§3） | 本ファイル + サンプル |
| 全失敗時だけ停止するルール | **完了**（§1） | 本ファイル §1 |
| commit/push 済 | 本ファイル + サンプル | — |

4 つすべて充足（ルール定義 Issue のため設計のみで完了）。

## 7. Issue #37 / #38 の重複扱い

- 本 Issue #37 と Issue #38 はタイトル・本文が**完全に同じ**（ChatGPT 起票時の重複と推定）
- 本ファイルで両方の完了条件を満たす
- クローズは**ペアで判断**（#37 = #38）

## 関連

- [[../../05_monetization/cron_research-run_idea-run設計]]（#36 cron 設計）
- [[research-run-log]]（実ログファイル）
- [[../daily/README]]（#30 保存規約）
- [[../../03_prompts/GitHub反映ルール]]
- Issue: kaeru07/vault#37 / #38（重複）/ 関連 #36 / #39
