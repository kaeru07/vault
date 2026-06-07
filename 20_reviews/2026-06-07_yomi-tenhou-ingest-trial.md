---
date: 2026-06-07
task: 天鳳公開牌譜URLを少数取得し /yomi 取り込み試験
runId: 20260607-234517
targetApp: mahjong
monetizationImpact: low
theme: [market-research, app-strategy, workflow]
relatedRunIds: [20260607-212218, 20260607-224848]
commitHashes: [7639773]
# reviewFileCommit:
---

# 2026-06-07 天鳳公開牌譜 取り込み試験

> 1 作業 = 1 ファイル の自動生成レビュー。

---

## 1. 作業目的

- ユーザーが手動でURLを探さなくても、公開天鳳牌譜URLを少数取得し `/yomi` の当たり牌読み問題生成が動くか確認する。
- 制約: ログイン/Cookie/認証突破/大量クロール/生牌譜GitHub保存/外部課金 すべて禁止。公開URLのみ・1〜3件・生牌譜は一時利用後削除・保存は問題データのみ。

---

## 2. 実施内容

- 公開アーカイブ `tenhou.net/sc/raw/`（list.cgi）から鳳凰卓1時間別 `scc{YYYYMMDDHH}.html.gz` を特定。
- `scc2026060712.html.gz`（公開ページ）から **四鳳南喰赤（4人鳳凰半荘）の `?log=` URL を3件**確認。
- 取得経路の実証:
  - `find.cgi?log=` → `ERROR`（非会員不可）。**認証突破はしない。**
  - **`tenhou.net/0/log/?{ref}` が生 mjlog XML を返す（認証/Cookie不要）** → これを採用。1件のみ一時取得（13KB）。
- `scripts/tenhou-to-yomi.mjs`（新規）で解析 → ロン局 5件抽出（turn≥10・4人河）→ 候補化。
- `scripts/ingest-yomi.mjs` 投入 → **採用0 / 保留(B)5**（自動生成は読み筋未検証＝B、`S/Aのみ採用`で不採用＝設計通り）。
- `--raw` で **生牌譜削除を確認**。正本 `yomi-questions.json` は10問のまま不変。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/mahjong/scripts/tenhou-to-yomi.mjs | mjlog→/yomi候補変換器（新規・牌譜ポインタ非保持・B保留） |
| apps/mahjong/docs/tenhou-collection.md | 公開取得エンドポイント実証＋取り込み試験結果を追記 |

※ 生牌譜・候補・ref・中間データはコミットしない（gitignore）。

---

## 4. 検証結果

- 取得: OK（公開URL `0/log/?{ref}`・認証/Cookie不要・単発取得）
- 解析/抽出: OK（1牌譜→ロン局5件・turn≥10・4人河）
- データ検証: OK（候補5件 validate PASS／正本10問 PASS）
- ingest: OK（採用0/保留5・**生牌譜削除確認**・正本不変）
- typecheck: OK（tsc 0err）／ build: OK（next build 成功）
- 機密スキャン: OK（git diff に ref 混入なし）
- ob sync: Fully synced／ git push: mahjong 7639773（pushed）／ obsidian-vault 本セッションで mirror+push

---

## 5. 未対応 / 報告サマリ

- **探したページ**: tenhou.net/sc/raw・list.cgi・scc{YYYYMMDDHH}.html.gz、参考: 天鳳鳳凰卓DB(nnkr.jp/tenhoulog)
- **候補URL数**: 3（四鳳南喰赤）
- **取得成功数**: 1
- **採用問題数**: 0
- **不採用理由**: 自動生成のため読み筋・危険度が未検証（B保留）。`S/Aのみ採用`ルールで不採用＝設計通り。
- **生牌譜削除確認**: 済（`--raw` 削除を確認）
- **検証結果**: tsc/build/validate すべて OK
- **commit/push**: 7639773 pushed

---

## 6. 危険ポイント

- find.cgi は非会員 ERROR。**認証突破はしない**。公開取得は `0/log/?{ref}` のみ使用。
- 生牌譜・gameId は保持/コミットしない。牌譜由来問題の公開可否は人間の法務判断（承認必須）。
- 自動生成を S/A に上げる場合、読み筋の質が伴わないと低品質問題を注入するリスク → 現状は B 止まりで安全側。

---

## 7. 次にやるべきこと

- 読み筋ヒューリスティック（無スジ/スジ/壁/現物/字牌処理）の自動付与で B→A 昇格を試す。
- 人手レビュー1局で S/A 採用 → `yomi-questions.json` 反映の小規模実証。
- scc 一覧からの ref 自動抽出（少数・マナー遵守）の補助スクリプト化。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: mahjong
作業: 天鳳公開牌譜URLを少数取得し /yomi 取り込み試験
runId: 20260607-234517
日付: 2026-06-07
GitHub commit: mahjong 7639773

## 実施内容
- 公開アーカイブ(sc/raw/scc)から鳳凰卓4鳳半荘の?log=URLを3件特定、1件をtenhou.net/0/log/?{ref}で一時取得(認証/Cookie不要、find.cgiは非会員ERRORなので不使用)
- tenhou-to-yomi.mjsでmjlog解析→ロン局5件抽出→候補化(validate PASS)
- ingestで採用0/保留5(自動生成=B、S/Aのみ採用で不採用)、生牌譜削除確認、正本不変

## 確認したい観点
- 公開URL限定の取得方針(0/log/?ref のみ・認証突破しない)は規約・倫理的に妥当か
- 自動生成をB止まりにし採用0とした品質判断は妥当か。B→A昇格に必要な読み筋検証の設計
- 牌譜ポインタ(gameId)非保持・idハッシュ化で再配布リスクを十分下げられているか
- 次に実装すべき読み筋ヒューリスティックの優先順位
````

---

## 関連

- progress runId: 20260607-234517（正本）
- 関連 run: 20260607-212218 / 20260607-224848
- 関連レビュー: [[2026-06-07_tenhou-paifu-collection-research]] / [[2026-06-07_yomi-ingest-no-raw-retention]]
- 関連アプリ: [[../02_apps/mahjong]]
