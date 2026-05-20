---
title: research-run / idea-run cron + @reboot 自動実行設計
type: monetization-design
issue: kaeru07/vault#36
created: 2026-05-20
updated: 2026-05-20
status: proposal（設計のみ・実 cron 登録は人間承認後）
relatedIssues: [kaeru07/vault#25, kaeru07/vault#26, kaeru07/vault#28, kaeru07/vault#33, kaeru07/vault#34]
---

# research-run / idea-run cron + @reboot 自動実行設計

> Issue #36。VPS 放置でも情報収集 → 案生成が回る状態にする**自動実行設計**。
> **本ファイルは設計（Runbook）のみ。実 crontab 登録 / 実コマンド実装 / 実実行はしない**
> （VPS crontab 操作は人間判断・実装は別アプリリポジトリ）。
> [[案工場_完全自動化フロー]]（#28）の Step 1（収集）/ Step 2（生成）に対応する 2 コマンドの起動設計。

## 1. 概念整理（責務分離）

| コマンド | 責務 | 入力 | 出力 |
|---|---|---|---|
| `research-run` | 8 源（or Phase 1: 3 源）から 1 日分のトレンド取得 | cron トリガ | `06_research/daily/YYYY-MM-DD/*.ndjson` + `summary.md` |
| `idea-run` | daily 当日分から 30 案生成 + 5 件 candidate 化 + 承認待ち追記 | research-run 出力 | `05_monetization/idea_pool/YYYY-MM-DD.ndjson` + `scenarios/candidate-*.md` 起票 + `20_reviews/ChatGPT承認待ち.md` 追記 |
| `vloop` | 既存（ToDo 消化） | — | — |

> **vloop は別軸**（ChatGPT 起票 Issue の消化）。research-run / idea-run と並列運用。

## 2. cron 設計（Issue 本文準拠）

```cron
# === 毎日実行 ===
0  7 * * * research-run >> /var/log/research-run/YYYY-MM-DD.log 2>&1
10 7 * * * idea-run     >> /var/log/idea-run/YYYY-MM-DD.log 2>&1

# === VPS 再起動時 ===
@reboot sleep 60  && research-run >> /var/log/research-run/reboot-$(date +\%Y-\%m-\%d).log 2>&1
@reboot sleep 180 && idea-run     >> /var/log/idea-run/reboot-$(date +\%Y-\%m-\%d).log 2>&1
```

### 設計判断（理由つき）

| 項目 | 値 | 理由 |
|---|---|---|
| 起動時刻 | JST 07:00 / 07:10 | 早朝の方が外部 API レート余裕あり / 0:00 だと日付境界の不確定性が高い |
| 順序 | research → 10 分後 idea | research の出力に idea が依存。10 分は実装時の取得時間バッファ仮置き |
| @reboot 遅延 | 60 秒 / 180 秒 | ネットワーク / pm2 / DNS 初期化を待つ。idea-run は research-run 完了を待つため長め |
| 同時実行回避 | flock or PID ファイル | 前日実行が継続中なら新規起動を抑制（設計時メモ） |
| stdout/stderr | ログファイル保存 | 失敗時の追跡（[[#37 / #38 失敗時継続ルール]] と連動） |

## 3. コマンド仕様（実装は別アプリリポジトリ）

### research-run

```
research-run [--phase=1|2|3|4] [--source=trends,play,youtube,...] [--dry-run]
```

- デフォルト: Phase 1 全 3 源（[[../06_research/無料情報源_取得方法比較]] #29 の Phase 分類）
- `--dry-run`: 取得せずプラン表示
- 終了コード: 0=成功 / 1=一部失敗（部分継続あり） / 2=全失敗（停止条件）
- 出力先: `06_research/daily/<YYYY-MM-DD>/<source>.ndjson` + `summary.md`
- 機密スキャン: 保存前に [[../03_prompts/GitHub反映ルール]] §機密パターン 8 種をフィルタ

### idea-run

```
idea-run [--date=YYYY-MM-DD] [--limit=30] [--promote=5] [--dry-run]
```

- デフォルト: 当日 daily を入力、30 案生成、上位 5 件昇格
- `--date`: 過去日の再生成（リプレイ）
- 終了コード: 0=成功 / 1=部分成功 / 2=失敗
- 出力先: `05_monetization/idea_pool/<日付>.ndjson` + `scenarios/candidate-*.md` 起票 + `20_reviews/ChatGPT承認待ち.md` 追記
- 安全弁: [[案プール自動昇格ルール]]（#27）の除外ルールを必ず適用

## 4. ログ保存設計（[[#37 / #38 失敗時継続ルール]] と整合）

- ログ場所: `/var/log/research-run/` / `/var/log/idea-run/`
- 1 日 1 ファイル: `YYYY-MM-DD.log`（stdout+stderr 結合）
- リブート時は別ファイル: `reboot-YYYY-MM-DD.log`
- 保存期間: 90 日（cron で `find -mtime +90 -delete`）
- ログ内容: 開始時刻 / 取得源別件数 / 失敗源 / dedup 前後件数 / 昇格件数 / 終了コード

## 5. VPS 配置案（実装時の選択）

| 配置 | 長所 | 短所 |
|---|---|---|
| ny01 配下（progress 同居） | 既存 pm2 環境流用 / VPS 1 台で完結 | progress 安定性に影響しうる |
| 新規リポジトリ（scrape-lab-v2 拡張） | 責務分離 / progress 非影響 | 新規セットアップ手間 |
| **推奨**: scrape-lab-v2 拡張 | スクレイピング既存ロジック流用可 | — |

## 6. progress 連携（実行結果の可視化）

- 1 サイクル（research-run + idea-run）= 1 ExecutionRun として progress へ POST
- `targetApp: research-engine`
- `runStatus`: completed / partial / failed
- `nextActions`: 失敗時の手動復旧コマンド

## 7. 機密・規約ガード

- API キー（YouTube 等）は `.env` + `.gitignore`。コミット禁止
- スクレイピング規約は実装着手時に各源 1 つずつ最新版を再確認
- VPS の crontab 内容 / SSH 鍵は Vault に書かない（VPS 側で完結）

## 8. 完了条件と現状

| Issue #36 完了条件 | 現状 | 達成手段 |
|---|---|---|
| VPS 起動時に自動実行 | **未実施**（@reboot 未登録） | 本設計に従い VPS 側で `crontab -e` 登録（人間操作） |
| 毎日自動実行 | **未実施**（cron 未登録） | 同上 |
| daily 更新 | **未実施**（research-run 未実装） | 別リポジトリで実装 |
| idea_pool 更新 | **未実施**（idea-run 未実装） | 同上 |
| commit/push 済 | **本設計のみ commit 済** | — |

> 「実稼働」は本 Issue のスコープ外（VPS crontab 操作 + 別アプリ実装）。本設計で**設計責務は完了**。

## 9. 実装着手の順序（推奨）

1. scrape-lab-v2 拡張 or ny01 配下に `research-engine/` 作成（人間承認後）
2. research-run（Phase 1 = Reddit / iTunes / RSS の 3 源）から実装着手
3. 1 日分手動実行で挙動観察（[[../06_research/2026-05-20_1日分情報収集テスト計画]] #32 準拠）
4. idea-run 実装 + 既存 candidate-001〜004 との重複除去ロジック検証
5. ログ保存設計（#37/#38）と実行ログ一覧（#39）を実装
6. 安定運用 1 週間後に crontab 登録（VPS 側 / 人間操作）
7. @reboot は最後（再起動頻度が低いため動作確認はリブート時のみ）

## 未対応点 / 仮説

- 起動時刻 07:00 は仮置き（実装時に外部 API のレート余裕時間帯を再確認）
- @reboot 遅延 60s / 180s も仮置き（実 VPS 起動時間で実測すべき）
- 同時実行回避（flock / PID）は実装時に選択
- 配置は scrape-lab-v2 拡張を**推奨**だが、最終判断は実装者と人間

## 次の一手

1. 本設計をユーザー / ChatGPT がレビュー → 実装着手の方向性承認
2. 承認後、scrape-lab-v2 拡張 or 新規 `research-engine/` で research-run から実装
3. 1 日分手動実行 → 観察 → idea-run 実装 → 安定 1 週間 → cron 登録（VPS 側）

## 関連

- [[案工場_完全自動化フロー]]（#28 全体 Runbook）
- [[../06_research/トレンド収集エンジン設計]]（#25）
- [[../06_research/無料情報源_取得方法比較]]（#29 Phase 分類）
- [[../06_research/daily/README]]（#30 保存規約）
- [[../06_research/2026-05-20_1日分情報収集テスト計画]]（#32）
- [[idea_pool/_README]]（#26）
- [[案プール自動昇格ルール]]（#27）
- [[../03_prompts/GitHub反映ルール]]
- Issue: kaeru07/vault#36（関連 #33 / #34 / #35 / #37 / #38 / #39）
