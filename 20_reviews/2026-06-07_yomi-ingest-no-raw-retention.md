---
date: 2026-06-07
task: /yomi 牌譜取り込みを「生牌譜非保管・処理後削除」へ改修
runId: 20260607-224848
targetApp: mahjong
monetizationImpact: low
theme: [app-strategy, workflow]
relatedRunIds: [20260607-190005, 20260607-212218]
commitHashes: [4993af4]
# reviewFileCommit:
---

# 2026-06-07 /yomi 取り込み 生牌譜非保管・処理後削除へ改修

> 1 作業 = 1 ファイル の自動生成レビュー。

---

## 1. 作業目的

- なぜ: 利用規約（牌譜再配布禁止）対応として、生牌譜を保管・公開せず**問題データのみ保存**し、処理後に元牌譜を削除する運用へ変更。
- 背景: [[2026-06-07_tenhou-paifu-collection-research]] で「生牌譜のGitHub保存＝再配布リスク」が判明。

---

## 2. 実施内容

- パイプライン: 牌譜取得 → 解析 → 候補生成 → **品質判定 → S/A採用 → yomi-questions.json保存 → 元牌譜削除**。
- `scripts/ingest-yomi.mjs`（新規）: 候補JSON入力→整合検証→品質判定（S/A採用・B保留・C/D隔離）→重複除外（id＋盤面signature）→正本マージ→採用後再検証→`--raw` の元牌譜削除→集計表示。`--dry-run` 対応。
- `scripts/lib/yomi-validate.mjs`（新規）: 検証を共有化。**`source.gameId/mjlog/xml/mjai/raw` 等の牌譜ポインタ保持をエラー化**。
- `scripts/yomi-stats.mjs`（新規）: `sourceRank/sourceType` 別集計（鳳凰卓/魂天/王座 問題数）。
- `types/yomi.ts`: `YomiSource` を `sourceType/sourceRank/importedAt` へ整理（gameId 等の牌譜ポインタ廃止）。
- `lib/yomi.ts`: `getYomiSourceStats`・出典ラベル追加。
- `.gitignore`: `data/rejected/**/*.json` 追加（**コミットは `data/yomi-questions.json` のみ**）。
- docs / CLAUDE.local.md / メモリ更新。旧「元牌譜削除禁止」と ingest の削除の矛盾を解消（ingest が取得した一時生牌譜の削除は正規動作、ユーザー唯一原本の独断削除は禁止）。

### 保持する / しない

- 保持: 問題 / 解説 / 読み筋 / 危険度 / カテゴリ / 出典種別（sourceType, sourceRank）
- 非保持: 生牌譜 / mjlog / xml / mjai 原本 / gameId 等の牌譜ポインタ

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/mahjong/scripts/ingest-yomi.mjs | 取り込みオーケストレータ（新規・元牌譜削除含む） |
| apps/mahjong/scripts/lib/yomi-validate.mjs | 共有検証（新規・牌譜ポインタ禁止） |
| apps/mahjong/scripts/yomi-stats.mjs | 出典別集計（新規） |
| apps/mahjong/scripts/validate-yomi.mjs | 共有モジュール利用へ |
| apps/mahjong/types/yomi.ts | YomiSource を sourceType/sourceRank/importedAt へ |
| apps/mahjong/lib/yomi.ts | getYomiSourceStats・ラベル追加 |
| apps/mahjong/.gitignore | data/rejected 配下も無視 |
| apps/mahjong/docs/yomi-ingestion.md | 生牌譜非保管・新フロー反映 |
| CLAUDE.local.md / memory project_yomi_ingestion | ルール改修反映 |

---

## 4. 検証結果

- ingest end-to-end: OK（/tmp合成候補5件→採用2(houou,ouza)/保留1/隔離2(整合エラー＋gameId保持禁止)・`--raw`削除確認・`--dry-run`で削除なし・正本10件不変）
- データ検証: OK（validate 全10問 PASS）
- 集計: OK（yomi-stats 動作・鳳凰卓/魂天/王座 を出力）
- typecheck: OK（tsc 0err）／ build: OK（next build 成功）
- 機密スキャン: OK ／ git status: 意図した変更のみ（生牌譜・中間・隔離は未staged）
- ob sync: Fully synced ／ git push: mahjong 4993af4（pushed）／ obsidian-vault 本セッションで mirror+push

---

## 5. 未対応

- mjlog → 候補JSON 変換アダプタ（解析・候補生成・dangerLevel/readingBasis 初期付与）は未実装。
- 実牌譜の取り込みは未実行（土台のみ）。

---

## 6. 危険ポイント

- 生牌譜の GitHub 保存・公開は禁止（再配布）。牌譜由来問題の公開可否は人間の法務判断（承認必須）。
- ingest の削除は `--raw` 指定かつ採用成功後のみ。`--dry-run` で安全確認可能。
- 既存10問・UI・「何切る」は無変更。

---

## 7. 次にやるべきこと

- 実牌譜提供時: houou-logs 取得 → 候補生成 → `ingest-yomi.mjs --raw` で採用＋元牌譜削除。
- mjlog→候補変換アダプタの実装。
- 採用が増えたら yomi-stats で出典/難易度の偏り監視。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: mahjong
作業: /yomi 取り込みを生牌譜非保管・処理後削除へ改修
runId: 20260607-224848
日付: 2026-06-07
GitHub commit: mahjong 4993af4

## 実施内容
- ingest-yomi.mjs(品質判定S/A採用→正本マージ→元牌譜削除, dry-run対応)
- 共有検証 yomi-validate.mjs(source.gameId等の牌譜ポインタ保持を禁止)
- yomi-stats.mjs(鳳凰卓/魂天/王座 集計)
- 型を sourceType/sourceRank へ、.gitignoreでコミットはyomi-questions.jsonのみ

## 検証
- ingest E2E(採用2/保留1/隔離2・raw削除・正本不変) / validate全10問PASS / tsc0err / build成功

## 確認したい観点
- 「生牌譜非保管・処理後削除」で再配布リスクを十分に下げられているか
- ingestの品質判定・重複除外・元牌譜削除の安全性(取りこぼし/誤削除)
- source.gameId等のポインタ禁止で出典追跡性とのトレードオフは妥当か
- 残課題(mjlog→候補変換)の設計上の注意点
````

---

## 関連

- progress runId: 20260607-224848（正本）
- 関連 run: 20260607-190005 / 20260607-212218
- 関連レビュー: [[2026-06-07_yomi-ingestion-rule]] / [[2026-06-07_tenhou-paifu-collection-research]]
- 関連アプリ: [[../02_apps/mahjong]]
