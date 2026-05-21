---
title: 手動実行 → cron 自動化 移行判定基準
type: monetization-design
issue: kaeru07/vault#47
created: 2026-05-21
updated: 2026-05-21
status: active（基準定義 / cron 投入判断のチェックリスト）
relatedIssues: [kaeru07/vault#36, kaeru07/vault#44, kaeru07/vault#45, kaeru07/vault#46]
---

# 手動実行 → cron 自動化 移行判定基準

> Issue #47。手動テストが安定した後に**安全に cron 自動化へ切り替える**判定基準。
> Issue #36 cron 設計 / Issue #46 初回手動実行 の後段にあたる。
> 判定主体は **人間**（vloop で勝手に cron 登録しない）。本ファイルは判定のチェックリストを提供。

## 1. 移行判定 7 項目（全項目 PASS で移行可）

| # | 項目 | 閾値 / 判定基準 | 確認場所 |
|---|---|---|---|
| 1 | daily 件数 | 1 サイクルあたり **20 件以上** 取得（Phase 1 / 3 源時の最低保証） | `06_research/daily/<日付>/*.ndjson` 行数合計 |
| 2 | 30 案件数 | idea-run 出力が **30 案** に達している | `05_monetization/idea_pool/<日付>.ndjson` 行数 |
| 3 | candidate 件数 | 上位 5 件抽出 + **少なくとも 1 件が candidate 起票**（または明示的に「全件保留」が安全弁の正しい動作と確認できている） | `05_monetization/scenarios/candidate-*.md` 新規分 or 上位 5 件すべてが安全弁発動の場合の判断記録 |
| 4 | エラー許容 | 全失敗ゼロ + 1 源失敗は 1 サイクル内 **最大 1 回**まで許容 | `06_research/logs/research-run-log.md` 失敗源列 |
| 5 | 3 日連続成功 | **連続 3 日間** 上記 1〜4 が PASS | `06_research/logs/research-run-log.md` 直近 3 行 |
| 6 | 機密混入ゼロ | 3 日間で機密パターン検出 **ゼロ**（[[../03_prompts/GitHub反映ルール]] 機密スキャン準拠） | `2026-MM-DD_status.md` 「機密混入: なし」3 日連続 |
| 7 | 規約抵触兆候ゼロ | 3 日間で HTTP 403 / robots.txt 変更 / 取得 0 件 が**ゼロ** | `2026-MM-DD_status.md` 異常検知セクション |

> 全 7 項目 PASS = **cron 投入可**。1 項目でも FAIL = 投入見送り（FAIL 項目を解消して再評価）。

## 2. 件数閾値の根拠

- **daily 20 件 / サイクル**: Phase 1 推奨 3 源（Reddit / iTunes Search / RSS 集約）で各源 5〜10 件取れれば余裕で達する基準
- **30 案**: Issue #26 で固定された生成件数
- **candidate 1 件以上 or 安全弁明示**: 「常に全件保留」が続く場合は 30 案を作る意味が薄くなる → 安全弁を見直すサイン（運用悪化検知）
- **エラー許容 1/サイクル**: 単発の規約変更や瞬間障害を吸収しつつ、慢性化を検知できる粒度
- **3 日連続**: 単発のラッキー成功を排除するための最小サンプル

## 3. 自動化移行プロセス（順序厳守）

1. **手動 3 日連続実行**（Phase 1 完全化 = Reddit + iTunes + RSS の 3 源稼働後）
2. 各日 §1 7 項目チェックを実施
3. 3 日連続で全項目 PASS → **人間が判定**
4. PASS なら以下の順序で cron 登録（VPS 側人間操作）:
   - a. crontab に `0 7 * * * research-run` のみ登録（idea-run は手動継続・段階導入）
   - b. 7 日観察 → 問題なければ `10 7 * * * idea-run` 追加
   - c. さらに 7 日観察 → 問題なければ `@reboot` 行を追加
5. 各段階で `06_research/logs/research-run-log.md` に「cron 段階導入: <段階>」を 1 行記録

## 4. 移行後の監視（自動化稼働中）

cron 投入後は以下を**毎日確認**（人間または progress 通知）:

- progress に `targetApp: research-engine` の ExecutionRun が**毎日 1〜2 件**出ているか
- `runStatus: failed` が**連続 2 日以上**続いていないか
- `06_research/logs/research-run-log.md` に異常エントリが入っていないか

異常時の対応:

| 異常 | 対応 |
|---|---|
| 1 日 ExecutionRun ゼロ | cron 起動失敗 / pm2 起動失敗の可能性 → VPS 側調査 |
| 連続 2 日 failed | スクレイピング失敗連続 → 規約変更チェック / 該当源を skip リスト |
| 機密混入検出 | **即時 cron 停止**（crontab で行をコメントアウト）→ 機密スキャナ強化後に再開 |
| candidate が 30 日連続でゼロ | 安全弁が常時発動 = 取得源の質低下 → 取得源見直し |

## 5. ロールバック手順（移行失敗時）

cron 投入後に問題が出た場合:

1. `crontab -e` で該当行をコメントアウト（即時停止）
2. 直近のログを `06_research/logs/research-run-log.md` で確認
3. 失敗原因を `06_research/logs/<日付>_failure.md` に記録
4. 修正 → 手動 3 日テストから再スタート（§1 〜 §3）
5. ロールバック判断を [[decision-log]] に 1 行記録

## 6. 完了条件と現状（Issue #47）

| 完了条件 | 現状 | 達成手段 |
|---|---|---|
| 自動化移行基準が明文化 | **完了**（§1 7 項目 + §2 根拠） | 本ファイル |
| cron 投入判断ができる | **完了**（§3 5 ステップ移行プロセス） | 本ファイル |
| commit/push 済 | 本ファイル commit/push 済 | — |

3 つすべて充足。

## 7. 適用タイミング

- 現状（2026-05-21）: Phase 1 完全化（3 源稼働）未達 → **本基準の適用フェーズに未到達**
- 順序: Issue #46 手動 1 サイクル ✅ → 3 源化 → 3 日連続手動成功 → §3 で cron 移行判断 → Phase 2（idea-run 自動化） → @reboot 追加

## 関連

- [[cron_research-run_idea-run設計]]（#36 cron 設計）
- [[../06_research/2026-05-20_1日分情報収集テスト計画]]（#32）
- [[../06_research/daily/2026-05-21_実運転証跡]]（#45 初回証跡）
- [[../06_research/logs/README]]（#37 失敗時継続）
- [[decision-log]]
- Issue: kaeru07/vault#47（関連 #36 / #46）
