---
title: /vloop 実行一括サマリー置き場
type: prompt-ops
created: 2026-05-18
---

# /vloop 実行一括サマリー置き場

`/vloop` がループ終了時（正常終了・停止条件いずれも）に**必須で残す**一括サマリーの保存先。

- ファイル名: `vloop_YYYY-MM-DD_HHMM.md`
- 必須項目: 実行日時 / 実行件数 / 完了 Issue / 各 commit hash / 停止理由 / 次に残った ToDo
- 代替保存先: `20_reviews/_vloop-summary_YYYY-MM-DD_HHMM.md`（どちらか1ファイル）
- サマリーは commit / push する（[[../../GitHub反映ルール]] 準拠）

詳細仕様は [[../vloop]] の「Step 11: 一括サマリーを残す」を参照。
