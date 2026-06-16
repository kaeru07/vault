---
date: 2026-06-16
task: Progress全画面にユーザー操作可能なURL同期フィルターUIを追加（Phase1 Codex実装／Phase2 Fableレビュー close_ok）
runId: 20260616-090603
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260616-002003]
commitHashes: [cb56917]
---

# 2026-06-16 Progress 表示専用フィルターUI（Phase1 Codex / Phase2 Fableレビュー）

> 目的: Progressアプリ全体で「ユーザーが画面上から操作でき、URLにも反映されるフィルターUI」を追加し、毎日の絞り込み・共有・復元を容易にする。**判定/スコア/Factory実行順は一切変えない（表示専用）**。結論 **close_ok**。

## 1. 作業目的
- Progress は「人間用の司令塔」。queue/Inbox が増えると目的の項目を探すコストが上がる。
- 画面上で絞り込み→URLに反映→再読み込み・共有リンクで復元、という基本動線を全画面に通す。
- ただしフィルターは**表示専用**。自動実行の候補判定・スコア・実行順に触れてはいけない（触ると工場挙動が変わる事故になる）。

## 2. 実施内容
- **Phase1（Codex実装）**: 表示専用フィルター基盤＋各ページ適用。
  - `lib/progress-filters.ts`（新）: `ProgressFilterState` 型 / `parseProgressFilters` / `buildProgressFilterUrl` / `updateFilterParam` / `clearFilters`。
  - `components/newux/FilterBar.tsx`（新）: ドロワー型の詳細フィルター（Goal/Project/app/priority/q）。
  - `components/newux/FilterChips.tsx`（新）: iPhone横スクロールchip＋「選択中 … ×クリア」表示。
  - `components/newux/InboxTabs.tsx`: tab/reviewStatus/goalId/q/fixPrompt をURLへ書き戻し。
  - `/queue`（pin/excluded/manual/executor/Goal/Project/app/priority/q）, `/tasks`, `/goal-planner`, `/portfolio` に適用。`/guide`・運用docへ説明追記。
- **Phase2（Fable独立レビュー）**: 下記「検証結果」。1点も実装是正不要（判定系無変更を確認）。close_ok。

## 3. 変更ファイル
- `lib/progress-filters.ts`（新）
- `components/newux/FilterBar.tsx`（新）
- `components/newux/FilterChips.tsx`（新）
- `components/newux/InboxTabs.tsx`（URL書き戻し）
- `app/queue/page.tsx` / `app/tasks/page.tsx` / `app/goal-planner/page.tsx` / `app/portfolio/page.tsx` / `app/guide/page.tsx`
- `docs/operations/current-operating-model.md`（「表示フィルターとURL同期」セクション＋frontmatter更新）
- ※無関係leftover（ai-trend-sns等）・`data/real` はコミット対象外。

## 4. 検証結果
- tsc 0 / lint 0 / build 成功。
- **CRITICAL（表示専用の担保）**: `git status` で判定/スコア系（auto-queue-score / auto-queue / factory-dispatch / factory-runner / factory-schedule / review-fix-runner / prompt-queue-runner）に**変更なし**を確認。
- **deep link 互換 9件 全200**: `/decide?tab=review&goalId=goal-ai-factory-os&focusRunId=20260615-212004`, `/decide?tab=review&filter=needs_followup`, `?reviewStatus=needs_followup`, `/queue`系, `/tasks?filter=todo&q=AI`, `/goal-planner?filter=active&q=AI`, `/portfolio?filter=ok&q=Progress`。
- **フィルタ実効性（全1件中の表示数）**: `/queue`=1件表示 / `?excluded=1`(候補外)=0件＋空状態ガイド（クリア/候補外を見る/pin済みを見る）/ `?filter=executable`=1件＋「選択中 状態: executable ×クリア」チップ / `?q=nomatch`=0件＋空状態＋「検索: … ×クリア」。件数表示「全N件中M件表示」・URL復元・×クリアを確認。
- 機密スキャン clean。commit `cb56917`（ny01 main）push済。

## 5. 未対応
- live queue item が現状 epic-91 の1件のみのため、データが増えたときの ToDo/Goal/Project 側フィルターの大規模挙動は未観察。
- iPhone実機でのchip横スクロール＋ドロワー操作性は構造確認のみ（実機タップ未確認）。

## 6. 危険ポイント
- フィルターは表示層のみ。**もし将来 `deriveWorkItemStatus`/queueScore/`scanFactoryDispatch` 側へフィルター文脈が漏れると、表示＝実行の一致が崩れ自動実行候補が変わる**。今回はそこに変更がないことを git diff で確認済み。
- DB/認証/課金/本番への影響なし。実データ物理削除なし。

## 7. 次にやるべきこと
- データ増加時に各画面フィルターの件数・空状態・URL復元が破綻しないか継続観察。
- iPhone実機での操作性（chip横スクロール、ドロワー開閉、×クリア）を確認。

## 8. ChatGPT レビュー依頼文
```
対象: progress 表示専用フィルターUI（runId 20260616-090603 / commit cb56917・Fableレビューclose_ok）
変更: lib/progress-filters.ts(新), components/newux/FilterBar.tsx(新), components/newux/FilterChips.tsx(新), components/newux/InboxTabs.tsx, app/{queue,tasks,goal-planner,portfolio,guide}/page.tsx, docs/operations/current-operating-model.md
背景: Progress全画面に「画面操作でき・URLに同期し・再読込/共有で復元できる」表示専用フィルター(チップ+ドロワー+検索+件数+空状態ガイド)を追加。判定/スコア/Factory実行順は不変。
観点:
1. フィルターが本当に表示専用に閉じているか(候補判定・スコア・実行順への副作用の有無)。
2. 既存deep link(?tab/goalId/focusRunId/filter=needs_followup/reviewStatus)の後方互換が壊れていないか。
3. URL同期の設計(parse/build/update/clear)に状態取りこぼし・XSS/インジェクション懸念がないか。
4. 0件時の空状態ガイド(クリア/候補外を見る/pin済みを見る)の導線が実用的か。
5. iPhone横スクロールchip+ドロワーの操作性・到達性。
```
