---
date: 2026-06-28
task: progress全ページの縦長レイアウトをiPhone向けにコンパクト化(横並び化)
runId: 20260628-222810
targetApp: progress
monetizationImpact: none
theme: [workflow, obsidian]
relatedRunIds: [20260628-190524, 20260628-211508]
commitHashes: [7618931]
---

# progress全ページの縦長レイアウトをiPhone向けにコンパクト化

## 1. 作業目的
「運用に限らず、progressアプリの全ページに同じコンパクト化を適用して」というユーザー指示への対応。先行で /guide・automation・goal-dashboard・portfolio・revenue に適用した「iPhone前提のコンパクト/横並び設計」を全ページへ一貫展開する。途中で「legacyは対象外でいいよ」の指示があり legacy を除外。

## 2. 実施内容
- 共通コンパクト設計を全 page.tsx に一貫適用: 最外の縦余白(space-y/py)圧縮、セクションpadding圧縮、入れ子カードの二重余白を平坦化、純表示の短いカード/タイル/統計/凡例の縦1列→`grid grid-cols-2`横並び、長い列挙は折りたたみ、過大なフォント/バッジの縮小。
- 対象: ホーム`/`(サマリー/Factory計器盤/Project・Goal進捗/修正依頼/AI保留/最近の成果・調査)、queue/activity/epic/epic[epicId]/factory candidates/monetization[id]/recommended-epics[id]/projects/projects[projectId]/projects import/morning/daily/logs/usage/goal-planner/integration-map ほか、計41 page.tsx。
- legacy(home/page)は途中指示で対象外化し、今回分を破棄(HEADへ戻す)。
- 実装はCodex委譲、設計/検証/安全判断/commit/POSTはClaude。

## 3. 変更ファイル
- `progress/app/**/page.tsx` 41ページ（純表示カードの2列横並び化・余白/フォント圧縮・入れ子余白平坦化。legacy除く・interactiveロジック不変）
- ※既存の未コミット(lib/*・automation-config route・未追跡lib)は本作業対象外として**コミットに含めていない**

## 4. 検証結果
- `npx tsc --noEmit`: pass / `npm run build`: pass（legacy除外後に再ビルド）
- 実描画: pm2(next start)再起動後、**静的37ページ＋動的4ルート全てHTTP200・errorマーカー0**。動的は実IDで確認(epic/epic-91, projects/autoexec-test-proj, monetization/mc-anglerlog-001, recommended-epics/rec-birdlog-billing-560e-5)。
- data/real 全JSON妥当・data/配下に今回の変更なし。
- 機密スキャン: 実値ヒットなし。commit `7618931` → main push済。

## 5. 未対応
- iPhone実機での目視（主要ページの2列カード横崩れ・タップ困難の有無）は人間確認待ち。
- 各ページの **interactive子コンポーネント内部**（TodoManager / QueueActionButton / ApprovalActions / RecActions / ProjectSummaryEditor / BlockersEditor / GoalPlannerForm / LogList / ExecutionRunList / AppUrlsBoard、automation の操作パネル等）は state/fetch/form/onClick を持つため構造変更せず＝縦積みのまま（要確認候補）。
- legacy は対象外（ユーザー指示）。

## 6. 危険ポイント
- 全ページ横断のため影響範囲が広い。安全策として変更を className(grid列数/gap/padding/margin/text/rounded/details化)と純表示の並びのみに限定し、onClick/useState/useEffect/fetch/form/router/API/props は不変（diff確認済）。
- data/real は前回破損の教訓から不接触を継続。
- コミットは page.tsx 41件のみに限定し、同居する既存の未コミット(lib/route)を巻き込まないようステージを精査。

## 7. 次にやるべきこと
- iPhoneでホーム/queue/activity/epic詳細/projects等を開き、2列カードの横はみ出し・文字詰まり・タップ領域を確認。崩れがあれば該当ページのgrid列数/gapを微調整。
- interactive子コンポーネントの縦積みを横並びにしたい場合は、ロジック保持のままレイアウトのみ別途対応。

## 8. ChatGPTレビュー依頼文
```
対象アプリ: progress (/root/company/apps/ny01/progress) / runId: 20260628-222810 / commit: 7618931
GitHub: kaeru07/ny01

progressの全ページ(41 page.tsx・legacy除く)を、iPhone前提でコンパクト化しました。
純表示カード/タイル/統計/凡例の縦1列→2列横並び、入れ子余白の平坦化、padding/text圧縮が中心です。
interactive(onClick/state/fetch/form)のロジック構造は不変で、Tailwindのclassと純表示の並びのみ変更しています。

以下の観点でレビューしてください:
1. iPhone(375px幅)で各ページの2列グリッドが潰れ/横はみ出しなく読めそうか(特にホーム/・queue・activity・epic詳細)
2. コンパクト化で詰まりすぎ・タップ領域が小さすぎる箇所がないか
3. 全ページ一括適用で、特定ページだけ情報密度が高すぎ/低すぎる不整合がないか
4. interactive子コンポーネント(縦積みのまま)を今後横並びにする際の安全な進め方
5. 共通コンパクト設計を今後の新規ページにも踏襲するためのガイドライン化案
```
