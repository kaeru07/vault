---
date: 2026-08-22
task: mahjong-analyzer の二重管理を解消し、正本を apps/mahjong-analyzer へ一本化
runId: 20260822-130457
targetApp: mahjong-analyzer
monetizationImpact: medium
theme: [app-strategy, workflow]
relatedRunIds: [20260822-011730]
commitHashes: [b14ee68, b7317bd, 5983ffd]
reviewFileCommit:
---

## 作業目的

麻雀手牌解析AI（`com.kaeru07.mahjonganalyzer`）が2箇所のディレクトリで並行更新されており、
「直したはずの画面が審査に出ない」事故が起きうる状態だった。正本を1つに決めて統合する。

- `apps/mahjong-analyzer` — 独立リポジトリ `kaeru07/mahjong-analyzer`。解析エンジンV2、fastlane、codemagic、アイコン、TestFlight到達済み
- `apps/ny01/mahjong-analyzer` — AI工場が毎晩作業していたコピー。ストア提出用の設定を持たない。**直近6,503行が未コミット**

## 実施内容

1. **差分の洗い出し**: 両者は同じアプリの別実装に育っていた
   - A: エンジンV2（`@kobalab/majiang-core` を正本計算に採用・差分検証・固定fixture・Stryker）、`rank`/`tieGroupId`/`ukeireDetail`/`pointEvaluation`/`reachableYaku`、牌タップ入力＋3タブ（入力/解析/履歴）
   - B: 旧エンジン自前実装（391行）＋`emergingYaku`/`vanishingYaku`、テキスト入力UI（下書き復元・aria対応）、error/404/manifest、**Expo移行の試作**
2. **ユーザー判断を取得**（5点）: ①A正本 ②Capacitor継続 ③UIは全部移植 ④B側はREADME残して中身削除 ⑤入力UXは牌タップのみ
3. **Bの未コミット分を先にcommit**（`b14ee68`）— 消える前に復元可能性を確保
4. **A へ移植**（`b7317bd`）
5. **B を退役**（`5983ffd`）— 追跡ファイル100件を削除し、経緯と行き先を書いた README のみ残す

## 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `lib/mahjong/handDraft.ts` / `.test.ts` | 新規移植。入力途中の手牌を端末内保存（同期版のみ） |
| `lib/mahjong/parser.ts` / `.test.ts` | NFKC正規化・実行時型ガード・字牌への赤ドラ0拒否・同種赤5重複拒否・15枚早期検出 |
| `lib/mahjong/tiles.ts` / `.test.ts` | `tileAccessibleName`（日本語牌名）・`tileDisplayKey` 追加。A の `sortTiles` は維持 |
| `lib/mahjong/analyzer.ts` / `types.ts` | `emergingYaku`/`vanishingYaku` を `possibleYaku` の打牌前後比較で付与 |
| `components/DiscardCard.tsx` | 順位バッジ・簡易ラベル・役の出入りチップ・受け入れ牌の読み上げラベル・有効牌なしの `role=status` |
| `components/HandInput.tsx` | 牌ボタンの読み上げを日本語牌名へ・選択枚数の `aria-live` |
| `app/page.tsx` | 下書きの保存/復元をタップ入力へ統合（13枚未満は部分パースで復元） |
| `app/error.tsx` / `global-error.tsx` / `not-found.tsx` / `manifest.ts` | 新規移植 |
| `app/layout.tsx` / `globals.css` | PWA・iOSメタデータ、`app-safe-area` |
| `docs/store-readiness.md` | 移植。技術スタックを Expo → Capacitor継続 に修正、タップ入力前提へ書き換え |
| `apps/ny01/mahjong-analyzer/**` | 追跡100ファイル削除 → README のみ |

## 検証結果

- `npx tsc --noEmit`: OK
- `npm run test:fast`: **62件 全pass**（差分検証2000ケース込み。移植前は37件）
- `npm run build`: exit 0（静的書き出し）
- ヘッドレスブラウザ（390px・アプリの `out/` を一時配信）: 牌タップで14枚入力 → 解析 → **第1候補バッジ表示** → 受け入れ表示 → リロード後に**下書き 2/14 が復元** → 横スクロールなし・JSエラーなし
- 役の出入り: `1112345678999m1m` で「消える役: 対々和」を検出（Node上で確認）
- progress の審査提出準備タブが4アプリ・スクショ6枚を正しく解決することを確認（統合でパス解決が壊れていない）
- 機密スキャン: ヒットなし
- push: `kaeru07/mahjong-analyzer` `b7317bd` / `kaeru07/ny01` `5983ffd`

## 未対応

- **ny01/mahjong-analyzer に未追跡の残骸が残っている**（`node_modules` 535MB / `expo` 416MB / `out` / `ios`）。`rm -rf` はユーザーに拒否されたため未削除。約950MB
- 統合後のUIでのストア用スクリーンショット撮り直しは未実施（既存6枚は統合前のUI）

## 危険ポイント

- **移植しなかったもの**: Expo (React Native) 版の試作 / テキスト入力UI / handDraft の非同期ストレージAPI。いずれもユーザー判断（Capacitor継続・タップ入力一本化）による。復元が必要なら ny01 の `b14ee68` から取り出す
- `emergingYaku`/`vanishingYaku` は `possibleYaku` のヒューリスティックを前後比較しただけの**参考情報**。点数・受け入れの正本計算（adapter / point-evaluator）には関与させていないため、V2 の検証済み精度は落ちていない
- AI工場（factory-schedule）が今後も `ny01/mahjong-analyzer` を対象に選ぶと、README しかないディレクトリで作業してしまう。Goal / Epic の targetApp が古いパスを指していないか要確認

## 次にやるべきこと

1. AI工場の Goal / Epic が指す mahjong-analyzer のパスを確認し、正本側へ向ける
2. 統合後のUIでストア用スクリーンショットを撮り直す（審査提出準備タブの「スクショを撮る」）
3. 不要なら ny01 側の未追跡残骸（約950MB）を手動削除する
4. Codex 使用上限（8/27まで）に伴う Factory executor の扱いを決める（未回答）

## ChatGPT レビュー依頼文

```
対象: kaeru07/mahjong-analyzer（main, commit b7317bd）と kaeru07/ny01（main, commit 5983ffd）
runId: 20260822-130457

同じアプリが2つのディレクトリで並行更新されていたため、独立リポジトリ側を正本として統合し、
もう一方は README だけ残して退役させました。以下の観点でレビューしてください。

1. 統合の取捨選択は妥当か
   - 残した: エンジンV2（@kobalab/majiang-core 採用・差分/fixture/ミューテーション検証）、
     牌タップ入力＋履歴、fastlane/codemagic/アイコン
   - 移植した: 下書き復元、入力検証強化、a11y（日本語牌名の読み上げ）、役の出入り表示、
     error/404/manifest、PWA・iOSメタデータ
   - 捨てた: Expo(React Native)版の試作、テキスト入力UI、非同期ストレージAPI
   Capacitor継続・タップ入力一本化という前提で、捨てた判断に見落としはないか。

2. 役の出入り（emergingYaku/vanishingYaku）の実装位置
   - possibleYaku（ヒューリスティック）の打牌前後比較で求め、点数・受け入れの
     正本計算には関与させていない。「参考情報」としてUIに出すことの是非。
   - ユーザーが正確な役判定と誤解するリスクをどう抑えるべきか。

3. 下書き復元の設計
   - 保存は手牌文字列（例 "123m456p"）、復元時は parseHand、13枚未満は部分パース。
   - 壊れた保存値・5枚目・15枚目は復元しない。この防御で十分か。

4. 退役ディレクトリの扱い
   - ny01 側は README のみ、実体は git 履歴（b14ee68）から復元可能。
   - AI工場が古いパスを対象に選ぶ事故を防ぐには、他に何を仕込むべきか。
```
