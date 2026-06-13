---
date: 2026-06-13
task: Factory定時自動実行に16:00 JSTを追加（11/14/16/23）
runId: 20260613-153617
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: []
commitHashes: [1eb1ddf]
---

# 2026-06-13 Factory定時自動実行に16:00 JSTを追加

## 1. 作業目的
- ユーザー依頼「16時にも自動実行を追加して」。Factoryの定時自動実行に 16:00 JST を追加する。

## 2. 実施内容
- `/etc/systemd/system/factory-schedule.timer` の OnCalendar に `*-*-* 16:00:00` を1行追加（従来 11:00 / 14:00 / 23:00 → 11:00 / 14:00 / 16:00 / 23:00）。
- `systemctl daemon-reload` + `systemctl restart factory-schedule.timer`。`list-timers` で次回 Sat 2026-06-13 16:00:00 JST 発火を確認（timer active+enabled）。
- UIの「定時」表示が読む repo 正本 `docs/factory-schedule/factory-schedule.timer` も 11/14/16/23 へ同期（従来 docs は 11/23 のみで 14 も欠落していたため合わせて補正）。
- `docs/factory-schedule/README.md` の定時表記と `docs/operations/current-operating-model.md` 変更履歴を更新。

## 3. 変更ファイル
| ファイル | 変更 |
|---|---|
| /etc/systemd/system/factory-schedule.timer | OnCalendar=16:00追加（system設定・repo外）daemon-reload+restart |
| docs/factory-schedule/factory-schedule.timer | repo正本を11/14/16/23へ同期（UI定時表示の読み元） |
| docs/factory-schedule/README.md | 定時表記更新 |
| docs/operations/current-operating-model.md | 変更履歴に16:00追加 |

## 4. 検証結果
- systemctl list-timers: 次回 16:00 発火・timer active+enabled を確認。
- UI定時表示の抽出（正規表現）: 11:00 / 14:00 / 16:00 / 23:00。
- build/tsc/lint: n/a（timer/docsのみ・アプリコード変更なし）。
- 機密スキャン: OK。

## 5. 未対応
- 16:00 発火後の `journalctl -u factory-schedule.service` 実行ログ確認（発火後に確認）。

## 6. 危険ポイント
- 各定時は `runScheduledFactory` の安全ゲート（factoryEnabled / Blocked / 二重起動lock）を通った場合のみ起動。無条件実行ではない。
- /etc 配下の systemd unit は repo 管理外。repo の docs コピーは UI 表示・履歴用の同期。

## 7. 次にやるべきこと
- 16:00 実行後に司令塔の定時表示・envelope ExecutionRun を確認。

## 8. ChatGPT レビュー依頼文
```
対象: progress Factory定時自動実行に16:00 JST追加（runId 20260613-153617 / commit 1eb1ddf）
変更: /etc/systemd/system/factory-schedule.timer(OnCalendar 16:00追加), docs/factory-schedule/{factory-schedule.timer,README.md}, docs/operations/current-operating-model.md
観点: 1.定時を11/14/16/23の4回にする頻度は妥当か(過剰起動・Run増加リスク) 2.安全ゲート経由で無条件起動でない点の確認 3.repo docs timerと/etc実体の二重管理運用の妥当性。
```
