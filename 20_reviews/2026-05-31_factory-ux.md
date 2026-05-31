---
date: 2026-05-31
task: 工場ページ(AI工場)のUX改善 - 使い方ガイド/現在地表示/ボタン説明/blocked導線
runId: 20260531-152019
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260531-145153, 20260531-015908]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 工場ページ(AI工場)のUX改善

> 機能は揃っているが初見ユーザーが運用方法を理解しづらい工場(/epic)・Epic詳細・Automationに、
> 説明導線と現在地表示を追加。既存機能・判定ロジック・保存仕様は変更しない方針で実施。

---

## 1. 作業目的

- なぜこの作業をするのか: 工場ページは機能が揃っているが「続きから実行 / Codexへ引き継ぐ / 承認待ち / Claude上限時の動作」が初見では分かりづらい。
- 背景: 直前で Claude上限自動検知（runId 20260531-145153）まで実装が進み、UI に状態が増えた。運用導線を整える段階。
- 期待効果: 初見ユーザーが「今なにをすればいいか」を一目で把握でき、各ボタン/状態の意味と Claude上限時の流れを理解できる。

---

## 2. 実施内容

- 主な変更（箇条書きで具体的に）:
  - 工場の使い方ガイド（FactoryGuideModal）: ボトムシート型モーダル。依頼指定の文面どおり「工場とは / 普段の流れ / 続きから実行 / Codexへ引き継ぐ（※自動起動しない）/ 承認待ち / よくあるケース（Claude上限→Codex）」。variant=icon/text。
  - 現在地表示（FactoryStatusBar）: 既存API（health / automation / claude-limit GET）を読むだけで、優先度判定し 🔴Claude上限 / 🟡承認待ち / 🟢作業中 / 🟣Codex引き継ぎ可能 / ⚪待機 を1枚で表示。各状態に一言ガイド＋CTA遷移。
  - ボタン説明: 「続きから実行」→『Claudeへ続きを依頼するための情報をまとめます』、「Codexへ引き継ぐ」→『Claude上限時にCodexへ作業を引き継ぐためのプロンプトを作ります』。
  - blocked導線: AutoFallbackPanel の blocked 表示を「なぜ止まっているか」＋理由別ワンタップ遷移（承認/Decision/設定）に改善。
  - 運用導線: /epic 上部にインフォカード「ここはAI作業を管理する場所です」＋使い方ガイド。Epic詳細にも ? アイコンを配置。
- 関連調査・判断:
  - 工場ページ＝ /epic（/operations は /automation へ redirect 済み）。Epic詳細＝/epic/[epicId]。AutoFallbackPanel は /automation と共有のため blocked導線は両画面に効く。
  - 現在地の判定は新ロジックを作らず既存API結果の読み取りに限定（変更禁止範囲を侵さないため）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| components/epic/FactoryGuideModal.tsx | 新規。工場の使い方ボトムシートモーダル（6セクション）。variant=icon/text |
| components/epic/FactoryStatusBar.tsx | 新規。現在地表示。health+automation+claude-limit から優先度判定＋CTA。判定ロジックは新設せず既存API結果を読むのみ |
| app/epic/page.tsx | 工場トップに現在地表示・運用導線インフォカード・使い方ガイドボタンを追加 |
| app/epic/[epicId]/page.tsx | 使い方ガイド（iconボタン）追加。続きから実行/Codex引き継ぎのボタン説明文を更新 |
| components/codex/AutoFallbackPanel.tsx | blocked表示を「なぜ止まっているか」＋理由別ワンタップ遷移に改善。判定ロジック不変 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit クリーン）
- build: OK（npm run build 成功・/epic 2.91kB / /epic/[epicId] 2.69kB）
- lint: OK（next lint 警告0）
- 手動確認: pm2 restart 後 curl
  - /epic → 200・「AI工場 / いまの状態 / ここはAI作業を管理する場所です / 工場の使い方」描画
  - /epic/epic-91 → 200・2つのボタン説明文描画
  - /automation → 200・Auto Fallback / 自動検知バナー描画
  - 現在地は実データ（detection=none, pending=0, running=0, canFallbackToCodex=true）で 🟣Codex引き継ぎ可能 に解決
- モバイル表示: ボトムシートmodal（items-end sm:items-center）・flex/gap・レスポンシブ文字サイズでモバイル優先
- 機密パターン事前チェック: OK（実トークン無し）
- ob sync: Fully synced
- git push: 本レビュー md / vault ミラーのみ（progressアプリのコードは未commit・本文参照）

---

## 5. 未対応

- スキップした項目: 使い方モーダルの初回自動表示（localStorage）。Epic詳細への現在地表示追加は判断保留。
- 環境制約で実行できなかったこと: 承認待ち/Claude上限が実際に発生した時の現在地表示の見え方は実機未確認（合成データでの状態分岐は確認済み）。
- ユーザー判断待ち: progressアプリのコード5ファイルは working tree に未commit（コードレビュー後の commit/push 要否はユーザー判断）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。新規2コンポーネント＋既存3ファイルへの追加/文言変更のみ。Auto Fallback / Codex安全判定 / Approval / Decision / ExecutionRun保存仕様は1行も変更していない（変更禁止範囲を遵守）。
- DB / 認証 / 本番 / 機密に触れたか: なし。読み取りのみ。新規APIゼロ（既存API流用）。
- ロールバック手段: 新規2ファイル削除＋3ファイルの追加分 revert で完全復旧。
- 観察すべき項目: 現在地の優先度順（🔴>🟡>🟢>🟣>⚪）が実運用の体感と合うか。blocked遷移先が正しいか。

---

## 7. 次にやるべきこと

- フォローアップ作業: 使い方モーダルの初回自動表示。実上限/承認待ち発生時の現在地表示の実機確認。
- ユーザー判断待ち事項: progressコードの commit/push 要否。Epic詳細への現在地表示の追加可否。
- 観察項目（数日〜数週間）: 初見ユーザーが工場の流れを理解できるか。
- 関連 ToDo の追加候補: Automationページにも現在地表示を出すか。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: 工場ページ(AI工場)のUX改善 - 使い方ガイド/現在地表示/ボタン説明/blocked導線
runId: 20260531-152019
日付: 2026-05-31
GitHub commit: (progressアプリのコードは未commit / Vaultレビューのみ反映)

## 作業目的
- 工場(/epic)・Epic詳細・Automationの初見UXを改善。続きから実行/Codexへ引き継ぐ/承認待ち/Claude上限時の動作を分かりやすくする。

## 実施内容
- 工場の使い方モーダル（FactoryGuideModal）追加。
- 現在地表示（FactoryStatusBar）: 既存API(health/automation/claude-limit)から🔴Claude上限/🟡承認待ち/🟢作業中/🟣Codex引き継ぎ可能/⚪待機を判定し一言ガイド+CTA。
- ボタン説明文の更新（続きから実行 / Codexへ引き継ぐ）。
- blocked表示を「なぜ止まっているか」+理由別ワンタップ遷移（承認/Decision/設定）に改善。
- /epic上部に運用導線インフォカード追加。

## 変更ファイル
- components/epic/FactoryGuideModal.tsx（新規）
- components/epic/FactoryStatusBar.tsx（新規）
- app/epic/page.tsx / app/epic/[epicId]/page.tsx
- components/codex/AutoFallbackPanel.tsx

## 検証結果
- typecheck / build / lint: すべて OK。
- 手動確認: /epic・/epic/epic-91・/automation 全て200+新要素描画。現在地は実データで🟣Codex引き継ぎ可能に解決。
- 変更禁止範囲（Auto Fallback/Codex安全判定/Approval/Decision/ExecutionRun保存仕様）は不変・新規APIゼロ・既存機能削除なし。

## 未対応
- 使い方モーダルの初回自動表示。実上限/承認待ち時の現在地表示の実機確認。progressコードのcommit/push（ユーザー判断）。

## 危険ポイント
- 追加中心で判定ロジック不変。

## 次にやるべきこと
- 初回自動表示、実機での状態別表示確認。

## 確認したい観点
- 現在地表示の状態優先順位（🔴>🟡>🟢>🟣>⚪）は妥当か
- 説明文・モーダル内容が初見ユーザーに分かりやすいか
- blocked遷移先のマッピングは正しいか
- 既存機能・判定ロジックを壊していないか
- モバイル表示の崩れがないか
````

---

## 関連

- progress runId: 20260531-152019（正本）
- 関連 run: 20260531-145153（Claude上限自動検知 / 現在地🔴の判定元）, 20260531-015908（Auto Fallback発火）
- 関連アプリ: [[../02_apps/progress]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
