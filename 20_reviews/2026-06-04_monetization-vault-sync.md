---
date: 2026-06-04
task: 収益化候補の定期取り込み（Vault→Hub）と調査元可視化（既存 /monetization 拡張）
runId: 20260604-122721
targetApp: progress
monetizationImpact: high
theme: [workflow, monetization, obsidian, app-strategy]
relatedRunIds: [20260603-144109, 20260604-013554]
commitHashes: []
---

# 収益化候補 定期取り込み・調査元可視化（2026-06-04）

## 1. 作業目的
Factory定期実行（11:00/23:00/boot）時に Vault の調査結果を確認し、未反映の収益化候補を Monetization Hub へ取り込む。各候補の調査元・根拠・調査履歴を Progress 上で追えるようにする。

## 2. 事前調査（拡張で対応）
- 定期実行時の候補追加処理 → **未存在**（新規追加が必要・二重ではない）。
- sourceRefs 様構造 → **未定義**（researchLogs/history/links はあり）。
- Hubデータ構造 = `types/monetization.ts`。Factory定期実行 = `runScheduledFactory`（ON/OFF・Blocked・lock の skip 分岐）。ExecutionRun = runId/source/trigger/changedFiles/summary。ob sync後 Vault = `/root/company/obsidian-sync-vault`。
- → 新規ページ/二重JSON/二重APIを作らず、**既存 Hub と monetization-candidates.json（正本）を拡張**。同期専用 API を1本だけ追加。

## 3. 追加/変更した処理
- 型拡張: `SourceRef` / `EvidenceLink`、`ResearchLog`(summary/sourcePath/runId/impact)・`HistoryEntry`(before/after/reason/runId/timestamp)、候補に sourceRefs/evidenceLinks/latestSignal/lastSyncRunId/ingestedAt。
- `lib/monetization-vault-sync.ts`（新規）: Vault の候補表(`05_monetization/収益化候補一覧.md`)・`06_research`・`20_reviews` を走査 → 重複判定 → 新規追加 or 調査元追記 → ExecutionRun 記録。**Vault は読み取りのみ**。
- `lib/factory-schedule.ts`: `runScheduledFactory` 先頭に best-effort 同期ステップ（Factory ON/OFF 問わず毎回）。
- `app/api/monetization/sync/route.ts`（新規）: POST 同期（定期実行ステップ/手動）。
- 表示: `/monetization` カードに調査元数バッジ・取込日、`/monetization/[id]` に調査元一覧/根拠リンク/調査履歴拡張/変更・取込履歴。

## 4. 重複判定
candidate id / slug / title / targetApp / 類似名(包含) / 既存Epic(slug) / 既存アプリ(EXISTING_APP_SLUGS) / 既存candidate(Rejected含む)。重複時は新規追加せず researchLogs/sourceRefs/evidenceLinks/history に追記し「どの調査元に再登場したか」を記録。

## 5. 安全ルール遵守
候補追加のみ・Epic化しない・epics.json不変・Factory実装対象にしない・public/billing/deploy/secret/production_db 非接触・既存候補削除なし・Vault書き換えなし・二重追加なし（冪等確認）。

## 6. 検証結果
- 同期: added 6 / updated 6 / skip 0 → 2回目 added 0 / updated 0 / skip 12（冪等・二重追加なし）。
- 既存 birdlog に sourceRefs 6件追記、ExecutionRun(source=monetization_sync)に件数記録。
- 過剰一致（初回 "vault" 語が79件マッチ）を英語フレーズ条件（空白あり or 6文字以上）で解消し最大6件に正常化。
- `/monetization` 調査元バッジ・`/monetization/[id]` 調査元一覧セクション表示・detail 200。
- typecheck OK / lint OK / build OK / pm2 deploy。

## 7. 未対応 / 次の一手
- daily-market-research（news-app 側）の走査対象化は未（Vault配下のみ走査）。
- 抽出は候補表の太字セル依存のため、表記ゆれ・新フォーマットには調整が要る場合あり。
- 次回 11:00/23:00/boot で sync が自動実行され候補が反映されるか観察。

## 8. ChatGPTレビュー依頼文
```
対象: progress / 収益化候補 定期取り込み・調査元可視化 / runId 20260604-122721
Factory定期実行時にVaultを走査して未反映の収益化候補をHub(monetization-candidates.json=正本)へ取り込み、既存候補には調査元/履歴だけ追記する機能を、既存 /monetization を拡張して実装しました（新規ページ/二重JSON/二重APIなし）。Vaultは読み取りのみ・Epic化や公開/課金には触れません。
確認したい観点:
1. VaultとHubを相互上書きしない設計（Vault読み取り専用）になっているか。
2. 重複判定（id/slug/title/targetApp/類似名/既存Epic/アプリ/candidate）の十分性と二重追加防止（冪等）。
3. 調査元抽出の精度（候補表の太字セル依存・英語フレーズ照合）に穴はないか。
4. 安全ルール（候補追加のみ・epics.json不変・public/billing/deploy/secret非接触）が守られているか。
5. /monetization と詳細の調査元表示の使い勝手（iPhone）。
```
