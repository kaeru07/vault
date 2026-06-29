---
date: 2026-06-29
task: アプリ開発の自動実行フロー整理(Phase1-2実装)
runId: 20260629-124303
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260629-093418, 20260629-110250, 20260629-124303]
commitHashes: [a5d52d4, 6f06c0c]
---

# アプリ開発の自動実行フロー整理（Phase1-2）

## 1. 作業目的
ユーザー指示「progressアプリの自動実行フローを、アプリ開発の場合に沿って整理したい」。提示された7ステップ（案調査→概要承認→詳細仕様をゴール承認→1ゴールずつ作業→達成連絡→NGなら修正→プロジェクト全達成で差分+候補）を現状実装と突き合わせ、段階実装する。

## 2. 実施内容
- 現状フローを調査し7ステップとのギャップを特定（設計 runId 20260629-093418）。
- 方針確認: モックアップ=簡易HTML生成プレビュー / 着手順=前提から段階実装（ユーザー選択）。
- **Phase1**: ゴール承認タブをプロジェクト別グループ化（step3前提）。
- **Phase2**: アプリ概要承認の専用ページ /app-proposals ＋ iPhone枠の簡易HTMLモックプレビュー（step2）。

## 3. 変更ファイル
- Phase1: `progress/components/newux/InboxTabs.tsx`（goalApprovalタブをprojectId別グループ化, commit a5d52d4）
- Phase2（commit 6f06c0c）:
  - 新規 `lib/app-proposals.ts` / `components/app-proposals/MockPhone.tsx` / `components/app-proposals/AppProposalCard.tsx` / `app/app-proposals/page.tsx` / `app/api/app-proposals/[id]/decide/route.ts`
  - `lib/nav-menu.ts`（「アプリ概要承認」追加）
  - `lib/types/operations.ts` / `lib/operations-store.ts`（OperationalDecisionにapp_proposal用フィールド追加。既存未コミットのfactoryMaxPerEpicバックエンドを同梱＝先行出荷UIの補完・追加のみ安全）

## 4. 検証結果
- Phase1: tsc0/build0、/decide 200・error0（client groupingの最終見た目はブラウザ確認）。proposed51件=try-research16/未分類22/company-mgmt13の3グループ。
- Phase2: tsc0/build0、/app-proposals 200・5アプリ(将棋/麻雀/ny-ai/news-app/解析)・モック・承認/却下/保留描画・error0。decide API: 不正body→400、hold正常→operational-decisions.ndjsonにtype=app_proposalで記録・ndjson無破損をE2E確認。nav登録確認。
- data/real は手編集なし（decide APIは既存recordOperationalDecision経由の正規書き込み）。

## 5. 未対応（Phase3-4・設計確認待ち）
- **Phase3**: ゴール達成内容確認欄（step5）＋NG→修正概要の接続（step6）。※既存のレビュー/fixPrompt(修正する)と重複しないよう、達成確認を既存レビューの再フレームにするか新欄にするか要確認。
- **Phase4**: プロジェクト完了サマリー（step7・全ゴール達成→残レビュー+現運用との差分+必要候補提案）。完了検知の定義（全Goal done?）要確認。
- iPhone実機での目視（承認のグループ折りたたみ・モックの見え方）。

## 6. 危険ポイント
- decide APIは正規store経由のみ（生fs書き込み禁止を厳守）。data/real手編集なし。
- 共有型(operations)変更は追加のみ・既存利用箇所に非破壊。
- 既存レビュー機構との重複リスクがPhase3にある（設計確認で回避）。

## 7. 次にやるべきこと
- Phase3の設計確認（達成確認欄=既存レビュー再フレーム or 新欄 / 達成のトリガ定義）。
- 確認後Phase3→Phase4を段階実装。
- iPhone実機確認。

## 8. ChatGPTレビュー依頼文
```
対象アプリ: progress / runId: 20260629-124303 / commits: a5d52d4, 6f06c0c
GitHub: kaeru07/ny01

アプリ開発の自動実行フロー(7ステップ)の段階実装Phase1-2です。
Phase1=ゴール承認タブのプロジェクト別グループ化。Phase2=アプリ概要承認ページ/app-proposals+iPhone簡易HTMLモックプレビュー(承認/却下/保留→operational-decisions記録)。

観点:
1. 7ステップ(案調査→概要承認→詳細仕様ゴール承認→作業→達成確認→NG修正→プロジェクト完了差分)の設計として妥当か
2. Phase3「ゴール達成内容確認欄」は既存レビュー(reviewStatus/fixPrompt)と重複しないか。再フレーム推奨か新欄推奨か
3. アプリ概要承認→詳細仕様ゴール承認への接続の設計
4. プロジェクト完了サマリー(残レビュー+現運用差分+候補)の現実的な実装範囲
5. モックを「簡易HTML決定生成」から将来AI生成に拡張する際の注意点
```
