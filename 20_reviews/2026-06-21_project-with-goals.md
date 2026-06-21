---
date: 2026-06-21
task: プロジェクト＋ゴール手動一括追加(JSON)機能＋自動実行対象テスト
runId: 20260621-183407
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: [0efb800]
---

# 2026-06-21 プロジェクト＋ゴール手動一括追加(JSON)＋自動実行対象テスト

## 1. 作業目的
- ユーザー要望: プロジェクトを手動追加し、キューにも入れて優先調整、ゴールも一緒にJSONテンプで追加、追加したPJ/ゴールが次回自動実行の対象になるかテスト。

## 2. 実施内容（Codex委譲・Claude検証）
- `app/api/projects/with-goals/route.ts`(新規): body `{project, goals[]}` で `addProject` + 各goalを `upsertSingleGoal(status:'active', projectId)`。重複projは追記続行。
- `app/projects/import/page.tsx`(新規): JSONテンプレ貼り付けフォーム→POST、結果と /queue・/portfolio?tab=goals リンク。
- `lib/nav-menu.ts`「計画・候補」に `/projects/import` 追加。

## 3. JSONテンプレ
```json
{
  "project": { "id": "my-new-app", "name": "新規アプリ名", "status": "active", "currentTask": "MVP実装", "nextAction": "最初の画面を作る" },
  "goals": [
    { "title": "MVPを実装する", "priority": "high", "prompt": "最初に作る中心機能" },
    { "title": "App Store公開仕様にする", "priority": "medium", "prompt": "公開準備" }
  ]
}
```

## 4. 検証結果（Claude・実テスト）
- tsc0/build0/lint0・/projects/import 200。
- POST /api/projects/with-goals(autoexec-test-proj+ゴール2)→200(projectCreated/goalsCreated2)。project存在true・ゴール2件 active+todo1。
- /api/auto-queue executable に todo:2件(**factoryEligible:true / candidateEligible:true** / priority P0,P1)=**次回自動実行の対象**。factory-run dry_run 200。
- prioritize(test todo)→200=優先調整可。
- 後片付け: テストゴール=dropped・テストプロジェクト=archived。

## 5-7. 未対応/危険/次
- 実機で /projects/import から追加→/queue で優先調整を確認推奨。破壊操作なし。

## 8. ChatGPT レビュー依頼文
````text
対象: progress プロジェクト+ゴール一括追加 / runId 20260621-183407 / commit 0efb800
/projects/importでJSONからプロジェクト+activeゴールを一括追加。実テストで追加ゴールが
queueにexecutable/factoryEligible/candidateEligible(次回自動実行対象)・prioritize可を確認。
実装Codex委譲・Claude実テスト検証。確認観点: active即時化(承認スキップ)の運用妥当性/重複proj時の追記挙動。
````
