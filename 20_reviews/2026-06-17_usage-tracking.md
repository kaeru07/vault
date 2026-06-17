---
date: 2026-06-17
task: progressの使用状況を記録・可視化する /usage ページ新設（画面別アクセス/ボタン操作TOP/最終使用/放置検知）
runId: 20260617-220624
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260617-134311]
commitHashes: [c6108ab]
---

# 2026-06-17 progress 使用状況ページ（/usage）新設

> 1 作業 = 1 ファイル の自動生成レビュー。
> ユーザー要望「progressアプリの使用を細かくprogressアプリに記述して把握できるようにしたい」→ 選択肢提示で「実際の使用状況を記録・可視化」を選択。

---

## 1. 作業目的

- なぜこの作業をするのか: Progress 自身がどう使われているか（どの画面をよく開き・どのボタンをよく押し・最後にいつ使い・どの画面を放置しているか）を把握できるようにするため。
- 背景: 機能が増え画面・タブが多数になり、実際に使われている画面と放置されている画面の判別が必要。
- 期待効果: 使用実態に基づく画面整理・導線改善の判断材料。放置画面＝不要画面の見直し。

---

## 2. 実施内容

- 横断キャプチャ方式を採用（既存の各画面・各ボタンを改修しない）:
  - layout 常駐の `UsageTracker`（client）が `usePathname` 変化で `page_view`、document 委譲クリックで `button`/`[role=button]` の `action`（文言80字）を記録。
  - `navigator.sendBeacon`（fallback: fetch keepalive）で `POST /api/usage` → `usage-store.ts` が `usage-log.ndjson`（PROGRESS_DATA_PATH 配下・既存ログと独立）へ追記。
- `/usage` ページ（server component・force-dynamic）で `buildUsageSummary(7)` を表示:
  - サマリー（画面表示数 / ボタン操作数 / 最後に使った日時）
  - 画面別アクセス回数（直近7日・多い順・棒グラフ）
  - よく使うボタン操作 TOP（直近7日・最大20）
  - 画面別 最終使用日時（全期間・新しい順）
  - 放置している画面（直近7日 未アクセスの登録画面）
- 画面レジストリ正本 `lib/usage-screens.ts`（ルート→人間語・詳細ページは親に丸め）。
- 導線: BottomNav「使用状況」/ TopNav(legacy)「使用状況」。
- `data-usage-ignore`（記録抑止）/ `data-usage-label`（文言固定）で個別制御可能。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/usage-screens.ts | 新規。画面レジストリ canonicalScreen/screenLabel |
| progress/lib/usage-store.ts | 新規。appendUsageEvent/readUsageEvents/buildUsageSummary |
| progress/app/api/usage/route.ts | 新規。POST 使用イベント記録API |
| progress/components/usage/UsageTracker.tsx | 新規。layout常駐トラッカー(page_view/action) |
| progress/app/usage/page.tsx | 新規。使用状況の集計表示 |
| progress/app/layout.tsx | UsageTracker マウント |
| progress/components/navigation/BottomNav.tsx | /usage 導線追加 |
| progress/components/navigation/TopNav.tsx | /usage 導線追加(legacy) |
| progress/lib/command-center.ts | TERMS に usage 追加 |
| progress/app/guide/page.tsx | FAQ 追加 |
| progress/docs/operations/current-operating-model.md | 使用状況節/用語表/frontmatter/変更履歴 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit 0。当初 Map iterator spread が target 制約で TS2802 → Array.from に修正して解消）
- build: OK（next build 0。/usage・/api/usage 生成）
- lint: n/a
- 手動確認: pm2 restart 後 /usage 実描画 HTTP200・全セクション描画・参照チャンク200・白画面/エラーなし。/api/usage に page_view×2 / action×1 POST → usage-log.ndjson に3行追記を確認 → /usage 集計に反映（自動実行画面の回数・「最優先」ボタン操作が表示）を確認。
- 機密パターン事前チェック: OK（ヒットなし）
- ob sync: n/a（ob sync 一旦中止中）
- git push: c6108ab（origin/main へ push 済み）

---

## 5. 未対応

- 実ブラウザでの自動記録（sendBeacon 経由）はサーバ側 POST で同等動作を確認済みだが、headless 実ブラウザでのクリック→記録は未実施（curl POST で代替検証）。
- usage-log.ndjson のローテーション/上限は未実装（肥大時の対応は要観察）。
- ボタン文言ベースの集計のため、同じ意味でも文言違いは別集計になる（必要なら data-usage-label で安定化）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。新規ファイル中心＋ layout に1コンポーネント追加のみ。既存画面・ボタン・実行ロジック・データスキーマは不変更。記録失敗は握りつぶす設計で画面動作を止めない。
- DB / 認証 / 本番 / 機密に触れたか: なし。記録は UI 文言（画面パス・ボタン文言）のみ。単一ユーザーの自分用ダッシュボード前提。
- ロールバック手段: commit c6108ab の revert（usage-log.ndjson はランタイムデータで未コミット）。
- 観察すべき項目: ログ肥大、全クリックキャプチャによるノイズ過多（ThemeToggle 等も action 記録される）。

---

## 7. 次にやるべきこと

- フォローアップ: 数日運用して TOP 集計の有用性・ノイズ量を観察。ノイズ過多なら除外（data-usage-ignore）や記録対象の絞り込み。
- usage-log.ndjson のサイズ上限/ローテーション要否の判断。
- 主要操作ボタンへの data-usage-label 付与で文言安定化（任意）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: ny01/progress
作業: progressの使用状況を記録・可視化する /usage ページ新設
runId: 20260617-220624
日付: 2026-06-17
GitHub commit: c6108ab

## 作業目的
- Progress 自身の使われ方(よく開く画面/よく押すボタン/最終使用/放置画面)を把握できるようにする。

## 実施内容
- layout常駐のUsageTrackerがpage_view(usePathname)とaction(button/[role=button]のdocument委譲クリック)を横断キャプチャ。
- sendBeacon→POST /api/usage→usage-store.tsでusage-log.ndjsonへ追記。
- /usageでbuildUsageSummary(7)を表示: 画面別アクセス回数(棒グラフ)/よく使うボタン操作TOP/画面別最終使用日時/放置画面。
- 既存画面・ボタンの改修なし。表示専用で判定・実行に非干渉。

## 変更ファイル
- 新規: lib/usage-screens.ts, lib/usage-store.ts, app/api/usage/route.ts, components/usage/UsageTracker.tsx, app/usage/page.tsx
- 編集: app/layout.tsx, BottomNav.tsx, TopNav.tsx, lib/command-center.ts(TERMS), app/guide/page.tsx, docs/operations/current-operating-model.md

## 検証結果
- typecheck/build: tsc0 / next build0
- 手動: /usage実描画200・全セクション描画・白画面なし。/api/usageにPOST→usage-log.ndjson追記→/usage集計反映を確認。
- 機密スキャン: clean

## 未対応
- 実ブラウザのsendBeacon自動記録はcurl POSTで代替検証(headless実クリック未実施)。
- usage-log.ndjsonのローテーション/上限なし。
- 文言違いは別集計。

## 危険ポイント
- 新規中心＋layoutに1コンポーネント。既存ロジック/スキーマ不変更。記録失敗は握りつぶし画面を止めない。全クリックキャプチャのノイズ(ThemeToggle等も記録)。

## 確認したい観点
- 横断キャプチャ(document委譲click)方式の妥当性・副作用(他のclickハンドラ干渉やパフォーマンス)
- sendBeacon送信とAPI記録の信頼性/取りこぼし
- ログ肥大対策(ローテーション/上限)の必要性と設計
- 全button記録のノイズをどこまで絞るべきか(除外設計)
- プライバシー観点(UI文言記録)の妥当性
- 収益化インパクト評価は妥当か(low)
````

---

## 関連

- progress runId: 20260617-220624（正本）
- 関連 run: 20260617-134311（同日の goal-auto-advance）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
