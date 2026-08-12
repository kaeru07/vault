---
date: 2026-08-12
task: 麻雀解析エンジンV2再構築(@kobalab採用+計算一元化+検算/性質/ミューテーションテスト)
runId: 20260812-083626
targetApp: mahjong-analyzer
monetizationImpact: high
theme: [app-strategy, mahjong, workflow, prompt-template]
relatedRunIds: [20260811-214434]
commitHashes: [ccc8fa2]
---

## 1. 作業目的

手牌解析アプリで「シャンテンが一律値に見える／受け入れの誤集計／同数値なのに1つだけ推奨／牌効率に反する推奨順／未成立の役を出る役と断定／上部候補・詳細カード・解説・役タグの食い違い」が出ていた。表示の対症修正ではなく、解析エンジンを設計から作り直し、正解計算を1つの中核に集約してUI・ランキング・解説がすべて同じ構造化結果を参照する構成にする。

## 2. 実施内容

- **Phase0 対象特定**: 対象を `apps/mahjong-analyzer`（GitHub kaeru07/mahjong-analyzer, 手牌解析3タブ）に一意特定。難易度/タグ/問題JSON/クイズ画面は analyzer に非存在＝対象外（ユーザー確認: 今まで通りのアプリ）。
- **Phase1 採用判定(Claude)**: `@kobalab/majiang-core@1.4.1` をスパイク。MIT・Node専用API依存なし(160K,静的export安全)・xiangting(総合)+形別+tingpai(改善牌種)。報告手牌 `444m579m244p36789s` の全打牌テーブルが自作エンジンと**完全一致**。採用条件を全充足→ adapter 経由で採用。ADR 0001 と計算仕様契約 mahjong-calc-spec.md を作成。
- **Phase2 実装(Codex委譲)**: adapter に計算を一元化。辞書順ランキング(シャンテン→受入枚数→牌種数)+同率rank/tieGroupId、受け入れ内訳(牌ごと残り枚数)、入力検証、役の4分類(確定/狙える/条件付き/参考=断定廃止)、UI同期(効率/点数タブと詳細を候補IDで同期)。
- **Phase3 独立検証(Claude)**: 差分50k・固定fixtureの2実装一致固定・性質テスト・Stryker実走を確認し close_ok。commit/push。

## 3. 変更ファイル

- `docs/adr/0001-adopt-kobalab-majiang-core.md`（新規）/ `docs/mahjong-calc-spec.md`（新規）
- `lib/mahjong/adapter/kobalab.ts`（新規: 権威adapter, 型と牌文字列を隔離）
- `lib/mahjong/analyzer.ts`（唯一の解析入口に再構成 / compare・same・buildUkeireDetail をexport）
- `lib/mahjong/types.ts`（rank/tieGroupId/shantenByForm/ukeireDetail/ukeireKinds/YakuAssessment 追加）
- `lib/mahjong/yaku.ts`（役分類・断定廃止）/ `lib/mahjong/shanten.ts`（形別関数export=検算オラクル, ロジック不変）
- `components/AnalysisResult.tsx` / `components/DiscardCard.tsx`（推奨=rank1全件・タブ同期・受け入れ内訳・役status）
- テスト: `analyzer.contract.test.ts` / `differential.test.ts` / `fixtures.test.ts` / `mutation-guard.test.ts` / `fixtures/*.json`
- `stryker.conf.json` / `package.json`(test:fast/full/mutation, devDeps) / `package-lock.json`

## 4. 検証結果

- typescript(tsc): OK / build(静的export prerendered, 109kB): OK
- test:fast: 29 pass / test:full: 差分 **50,000件 seed=0x20260811 不一致0**
- 性質テスト(fast-check): スート対称/並び順不変/赤5=通常5/4枚上限/残り0-4/総数=内訳合計/決定性/同点で単独推奨にしない/不正入力を正常処理しない → 通過
- ミューテーション(Stryker): **重要計算関数(compareEfficiency/sameEfficiency/buildUkeireDetail/rank付け)の生存0件**。adapter残存34件は全て等価と確定(下記6)。
- 機密スキャン: clean / commit ccc8fa2 push済み

## 5. 未対応

- lint: このアプリはESLint未導入。tsc+build+テストで担保し N/A（将来 next/core-web-vitals 導入は任意）。
- 二手先評価・点数重視の期待打点化・副露/ドラ/河入力は将来拡張。
- shanten.ts(検算オラクル)自体はStryker対象外(七対子/国士は差分50k+fixtureで固定)。

## 6. 危険ポイント

- 受け入れ枚数は「見えている牌＝手牌のみ」を前提とした最大残り枚数。実際の山残り(河/副露/ドラ未考慮)とは異なる旨をUIに明示済み。誤読しないこと。
- Stryker残存34件(adapter)は**等価ミュータント**: `libraryTileToIndex` の赤5(0)分岐・未知スート-1・防御throwは@kobalabの正当出力で到達不能。`improvingTileIndices` の `<4`→`<=4` と filter除去は改善牌が定義上4枚未満のため no-op、`result===null` は13枚手で不発。`validateCounts` は常に妥当入力。→ 振る舞い(シャンテン/受け入れ/順位)は変えられない。
- 14枚の完成形手牌では analyzeHand.shanten は「最良打牌後」の値(=0)を返す(生手牌の-1とは別意味)。fixtureテストで区別済み。

## 7. 次にやるべきこと

- 必要なら TestFlight ビルドを指示（engine V2 はシャンテン値は不変、推奨の同率表示・役分類・タブ同期が改善）。本スペックの「外部公開禁止」に従い自動起動していない。
- ChatGPTレビューで採用判断とテスト設計(特に等価ミュータントの根拠)を第三者確認。

## 8. ChatGPT レビュー依頼文

```
麻雀手牌解析アプリ mahjong-analyzer の解析エンジンをV2再構築しました。以下の観点でレビューをお願いします。

対象: GitHub kaeru07/mahjong-analyzer / commit ccc8fa2 / runId 20260812-083626
中核: lib/mahjong/adapter/kobalab.ts, lib/mahjong/analyzer.ts, docs/adr/0001-*, docs/mahjong-calc-spec.md

設計:
- シャンテン/改善牌の正本を @kobalab/majiang-core(MIT) に一元化(adapterで型隔離)。自作DFS(shanten.ts)は第2独立実装として残し検算オラクルに。
- ランキングは辞書順(シャンテン昇順→受入総枚数降順→受入牌種数降順)+同率rank/tieGroupId。推奨は配列index0でなくrank===1全件。
- 受け入れ枚数=Σ(4-見えている(手牌のみ))=最大残り枚数(河/副露/ドラ未考慮)としてUIに明示。
- 役は確定/狙える/条件付き/参考に分類し「出る/消える」の断定を廃止。
- UIは効率/点数どちらのタブで候補選択しても詳細カード・解説・役を候補IDで同期。

検証:
- 差分テスト: adapter vs 自作DFS を seed固定で50,000件、総合/通常形/七対子/国士シャンテン+改善牌+受け入れ枚数、不一致0。
- 固定fixture: 期待値を本番実装だけから生成せず、@kobalabと自作DFSの一致値として固定。報告手牌444m579m244p36789sの全打牌内訳も回帰。
- 性質テスト(fast-check)/ミューテーション(Stryker: 重要計算関数の生存0、adapter残存は等価と判断)。

質問:
1. @kobalabをruntime権威に、自作DFSを検算オラクルに残す二本立ては妥当か(将来どちらかに寄せるべきか)。
2. 受け入れ枚数を「手牌のみ最大残り枚数」とする単手牌前提のUI表現は誤解を生まないか。
3. Stryker残存34件(adapterの防御コード/no-opフィルタ上の変異)を等価として除外した根拠は十分か。見落としの behavioral 変異はないか。
4. 役の4分類のうちヒューリスティック依存(参考)の扱い方、非表示すべき役はあるか。
```
