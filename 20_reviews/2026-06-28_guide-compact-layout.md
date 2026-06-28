---
date: 2026-06-28
task: 運用ガイド等の縦長レイアウトをiPhone向けにコンパクト化(横並び化)
runId: 20260628-211508
targetApp: progress
monetizationImpact: none
theme: [workflow, obsidian]
relatedRunIds: [20260628-190524]
commitHashes: [920335b]
---

# 運用ガイド等の縦長レイアウトをiPhone向けにコンパクト化

## 1. 作業目的
直前に図ベース化した /guide を含め「用語欄や使い方ページがほぼ全て縦長で見づらい。iPhone前提で改善して。横並びで他ページも確認して」というユーザー指摘への対応。

## 2. 実施内容
- 縦長の主因を特定: (a) Slideがchildrenをglowボックス(p-3)で包み、中の各カードもp-3を持つ「カードの中にカードで余白三重」、(b) 用語辞典41件×1列、(c) 凡例(LegendGrid)1列、(d) フローノードの余白が大きい。
- `/guide`: Slideの二重余白を平坦化、番号バッジ/タイトル/フローノード/矢印/KPI/Roadmapを圧縮、用語辞典を2列グリッド化、凡例を2列化、キューの「操作」「並び順」を既定閉じのdetails折りたたみに。
- 他ページ横並び監査: portfolio/revenue/goal-dashboard/legacy-home の純表示カードを2列グリッド化・余白圧縮。automationは外側余白・状態カードのみ圧縮。
- 内容(文言/件数/データ/3タブ/footer)は不変、表示密度のみ向上。
- 実装はCodex委譲、設計/検証/安全判断/commit/POSTはClaude。

## 3. 変更ファイル
- `progress/components/guide/SlideKit.tsx` — Slide二重余白平坦化・FlowNode/Legend(2列)/StatTiles/Roadmap圧縮
- `progress/app/guide/page.tsx` — 用語辞典2列・キュー詳細details折りたたみ・space-y圧縮
- `progress/app/automation/page.tsx` — 外側余白・状態カード圧縮（ロジック不変。既存のfactoryMaxPerEpic UIを同梱commit）
- `progress/app/goal-dashboard/page.tsx` — 実行中ゴール一覧2列化
- `progress/app/portfolio/page.tsx` — プロジェクトカード2列化
- `progress/app/revenue/page.tsx` — Project別収益化距離2列化
- `progress/app/legacy/home/page.tsx` — Factory Metrics/Goal別進捗/進行中Epic表示カード2列化

## 4. 検証結果
- `npx tsc --noEmit`: pass / `npm run build`: pass
- 実描画: pm2(next start)再起動後、変更6ページ全てHTTP200・errorマーカー0。/guide に grid-cols-2 が10箇所（用語/凡例/KPI）・`<details>` 37件（FAQ36+キュー折りたたみ）を確認。
- 他ページ差分のロジック行(onClick/useState/fetch/await)増減ほぼ0＝presentationalのみを確認。
- data/real 全JSON妥当・data/配下に今回の変更なし。
- 機密スキャン: 実値ヒットなし。commit `920335b` → main push済。

## 5. 未対応
- iPhone実機での目視（用語辞典2列が潰れず読めるか・キュー折りたたみ開閉・他ページ2列カードの横崩れ）は人間確認待ち。
- automation/legacy-home の interactive部（Executor/AutoFallback/Runner/Dispatch/承認/レビュー/フォーム/Decision controls）はロジック絡みのため縦積み維持＝今回触れず（要確認候補）。

## 6. 危険ポイント
- 他ページはロジックを持つため、presentationalなTailwind調整(列数/padding/text/折りたたみ)のみに限定。構造・onClick・fetch・stateは不変。
- automation/page.tsx に前回からの既存未コミット分(factoryMaxPerEpic 深掘り回数UI=完了run 20260627-104233)が同梱された。これも正規の完了作業のためcommitに含めた。
- data/real は前回破損の教訓から引き続き不接触。

## 7. 次にやるべきこと
- iPhoneで /guide と portfolio/revenue/goal-dashboard/legacy-home を開き、2列カードの横はみ出し・文字の詰まりを確認。崩れがあればSlideKit/該当ページのgap・fontを微調整。
- automation等のinteractive縦積みを横並びにしたい場合は、ロジック保持のままレイアウトのみ別途対応。

## 8. ChatGPTレビュー依頼文
```
対象アプリ: progress (/root/company/apps/ny01/progress) / runId: 20260628-211508 / commit: 920335b
GitHub: kaeru07/ny01

運用ガイド/guideを含む複数ページの「縦長で見づらい」をiPhone前提でコンパクト化しました。
/guide: スライドの二重余白を平坦化、用語辞典(41件)を1列→2列、凡例を2列、キュー詳細を折りたたみ。
他ページ(portfolio/revenue/goal-dashboard/legacy-home)も純表示カードを2列横並びに。内容は不変・表示密度のみ向上。

以下の観点でレビューしてください:
1. iPhone(375px幅)で2列グリッド(用語辞典・凡例・各カード)が潰れ/横はみ出しなく読めそうか
2. コンパクト化で逆に詰まりすぎ・タップ領域が小さすぎる箇所がないか
3. キュー詳細の折りたたみ(既定閉じ)で重要情報が見つけにくくなっていないか
4. interactive要素を持つ automation/legacy-home の縦積みを横並びにする際の安全な進め方
5. SlideKitのプリミティブ圧縮が他ページ展開時も破綻しないか
```
