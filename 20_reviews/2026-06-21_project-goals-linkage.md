---
date: 2026-06-21
task: 理想運用の監査＋ゴールのプロジェクト紐付け・プロジェクト×ゴール進捗一覧
runId: 20260621-143635
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy, monetization]
relatedRunIds: []
commitHashes: [d175c1f]
---

# 2026-06-21 理想運用の監査＋プロジェクト×ゴール進捗一覧

## 1. 作業目的
- ユーザーの理想運用(収益化自動ループ / ゴールは必ずプロジェクト紐付け / プロジェクト×ゴール進捗一覧 / App Store公開=完成表示 / 新アプリ提案 / 運用改善)が実現できているか監査し、不足を埋める。優先度高は今作業。

## 2. 監査結果（理想 vs 現状）
- **ゴールのプロジェクト紐付け: active16中1のみ→15未紐付け(最大の不足)**
- プロジェクト×ゴール進捗一覧: 無し(/goal-dashboardはフラット, /portfolioはPJのみ)
- App Store公開=完成表示: 無し
- 収益化自動ループ/新アプリ提案/運用改善提案: 部分実装(research-goals/monetization-candidates)で実作業・今日の判断連携・新アプリ提案は未
- プロジェクト: 15件(mahjong系重複・停滞あり)

## 3. 是正（今作業・優先度高）
- `lib/goal-writer.ts` `linkGoalProject` + `app/api/goals/link-project`: ゴールをプロジェクトへ紐付け(個別/未紐付け一括)。active16を company-mgmt へ紐付け(16/16紐付け済)。
- `app/project-goals/page.tsx` 新設: プロジェクト別にゴール・達成率(goalAchievement)・優先度を一覧。優先度高ゴールのあるPJを上に。App Store公開仕様到達を推定して「完成」バッジ。未紐付けゴールを警告表示。nav掲載。

## 4. 追加（承認/判断へ）
- ゴール承認(company-mgmt紐付け)6件: 収益化自動ループ完成(P0)/新アプリ候補自動提案(P1)/新規ゴールのプロジェクト紐付け必須化(P1)/App Store完成フラグ導入(P2)/運用改善の定期提案(P2)/自動実行の実装をCodex優先化(P1)。
- 今日の判断(方針選択): プロジェクト整理方針(15件 休止/統合/アーカイブ・appr-1782020129265)。

## 5. 検証結果
- tsc0 / next build0 / lint0。pm2 restart後 /project-goals 200(company-mgmt/実行中ゴール/未紐付け描画)・/legacy に nav掲載。active 16/16 projectId 紐付け確認。
- git push: d175c1f

## 6. 未対応
- 収益化ループ/完成フラグ/紐付け必須化 等は承認後に実装(ゴール承認待ち)。
- proposed/paused の研究系ゴールは未紐付け(承認時にプロジェクト付与する方針)。

## 7. 次にやるべきこと
- ゴール承認の「収益化自動ループ(P0)」を承認して着手。
- 今日の判断の「プロジェクト整理方針」を選択。
- 以降の実装は Codex 優先に委譲して Claude 消費を抑える(方針記録済み)。

## 8. ChatGPT レビュー依頼文
````text
対象: progress プロジェクト×ゴール運用
runId: 20260621-143635 / commit d175c1f
監査=active16中15ゴールが未紐付け。是正=linkGoalProjectでcompany-mgmtへ紐付け＋
/project-goals(プロジェクト別ゴール進捗・App Store完成表示・未紐付け警告)新設。
残りの仕組み(収益化ループ/新アプリ提案/紐付け必須化/完成フラグ/運用改善/Codex優先化)は
ゴール承認、プロジェクト整理は今日の判断へ。確認観点: company-mgmtへの一括紐付けは妥当か
(研究系は別PJが要るか)/App Store完成の判定基準/プロジェクト整理の進め方。
````
