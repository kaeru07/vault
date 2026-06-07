---
date: 2026-06-07
task: /yomi 牌譜取り込みルールの恒久化＋取り込みパイプライン土台の整備
runId: 20260607-190005
targetApp: mahjong
monetizationImpact: low
theme: [app-strategy, workflow]
relatedRunIds: [20260607-141704, 20260607-160250]
commitHashes: [9e64d13]
# reviewFileCommit:
---

# 2026-06-07 /yomi 牌譜取り込みルール・パイプライン土台

> 1 作業 = 1 ファイル の自動生成レビュー。

---

## 1. 作業目的

- なぜ: 実戦牌譜・追加問題データを、毎回ユーザー確認で止めずに `/yomi` 問題集へ取り込めるようにする。
- 背景: [[前進優先ルール]]（承認待ち停止しない）の確立を受け、取り込みも自走化したい。
- 期待効果: 牌譜提供→変換→検証→採用までを「ただ動く」状態にし、**上級者帯の良問を品質優先で蓄積**。

---

## 2. 実施内容

- 主な変更:
  - `CLAUDE.local.md` に §「麻雀 /yomi 取り込みルール」を追記（自走可/禁止、取り込み条件10項、ソース優先度4帯、品質ランクS-D、報告フォーマット）。
  - `apps/mahjong/docs/yomi-ingestion.md` 新規＝アプリ側の実装仕様正本。
  - `apps/mahjong/scripts/validate-yomi.mjs` 新規＝取り込み条件と整合を機械検証（終了コードで採用可否）。
  - `apps/mahjong/types/yomi.ts` に `YomiSource` / `YomiQualityRank` と任意フィールド `source` / `qualityRank` を追加。
  - `apps/mahjong/data/imported/`（B保留）・`apps/mahjong/data/rejected/`（C隔離/D破棄）を README 付きで新設。
  - メモリ `project-yomi-ingestion` 新規 + `MEMORY.md` 更新。
- 関連判断:
  - 問題は削除でなく rejected へ理由付きで隔離（元牌譜・既存良問は保護）。
  - 採用は S/A のみ。問題数より品質、上級者帯（魂天/王座/魂の間/鳳凰卓）を優先。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/mahjong/docs/yomi-ingestion.md | 取り込み仕様（新規） |
| apps/mahjong/scripts/validate-yomi.mjs | 整合検証スクリプト（新規） |
| apps/mahjong/types/yomi.ts | YomiSource・YomiQualityRank・任意 source/qualityRank 追加 |
| apps/mahjong/data/imported/README.md | 保留(B)置き場（新規） |
| apps/mahjong/data/rejected/README.md | 隔離(C)/破棄(D)置き場（新規） |
| CLAUDE.local.md | §麻雀/yomi取り込みルール追記 |
| ~/.claude/.../memory/project_yomi_ingestion.md | project memory（新規） |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit 0err）
- build: OK（next build 成功・全ルート静的）
- データ検証: OK（`node scripts/validate-yomi.mjs` 全10問PASS）
- 機密パターン事前チェック: OK（実値なし）
- ob sync: Fully synced
- git push: mahjong 9e64d13（pushed）／ obsidian-vault は本セッションで mirror+push

---

## 5. 未対応

- 実際の牌譜データはまだ未提供（ルール・土台のみ整備）。提供時に標準フローで自走取り込みする。

---

## 6. 危険ポイント

- 影響範囲: /yomi 関連の追加と型の任意フィールドのみ。既存10問・既存UI・「何切る」は無変更。
- 禁止操作の明文化: 元牌譜削除・既存良問の大量削除・force push・課金API・牌譜外部送信・認証情報保存・本番データ削除。
- ロールバック: 追加ファイル削除＋type の任意フィールド除去で元に戻せる（破壊的変更なし）。

---

## 7. 次にやるべきこと

- 魂天/王座/鳳凰卓帯の牌譜を収集し、標準フローで取り込み。
- validate-yomi.mjs に「読み筋が説明可能か」等の半自動ヒューリスティック追加を検討。
- 取り込みが増えたら difficulty/dangerLevel の分布監視。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: mahjong
作業: /yomi 牌譜取り込みルールの恒久化＋パイプライン土台整備
runId: 20260607-190005
日付: 2026-06-07
GitHub commit: mahjong 9e64d13

## 実施内容
- 取り込み仕様 docs/yomi-ingestion.md・検証 scripts/validate-yomi.mjs・型 source/qualityRank・imported/rejected ディレクトリを新設
- CLAUDE.local.md と メモリに取り込みルールを恒久化
- 問題数より品質優先、上級者帯(魂天/王座/鳳凰卓)優先、採用S/Aのみ、問題は削除でなくrejected隔離

## 検証
- validate-yomi 全10問PASS / tsc 0err / next build 成功 / secret scan OK / push 9e64d13

## 確認したい観点
- 取り込み条件10項・除外条件・品質ランクS-Dの基準は実戦的に妥当か
- validate-yomi.mjs の整合チェックに抜けはないか（特に「正解が一意」「リーチ後手出し」「壁/スジの自動判定」）
- ソース優先度(魂天/王座/鳳凰卓優先)と「品質優先・数より質」の方針は妥当か
- 自走取り込みで事故りやすい箇所はどこか
````

---

## 関連

- progress runId: 20260607-190005（正本）
- 関連 run: 20260607-141704 / 20260607-160250
- 関連レビュー: [[2026-06-07_mahjong-yomi-quality-improve]] / [[2026-06-07_forward-progress-rule]]
- 関連アプリ: [[../02_apps/mahjong]]
- フォルダ運用: [[../20_reviews/README]]
