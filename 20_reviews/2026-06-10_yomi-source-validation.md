---
date: 2026-06-10
task: /yomi 原本再現性の検証(sourceValidation)を追加 — 原本とアプリ表示の差異を14項目で検出
runId: 20260610-220105
targetApp: mahjong
monetizationImpact: low
theme: [mahjong, workflow]
relatedRunIds: []
commitHashes: [89dd84e]
---

# 2026-06-10 /yomi 原本再現性の検証(sourceValidation)を追加

> 1 作業 = 1 ファイルの自動生成レビュー。/yomi が「問題データを表示しているだけ」で原本再現性の保証が無かった問題に対し、原本との差分検出層を追加した。

---

## 1. 作業目的

- なぜこの作業をするのか: 現在の /yomi は問題データを表示している状態で、**原本問題の再現性保証が無い**。原本とアプリ表示の差異を検出できるようにする。
- 背景: 牌譜由来の取り込みで、原本の盤面（河・鳴き・自風など）が変換過程で欠落/改変されてもそれを検知できなかった。
- 期待効果: 原本から生成した問題について「どの情報が再現され、どの情報が欠落したか」を機械的に判別でき、原本差分をレビューせずに S/A 採用するのを防ぐ。

---

## 2. 実施内容

- 問題ごとに `question.sourceValidation` を追加（任意フィールド）。**確認14項目** = プレイヤー位置 / 自風 / 場風 / 河枚数 / 河順序 / 手牌枚数 / 鳴き内容 / 鳴き位置 / ドラ / 巡目 / 点数 / リーチ有無 / 放銃者 / 和了者。
- 比較エンジン `compareToOriginal(rendered, original)`: 各項目を `match` / `mismatch` / `missing`（原本にあるが表示で欠落）/ `unknown`（原本に該当なし・分母から除外）で判定。`matchRate = matched / checked`。
- `validationStatus`: `exact`（全一致）/ `partial`（一部）/ `failed`（致命項目=和了者・放銃者・巡目の不一致 or 一致率<0.5 or 検証不能）。
- 取り込み時、原本（画像/JSON）があれば**差分レポートを必ず生成**。`ingest-yomi --original` で各候補に sourceValidation を付与 → **差分 failed の S/A は隔離** → 差分レポートを `data/source-validation/`（gitignore）に保存。
- 差分は `上家河 12枚 → 8枚` / `下家鳴き欠落` / `自風不一致` のように出力（`formatDiffReport`）。
- UI: `/yomi/review` に原本一致率バッジ + 差分サマリーパネルを追加。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| types/yomi.ts | SourceValidation 型群 + question.sourceValidation? |
| scripts/lib/yomi-source-validate.mjs | 新規。compareToOriginal(14項目) / formatDiffReport / 形式検証 |
| scripts/lib/yomi-validate.mjs | sourceValidation 形式検証 + 原本差分 failed の S/A をエラー化 |
| scripts/ingest-yomi.mjs | --original 対応・sourceValidation 付与・failed S/A 隔離・差分レポート生成 |
| scripts/yomi-source-report.mjs | 新規。原本差分レポート単体 CLI（--write で書き戻し） |
| lib/yomi.ts | status 表示ヘルパー + getYomiValidationStats |
| app/yomi/review/page.tsx | 原本一致率バッジ + 差分サマリーパネル |
| docs/yomi-ingestion.md | 「原本再現性の検証」仕様を追記 |
| .gitignore | data/source-validation/（差分レポート）を ignore |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit exit 0）
- build: OK（next build 12ページ成功）
- lint: n/a（このリポは next lint 未設定 / tsc で代替）
- データ検証: validate-yomi（正本10問）PASS / yomi-stats OK
- エンジン単体: exact→一致率1.0 / winner mismatch(critical)→failed(一致率0.909でも critical 優先) / partial→62% で「上家河 12枚 → 8枚」等の差分文言を確認
- ゲート: ingest --dry-run --original で exact→採用 / failed(S)→隔離(理由付き)。validate-yomi が failed+S を弾くことも確認
- 機密パターン事前チェック: OK（鍵・トークン等なし）
- ob sync: Fully synced
- git push: 89dd84e（mahjong main）

---

## 5. 未対応

- 既存正本10問は手作成のため sourceValidation 未付与（任意フィールド・UI は存在時のみ表示）。
- 原本画像 → original JSON 化の運用テンプレは未整備（手書き起こし前提）。
- 手牌枚数（handCounts）検証は原本に hands がある場合のみ。実データでの確認は未実施。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。sourceValidation は任意フィールドで、既存10問・既存UIは未指定でも従来通り動作。
- DB / 認証 / 本番 / 機密: 触れていない。生牌譜保管なし方針も維持（差分レポートは問題データのみ・gitignore）。
- 採用ゲート変更: 原本差分 failed + qualityRank S/A は validate-yomi がエラー化する。原本を渡さない従来フローには影響しない（sourceValidation 無しは素通り）。
- ロールバック手段: commit 89dd84e の revert。

---

## 7. 次にやるべきこと

- tenhou-to-yomi 変換時に「解析直後の state」を original として併走出力 → 自動で原本照合できるようにする。
- 原本画像書き起こし用の original JSON テンプレ整備。
- partial 採用問題の人手レビュー運用（差分サマリーをレビューキュー or review 画面で確認）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: mahjong（/yomi 当たり牌読みクイズ）
作業: /yomi 原本再現性の検証(sourceValidation)を追加
runId: 20260610-220105
日付: 2026-06-10
GitHub commit: 89dd84e（リポジトリ kaeru07/mahjong, main）

## 作業目的
- /yomi は問題を表示しているだけで原本再現性の保証がない。原本とアプリ表示の差異を検出できるようにする。

## 実施内容
- question.sourceValidation を追加。14項目(プレイヤー位置/自風/場風/河枚数/河順序/手牌枚数/鳴き内容/鳴き位置/ドラ/巡目/点数/リーチ有無/放銃者/和了者)を原本と突き合わせ。
- match/mismatch/missing/unknown 判定・一致率・status(exact/partial/failed)。
- 取り込み時に原本があれば差分レポートを必ず生成。原本差分failedのS/Aは隔離。
- /yomi/review に原本一致率バッジ+差分パネル。

## 変更ファイル
- types/yomi.ts / scripts/lib/yomi-source-validate.mjs(新規) / scripts/lib/yomi-validate.mjs
- scripts/ingest-yomi.mjs / scripts/yomi-source-report.mjs(新規) / lib/yomi.ts
- app/yomi/review/page.tsx / docs/yomi-ingestion.md / .gitignore

## 検証結果
- tsc/build/validate-yomi: 全pass
- エンジン単体: exact/partial/failed・差分文言(上家河12→8等)を確認
- ゲート: failed(S)→隔離、validate-yomi が failed+S を弾く

## 確認したい観点
- 14項目の粒度・抜け(例: 役/打点/供託リーチ棒は対象外で良いか)
- status の閾値設計(critical=和了者/放銃者/巡目、一致率<0.5でfailed)は妥当か
- missing(欠落)とunknown(原本に無い)の切り分けは妥当か
- 手牌枚数検証(原本にhandsがある場合のみ)の設計
- 原本JSONの形式・運用(画像書き起こしの現実性)
- 収益化インパクトの評価は妥当か（low）
````

---

## 関連

- progress runId: 20260610-220105（正本）
- 仕様: docs/yomi-ingestion.md §「原本再現性の検証」
- 関連アプリ: [[../02_apps/mahjong]]
- 取り込みルール: [[../03_prompts/Claude-Code標準運用]]
- フォルダ運用: [[../20_reviews/README]]
