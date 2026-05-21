---
title: research-run / idea-run 実行ログ 一覧（最新状況）
type: monetization-research-log
issue: kaeru07/vault#39
relatedIssues: [kaeru07/vault#36, kaeru07/vault#37, kaeru07/vault#38]
created: 2026-05-20
updated: 2026-05-20
status: active（実行のたびに更新）
---

# research-run / idea-run 実行ログ 一覧

> Issue #39。自動化が動いたか・失敗したかを**後から 1 ページで確認できる**ためのインデックス。
> 詳細ログ: [[research-run-log]]（時系列テーブル） / [[../daily/YYYY-MM-DD_status]]（日別詳細）。
> 本ファイルは**最新状況のクイックビュー**。

## 最新状況（直近の状態）

| 項目 | 値 |
|---|---|
| 最終 research-run | 2026-05-21 08:32 JST（manual-mvp / Issue #44） |
| 最終 idea-run | 2026-05-21 12:09 JST（manual-extend / 30 案・上位 5 件抽出 / Issue #45） |
| 最終成功日 | 2026-05-21 |
| 連続失敗源 | なし |
| skip リスト | — |
| 累計 ExecutionRun | 2（MVP + 拡張）|

> Issue #44 MVP 1 サイクル（ai-news 1 源・15 件 → 10 案 → 上位 3 件）+ Issue #45 拡張（30 案・上位 5 件抽出）まで手動実行。candidate 化は安全弁発動で保留（根拠 = 2）。詳細証跡: [[../daily/2026-05-21_実運転証跡]]。research-run / idea-run コマンド本体は未実装（[[../../05_monetization/cron_research-run_idea-run設計]] #36 の実装・cron 登録待ち）。

## 直近 7 日サマリー（実装後に自動更新）

| 日付 | research-run | idea-run | 取得件数 | 生成案 | 昇格件数 | 処理時間 | 備考 |
|---|---|---|---|---|---|---|---|
| (未実行) | — | — | — | — | — | — | research-run / idea-run 未実装 |

> 実装後は毎日 1 行追加。古い行は 30 日経過で `archive/` へ移動（[[README]] §3 ローテ準拠）。

## 必須記録項目（Issue #39 完了条件）

| 項目 | 記録場所 |
|---|---|
| 実行日時 | research-run-log.md の「日付」列 + 各 status.md frontmatter |
| 成功 / 失敗 | research-run-log.md の「成功 / 失敗」列 |
| 取得件数 | research-run-log.md の「全件」列 + status.md `inputItems` |
| 生成案数 | status.md `idea_run.ideas` |
| 処理時間 | status.md に `duration_ms` を追加（実装時） |
| エラー内容 | status.md「失敗源詳細」セクション（機密ガード適用） |

> 「処理時間」は [[research-run-log]] テーブルに列を追加するか、status.md 側に持たせるかは実装時判断（仮置き: status.md 側）。

## 異常検知ハイライト（実装時に自動表示）

実装時に本セクションは「過去 7 日間の異常」を列挙する想定:

- 連続失敗源（3 日以上）
- 過半数失敗の日
- 機密混入検出があった日
- skip リストへ追加された源

(現状は実行ゼロのため空)

## progress 連携

- 1 サイクル = 1 ExecutionRun（[[README]] §5）
- progress で `targetApp: research-engine` を検索すれば全履歴が取れる
- 本ファイルは Vault 内のクイックビュー（progress 不調時のバックアップ参照）

## 関連

- [[README]]（ログ運用ルール / 失敗時継続）
- [[research-run-log]]（実履歴テーブル）
- [[../../05_monetization/cron_research-run_idea-run設計]]（#36）
- Issue: kaeru07/vault#39（関連 #36 / #37 / #38）
