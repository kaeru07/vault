---
date: 2026-06-29
task: ゴール承認/達成確認に横並びフィルター追加+新ページをタブに追加
runId: 20260629-152828
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260629-110250, 20260629-124303, 20260629-141541]
commitHashes: [df4b909, f35e51b]
---

# ゴール承認/達成確認の横並びフィルター＋新ページのタブ追加

## 1. 作業目的
アプリ開発フロー実装後のユーザー指摘:「ゴール承認をプロジェクト別にまとめたら縦長でiPhoneで見られない／フィルターを追加して／他の画面も横並びに／それぞれの新ページはタブに追加して／モックへ行けるように」。

## 2. 実施内容
- **横並びプロジェクトフィルター**: ゴール承認タブ・達成確認タブに、横スクロールのプロジェクトチップ（すべて＋各プロジェクト件数）を追加（local state・既存URLフィルタと独立）。
- **折りたたみ既定**: 「すべて」時は各グループ `<details>` を閉じた状態、特定プロジェクト選択時だけそのグループを展開 → 縦長を解消。サブタブ切替で選択projectが消えたら表示上allへフォールバック。
- **新ページをタブに追加**: /app-proposals（アプリ承認・モックプレビュー）と /project-complete（PJ完了）を BottomNav（iPhone横スクロール）と TopNav（PC・newNavItems＋NEW_ROUTES）に追加。モック確認ページへタブから直行可能に。

## 3. 変更ファイル
- `progress/components/newux/InboxTabs.tsx`（フィルターチップ＋折りたたみ既定, commit df4b909）
- `progress/components/navigation/BottomNav.tsx` / `progress/components/navigation/TopNav.tsx`（新ページ2件を下タブ・上タブに追加, commit f35e51b）

## 4. 検証結果
- tsc0 / build0。
- /decide?tab=goalApproval・?tab=achievement 200・「すべて」チップ描画・error0。
- home 200・下タブ/上タブに「アプリ承認」「PJ完了」描画（各2リンク）。/app-proposals・/project-complete とも 200。
- data/real 手編集なし。

## 5. 未対応
- iPhone実機での目視（チップ絞り込み・グループ折りたたみ開閉・新タブからの遷移・モック表示）。

## 6. 危険ポイント
- フィルターは local state による表示絞り込みで、既存のURLフィルタ・承認/レビュー操作には非干渉。
- ナビ追加は表示のみ（既存タブ・ルーティング不変）。

## 7. 次にやるべきこと
- iPhone実機確認。
- （継続候補）アプリ概要『承認』→『詳細仕様ゴールの自動起票』接続。

## 8. ChatGPTレビュー依頼文
```
対象アプリ: progress / runId: 20260629-152828 / commits: df4b909, f35e51b
GitHub: kaeru07/ny01

ゴール承認・達成確認タブに横並びプロジェクトフィルター(チップ)+グループ折りたたみ既定を追加し、iPhoneでの縦長を解消。新ページ/app-proposals(アプリ承認・モック)と/project-complete(PJ完了)を下タブ・上タブに追加しました。

観点:
1. iPhoneでプロジェクトチップ(横スクロール)+折りたたみ既定は見やすいか
2. local stateフィルタと既存URLフィルタの併用に破綻がないか
3. 下タブの項目数が増えたことで横スクロールが使いにくくないか(取捨選択すべきか)
```
