---
date: 2026-06-08
task: /yomi 候補品質の自動採点（B→A判定）を実装・試験
runId: 20260608-004420
targetApp: mahjong
monetizationImpact: low
theme: [app-strategy, workflow]
relatedRunIds: [20260607-234517, 20260607-224848]
commitHashes: [5fc9fe5]
# reviewFileCommit:
---

# 2026-06-08 /yomi 候補品質 自動採点（B→A判定）

> 1 作業 = 1 ファイル の自動生成レビュー。

---

## 1. 作業目的

- 天鳳牌譜から生成した候補が全件 B 止まりだった問題を解消する。
- 読み要素をスコア化し B→A を判定。「なぜ A 判定か」を出力する。

---

## 2. 実施内容

- `scripts/lib/yomi-score.mjs`（新規）: 和了者の河＋盤面から読み要素を検出し加点 → S/A/B/C 判定。
  - 配点: スジ引っ掛け+3 / 無スジ+2 / 字牌処理+2 / 対子落とし+2 / リャンメン落とし+2 / 染め手気配+2 / 壁+1 / ワンチャンス+1 / 現物+1 / リーチ宣言牌+1。
  - 判定: コア読み要素（現物/リーチ以外）が無ければ C。`score>=7→S / >=5→A / >=3→B / else C`。
  - `reasons` 配列で「なぜA」を返し、`applyScore` で `qualityRank/dangerLevel/readingBasis/explanation` に反映。
- `scripts/yomi-quality-score.mjs`（新規）: 候補数 / S・A・B・C / A判定サンプル / 不採用理由上位 をレポート（`--out` で採点反映候補を書き出し）。
- `scripts/tenhou-to-yomi.mjs`: 変換時に `applyScore` を適用（B 止まり → 自動採点へ）。
- `docs/yomi-ingestion.md`: 品質採点（配点・閾値・注意）を追記。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/mahjong/scripts/lib/yomi-score.mjs | 読み要素スコアリング＋S/A/B/C判定＋判定理由（新規） |
| apps/mahjong/scripts/yomi-quality-score.mjs | 採点レポートCLI（新規） |
| apps/mahjong/scripts/tenhou-to-yomi.mjs | 変換時に採点適用 |
| apps/mahjong/docs/yomi-ingestion.md | 品質採点を追記 |

---

## 4. 検証結果（試験レポート）

実データ（公開取得 `0/log/?{ref}`）で鳳凰卓4鳳半荘3戦 → 12候補を採点。

- **候補数: 12**
- **S: 3 / A: 5 / B: 4 / C: 0**（採用相当 S/A = 8、不採用 B/C = 4）
- **A判定サンプル3件**:
  1. `t-1flrdx4` ron=7筒 turn10 score6 — なぜA: 無スジ+2, ワンチャンス+1, 現物+1, 染め手気配+2
  2. `t-1flrdx9` ron=中 turn12 score5 — なぜA: 字牌処理+2, 壁+1, ワンチャンス+1, 現物+1
  3. `t-goiyl8` ron=3筒 turn10 score6 — なぜA: 無スジ+2, ワンチャンス+1, 現物+1, リャンメン落とし+2
- **不採用理由 上位**: 4件「コア要素はあるが加点不足（読み要素が単発）」
- graded候補 validate 全PASS / 正本10問 PASS / tsc 0err / next build 成功 / 機密スキャンOK。
- 生牌譜は処理後削除。**canonical（yomi-questions.json）は未変更**。
- ob sync: Fully synced / push: mahjong 5fc9fe5（pushed）/ obsidian-vault 本セッションで mirror+push。

---

## 5. 未対応

- 自動 S/A を canonical へ反映していない（人手レビュー前提）。
- 配点・閾値は v1。`無スジ` と `染め手気配` の重複加点補正が未対応。
- 待ち形（リャンメン/カンチャン等）の推定は未実装（waits は実ロン牌のみ）。

---

## 6. 危険ポイント

- 自動 S/A をそのまま採用すると誤判定問題を注入するリスク → 現状は canonical 未反映で安全側。人手レビューを挟む。
- 生牌譜・gameId は非保持/非コミット。牌譜由来問題の公開可否は人間の法務判断（承認必須）。

---

## 7. 次にやるべきこと

- A判定サンプルを人手レビューし `yomi-questions.json` へ少数採用する実証。
- 重複加点（無スジ×染め手）の補正と閾値チューニング。
- 待ち形推定で説明精度を上げる。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: mahjong
作業: /yomi 候補品質の自動採点(B→A判定)
runId: 20260608-004420
日付: 2026-06-08
GitHub commit: mahjong 5fc9fe5

## 実施内容
- 読み要素スコアリング(無スジ+2/スジ引っ掛け+3/字牌+2/対子落とし+2/リャンメン落とし+2/染め手+2/壁+1/ワンチャンス+1/現物+1/リーチ+1)でS/A/B/C判定+なぜA出力
- 実データ3戦12候補: S3/A5/B4/C0。canonicalは未反映(人手レビュー前提)

## 確認したい観点
- 配点・閾値(S>=7/A>=5/B>=3)は妥当か。A率(8/12)は高すぎないか
- 無スジと染め手気配の重複加点を補正すべきか
- 検出ロジック(無スジ/スジ引っ掛け/対子落とし/リャンメン落とし/染め手)に誤検出リスクはないか
- 自動S/Aをcanonicalへ採用する前提条件(人手レビュー範囲)はどうすべきか
````

---

## 関連

- progress runId: 20260608-004420（正本）
- 関連 run: 20260607-234517 / 20260607-224848
- 関連レビュー: [[2026-06-07_yomi-tenhou-ingest-trial]] / [[2026-06-07_yomi-ingest-no-raw-retention]]
- 関連アプリ: [[../02_apps/mahjong]]
