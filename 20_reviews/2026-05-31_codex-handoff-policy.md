---
date: 2026-05-31
task: Codex引き継ぎ運用方針変更（Executor非依存・状態中心UI・手動引き継ぎを開発者モード限定）
runId: 20260531-174248
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260531-165655, 20260531-155036, 20260531-145153]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 Codex引き継ぎ運用方針変更（Executor非依存・状態中心UI）

> 「ユーザーは Executor を意識しない」を実現する方針変更。手動 Codex 引き継ぎ・手動 executor 選択・AutoFallback 手動上書きを開発者モード限定にし、AutoFallback の自動検知を正規パスに。Automation 最上位に状態中心の Factory 進行状況カードを新設。

---

## 1. 作業目的

- 「Codexへ引き継ぐ」手動ボタンが Factory 完成形の運用（Executor 非依存）と合わないため見直す。
- 理想フロー: Claude実行 → 上限検知 → AutoFallback評価 → Codex実行可能 → Codexプロンプト自動生成 → 通知 → ユーザーは生成済みプロンプトを確認のみ。
- ユーザーは「Claude/Codex どちらで動いているか」ではなく「Factory が進行しているか」だけを見る。

---

## 2. 実施内容

- 手動 Codex 引き継ぎ（CodexHandoffPanel：Automation / Epic詳細）を DevModeGate で開発者モード限定。
- Factory Dispatch の手動 executor 選択（Claude/Codex へ渡すボタン）も開発者モード限定（疎通テスト用）。
- AutoFallback の手動上書きボタンも開発者モード限定。自動検知バナーは常時表示の正規パスとして残す。
- Automation 最上位に FactoryProgressCard（状態中心）を新設。
- DevModeToggle（localStorage progressDevMode）で開発者モード ON/OFF。既定 OFF。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/dev-mode.ts | 新規。useDevMode（localStorage） |
| components/DevModeGate.tsx | 新規。DevModeGate + DevModeToggle |
| lib/factory-status.ts | 新規。computeFactoryStatus（状態中心の派生ビュー） |
| app/api/operations/factory-status/route.ts | 新規。GET=Factory進行状況 |
| components/automation/FactoryProgressCard.tsx | 新規。状態中心カード |
| app/automation/page.tsx | 最上位にカード・手動操作を開発者モード限定・トグル追加 |
| components/codex/AutoFallbackPanel.tsx | 手動上書きを開発者モード限定 |
| app/epic/[epicId]/page.tsx | CodexHandoffPanel を開発者モード限定 |

---

## 4. 検証結果

- typecheck / build / lint: すべて OK（/api/operations/factory-status 登録）
- 手動確認: factory-status → state=停止中（autoFallback/autoResume OFF→factoryOn false・停止理由表示）/ claudeStatus=none / executor=codex(参考)。/automation 既定（dev OFF）→ 手動 Codex 引き継ぎは SSR 非表示・DevModeToggle 表示・Auto Fallback 自動検知は残る。epic-91 200。データ変更なし。
- 機密チェック: OK

---

## 5. 確認したいこと 5点への回答（推奨）

1. **手動「Codexへ引き継ぐ」は削除・非表示・開発者モード限定のどれが適切か** → **開発者モード限定（推奨・採用）**。削除すると疎通テスト/デバッグ手段を失う。完全非表示だと再表示の導線が無い。開発者モード限定なら通常 UX から消えつつ、疎通テスト時に復活できる。コードは温存。

2. **どの画面にどの状態表示を置くか** → 最上位の状態は **Automation の FactoryProgressCard**（Factory進行状況）と **/epic の FactoryStatusBar**（現在地）。Epic詳細は Epic Contract + 自動実行の状態 + 承認即処理。Executor は「参考表示」に格下げ、per-Epic の executor 選択 UI は出さない。

3. **完全自動疎通テスト時に手動 Codex 引き継ぎ UI が邪魔にならないか** → 既定 OFF で非表示なので通常運用では邪魔にならない。疎通テストは開発者モード ON で手動 Dispatch を出し1周 → OFF に戻して状態中心運用、という切替で両立。

4. **AutoFallback→CodexReady→結果戻しでユーザー操作を最小化できるか** → 現状は「自動検知（AutoFallback自動発火）→ codex_ready 表示 → ユーザーはプロンプト確認/コピー → 実行 → 結果戻し」。コピーと結果戻しは手動コピー運用のため残る。さらに最小化するなら push 通知 + 結果戻しの dispatchPlanId 自動補完（実装済み）+ ワンタップ起動の導線が候補。

5. **完全自動 Factory へ進むための不足（運用面・UI面・技術面）**:
   - 運用面: Factory ON/OFF の専用スイッチ（現在は AutoResume/AutoFallback の合成）。停止条件（サーキットブレーカ）の運用合意。最初の手動疎通テスト1周の実施。
   - UI面: Epic詳細への簡易進行表示。push 通知。結果戻しのワンタップ化。
   - 技術面: Factory ループ本体（scan→pick→dispatch→record→loop）。executor 抽象 pickExecutor の共通化。executor の実起動手段（半自動なら通知＋人トリガ）。Factory 専用 ExecutionRun source（factory_loop）と可観測性。

---

## 6. 危険ポイント

- 既存ロジック（evaluateAutoFallback / classifyCodexEligibility / decideApproval / Dispatch判定）は不変・呼ぶだけ。
- 新正本なし（Factory進行状況は派生ビュー）。完全自動実行・CLI直叩き・cron/pm2/systemd なし。
- DevModeGate は表示制御のみ（権限・セキュリティ境界ではない）。

---

## 7. 次にやるべきこと

- 完全自動 Factory 疎通テスト（開発者モード ON で手動 Dispatch 1周 → OFF で状態中心運用を確認）。
- Factory ON/OFF 専用 config と Factory ループ本体。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: Codex引き継ぎ運用方針変更（Executor非依存・状態中心UI・手動引き継ぎを開発者モード限定）
runId: 20260531-174248
日付: 2026-05-31
GitHub commit: (progressアプリのコードは未commit / Vaultレビューのみ反映)

## 作業目的
- ユーザーが Executor を意識しないよう、手動 Codex 引き継ぎを開発者モード限定にし、AutoFallback 自動検知を正規パスに。状態中心 UI へ。

## 実施内容
- 手動 Codex 引き継ぎ / 手動 executor 選択（Factory Dispatch）/ AutoFallback 手動上書き → 開発者モード限定。
- Automation 最上位に状態中心の Factory 進行状況カード（state/Claude利用状況/Fallback/実行待ち/承認待ち/停止理由/次回予定）。
- Executor は参考表示（内部状態）に格下げ。

## 検証結果
- typecheck/build/lint OK。factory-status endpoint・dev OFF で手動UI非表示・自動検知は残る・epic-91 200。データ変更なし。

## 確認したい観点
- 手動引き継ぎを「開発者モード限定」にした判断は妥当か（削除/非表示と比較）
- 状態中心カードの状態優先順位（実行中>承認待ち>停止中>再開待ち>Codex準備完了）は妥当か
- Executor を参考表示に格下げした方針は Factory 完成形と整合するか
- ユーザー操作最小化（コピー/結果戻し）の次の打ち手は何が良いか
- 完全自動 Factory への不足（運用/UI/技術）の整理は妥当か
````

---

## 関連

- progress runId: 20260531-174248（正本）
- 関連 run: 20260531-165655（Factory Dispatch準備）, 20260531-155036（Auto Resume/Approval即処理）
- Factory設計: [[../06_research/factory-orchestration-design]]
- 関連アプリ: [[../02_apps/progress]]
