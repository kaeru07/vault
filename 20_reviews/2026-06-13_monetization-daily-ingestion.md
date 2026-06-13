---
date: 2026-06-13
task: 収益化候補の定期取り込み・調査元可視化（既存実装の調査＋日次調査フォルダ走査の拡張）
runId: 20260613-225744
targetApp: ny01/progress
monetizationImpact: medium
theme: [monetization, market-research, workflow]
relatedRunIds: []
commitHashes: [42692d4]
---

# 2026-06-13 収益化候補 定期取り込み・調査元可視化

> 依頼の趣旨「既存に近い機能は新規作成せず拡張する」に従い、事前調査→ギャップのみ拡張。

## 1. 作業目的
- Factory定時実行時にVaultの調査結果を取り込み、収益化候補一覧・詳細から根拠（調査元）を追えるようにする。

## 2. 事前調査の結論（重要）
依頼機能の**約9割が既存実装済み**だった:
- **データ構造**: `types/monetization.ts` に `sourceRefs` / `researchLogs`(summary/sourcePath/runId/impact) / `evidenceLinks` / `history`(before/after/reason/runId/timestamp) / `latestSignal` / `lastSyncRunId` / `ingestedAt` が既に存在。
- **定期同期**: `lib/monetization-vault-sync.ts` の `syncCandidatesFromVault()` が `runScheduledFactory` step0 で実行（新規→追加(Draft)/既存→sourceRefs・researchLogs・evidenceLinks追記/二重追加しない/Vaultは読み取りのみ/Epic化なし）。
- **重複判定**: `findExisting`（name/slug/targetApp）＋ `epicSlugs` ＋ `EXISTING_APP_SLUGS`。
- **ExecutionRun記録**: `addExecutionRun` で added/updated/scannedFiles を記録。
- **一覧表示**: `/monetization`（MonetizationList）に「📎調査元N / 出典なし」「最終調査 日付」。
- **詳細表示**: `/monetization/[id]` に 調査元一覧 / 根拠リンク / 調査履歴 / スコア変更・取り込み履歴。

## 3. 拡張した点（ギャップのみ）
- `lib/monetization-vault-sync.ts`:
  - 走査対象に `06_research/daily-market-research` / `daily-ai-news` / `daily-ai-tools` を追加（これらは `06_research` のサブフォルダで、`listMarkdown` が非再帰のため従来未走査だった）。
  - `sourceType()` を `daily` 優先順に並べ替え、日次調査を `daily` 種別の調査元に分類。
  - スキャン上限 300→400（全301ファイル走査可能に）。
- `docs/operations/current-operating-model.md`: 変更履歴に追記。

## 4. 変更ファイル
- lib/monetization-vault-sync.ts
- docs/operations/current-operating-model.md
（types / pages / API / Factory連携は既存実装を流用・新規作成なし）

## 5. 検証結果
- tsc 0 / build 成功。
- 手動同期2回（POST /api/monetization/sync）:
  - 1回目: `scannedFiles=302`（従来245→日次57ファイル追加を確認）、added 0 / updated 1（BirdLogに新調査元追記）/ skipped 11。
  - 2回目: added 0 / updated 0 / skipped 12 → **冪等・二重追加なし**を確認。
- `/monetization/[id]`（mc-birdlog-001）で 調査元一覧（43件）/ 調査履歴 / 根拠リンク の描画を確認。
- 安全ルール順守: 候補追加のみ・Epic化/公開/課金/deploy/secret非接触・Vault読み取りのみ・既存表示不変。

## 6. 未対応 / 注意
- 日次調査メモ（daily-*）は**候補名を明示参照**したときだけ調査元として自動付与される（既存の名前照合方式）。現状の daily ファイルは既存候補名と一致しなかったため daily 種別の sourceRef は付かなかった（走査機構は有効）。
- 候補名の表記ゆれ吸収（エイリアス）や、daily からの新規候補抽出ロジック強化は将来課題。
- `data/real/monetization-candidates.json` は実データのため commit 対象外（同期で BirdLog に sourceRef 追記済み）。

## 7. 次にやるべきこと
- 候補名エイリアス辞書で daily メモのヒット率を上げる。
- iPhone実機で /monetization・/monetization/[id] の調査元表示の崩れ確認。

## 8. ChatGPT レビュー依頼文
```
対象: progress 収益化候補 定期取り込み・調査元可視化（runId 20260613-225744 / commit 42692d4）
変更: lib/monetization-vault-sync.ts（走査対象に日次調査フォルダ追加）, docs/operations/current-operating-model.md
背景: 依頼機能の大半が既存実装済みだったため、新規作成せず未走査だった日次調査フォルダ(daily-market-research/ai-news/ai-tools)の走査のみ拡張。
観点:
1. 既存実装を流用し新規JSON/APIを作らない判断は妥当か。
2. 日次メモが候補名を明示参照しないと調査元が付かない名前照合方式の限界（エイリアス辞書の要否）。
3. スキャン上限400・毎定時(11/14/16/23)で302ファイル読み込みの性能影響。
4. daily種別分類（06_research配下だがdaily優先）の妥当性。
```
