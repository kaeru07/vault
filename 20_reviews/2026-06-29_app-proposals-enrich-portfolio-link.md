---
date: 2026-06-29
task: アプリ概要承認のモック個別化+作成不要+反映可視化 / プロジェクトカード詳細遷移
runId: 20260629-191327
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260629-124303, 20260629-152828]
commitHashes: [2980b1c, 6e9e245]
---

# アプリ概要承認の充実＋プロジェクトカード詳細遷移

## 1. 作業目的
ユーザー指摘:「モックは良いが内容が全アプリ同じ→充実させて」「既存アプリ/プロジェクトは画面があるので作成不要、と操作できるように」「ページごと承認できそうだが反映状況がみづらい」「プロジェクトタブはタップで詳細を見られるように」。

## 2. 実施内容
- **モック個別化**: `lib/app-proposals.ts` に sourceProjectId別の具体画面テンプレを追加（将棋囲いトレーナー/麻雀/ny-ai/news-app/mahjong-analyzer）。未定義アプリは従来の汎用 buildScreens にフォールバック。MockPhone は未知 key でもクラッシュせず汎用描画。
- **作成不要(not_needed)**: 既存アプリ（画面あり）を「新規作成不要」とマークできる操作を追加。decide API の許可値に追加、カードに [作成不要] ボタン追加。
- **反映状況の可視化**: カード上部に大きなステータスバッジ（未判断/承認済み/作成不要/却下/保留）、決定済みはトーンダウン、ページ先頭にステータス集計チップ、並び順を未判断→保留→その他に。
- **プロジェクト詳細遷移**: /portfolio の案件カードを `/projects/[projectId]` への Link に（hover/active＋›表示）。

## 3. 変更ファイル
- commit 2980b1c: `lib/app-proposals.ts` / `app/api/app-proposals/[id]/decide/route.ts` / `components/app-proposals/AppProposalCard.tsx` / `app/app-proposals/page.tsx`
- commit 6e9e245: `app/portfolio/page.tsx`

## 4. 検証結果
- tsc0 / build0。
- /app-proposals 200・アプリ別の内容（囲い一覧/問題10問/Research DB/待ち牌クイズ）・作成不要ボタン・ステータス集計描画・error0。decide API not_needed → 200。
- /portfolio 200・プロジェクト詳細リンク18件・/projects/company-mgmt 詳細→200・error0。
- data/real 手編集なし（decide は既存 store 経由）。

## 5. 未対応
- iPhone実機での目視（アプリ別モックの中身・作成不要操作・ステータス集計の見やすさ・プロジェクト詳細遷移）。

## 6. 危険ポイント
- decide API は既存 recordOperationalDecision 経由のみ（生fs書き込みなし）。OperationalDecision 型は壊さず not_needed を許可値追加。
- portfolio リンク化は表示のみ（既存フィルタ・データ不変）。

## 7. 次にやるべきこと
- iPhone実機確認。
- （継続候補）概要『承認』→詳細仕様ゴールの自動起票接続。

## 8. ChatGPTレビュー依頼文
```
対象アプリ: progress / runId: 20260629-191327 / commits: 2980b1c, 6e9e245
GitHub: kaeru07/ny01

/app-proposals(アプリ概要承認)を改善: ①モックをアプリ別の具体画面に個別化 ②「作成不要(not_needed)」操作を追加(既存アプリ向け) ③ステータスバッジ+集計+未判断優先並びで反映状況を可視化。/portfolioのプロジェクトカードを詳細(/projects/[id])へ遷移可能に。

観点:
1. アプリ別モックの中身は各アプリの実態に合っているか/さらに足すべき画面は
2. 作成不要(not_needed)の意味付け・運用は妥当か(既存アプリの扱い)
3. ステータス可視化(バッジ+集計+並び)で承認の反映状況が分かりやすくなったか
4. プロジェクト一覧→詳細の導線は十分か
```
