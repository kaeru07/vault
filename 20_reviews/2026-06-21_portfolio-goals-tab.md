---
date: 2026-06-21
task: プロジェクト×ゴール進捗をProjectタブ内の新規タブに統合
runId: 20260621-173720
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: []
commitHashes: [7e4abfe]
---

# 2026-06-21 プロジェクト×ゴール進捗をProjectタブに統合（Codex委譲）

## 1. 作業目的
- ユーザー要望「project-goals をプロジェクトタブの中に新規タブで追加」。

## 2. 実施内容（Codex委譲・Claude検証）
- `components/projects/ProjectGoalsView.tsx` に本体を切り出し。
- `app/portfolio/page.tsx` に「案件一覧 / ゴール進捗」タブバー。`?tab=goals` で ProjectGoalsView 表示。
- `app/project-goals/page.tsx` は ProjectGoalsView を包むだけに簡素化。
- `lib/nav-menu.ts` の該当エントリを `/portfolio?tab=goals` へ。

## 3. 変更ファイル
| ファイル | 変更 |
|---|---|
| components/projects/ProjectGoalsView.tsx | 新規(Codex) |
| app/portfolio/page.tsx | タブバー(Codex) |
| app/project-goals/page.tsx | ラッパー化(Codex) |
| lib/nav-menu.ts | href更新 |

## 4. 検証結果
- tsc0/build0/lint0・/portfolio 200・/portfolio?tab=goals 200(company-mgmt描画)・git push 7e4abfe

## 5-7. 未対応/危険/次
- 表示専用。実機でタブ切替確認推奨。

## 8. ChatGPT レビュー依頼文
````text
対象: progress /portfolio ゴール進捗タブ / runId 20260621-173720 / commit 7e4abfe
project-goalsをProjectタブの新規タブ(?tab=goals)に統合。実装Codex委譲・Claude検証(両タブ200)。
````
