---
date: 2026-05-31
task: AI工場 次フェーズ - Auto Resume実装 / Approval即処理 / Factoryオーケストレーション設計
runId: 20260531-155036
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260531-152019, 20260531-145153, 20260531-015908]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-31 AI工場 次フェーズ（Auto Resume / Approval即処理 / Factory設計）

> AI工場完成に向けて Phase1 Auto Resume・Phase2 Approval即処理を実装し、Phase3 Factory は設計のみ。
> Progress を唯一の管制塔とし、新しい正本を作らず、executor 非依存・handoff 非正本を遵守。

---

## 1. 作業目的

- なぜこの作業をするのか: 工場の機能・UXが揃ったので、止まらず回す中核（Claude上限後の自動継続＝Auto Resume）と、スマホ即決（Approval即処理）を入れ、無人運用の上位設計（Factory）を固める。
- 背景: Claude上限自動検知・AutoFallback・現在地表示までが直近で完成（runId 145153 / 152019）。次は「止まったら安全作業だけ継続」と「承認を即決」。
- 期待効果: Epic詳細を中心に、Claude上限が来ても安全作業は継続、承認はその場で即決でき、Factory実装の設計図が揃う。

---

## 2. 実施内容

- Phase1 Auto Resume（実装）:
  - `evaluateAutoResume` / `triggerAutoResume`。安全判定は既存 `evaluateAutoFallback` を再利用し、feature-toggle の `disabled` ブロックだけ除外して安全ゲート（承認待ち/決定待ち/requires_approval/requires_claude/destructive/no_codex_candidate）を採用。ゲートは変更しない。
  - state = running / paused(OFF) / blocked / auto_resumed。再開対象件数・最終再開時刻（AutomationLog から導出）。
  - 記録先 = ExecutionRun（source=auto_resume）+ Automation Log（event=auto_resume）。新正本なし。
  - executor 非依存: `pickResumeExecutor(config)` が rate-limited でない有効 executor を返す（both/codex→codex、claude専任→不可）。
  - Automation ページに状態パネル（state/件数/最終再開/再開ボタン/プロンプトコピー）。
- Phase2 Approval即処理（実装）:
  - Epic詳細の承認待ちを読み取り専用 → `InlineApprovalPanel`（選択肢A/B/C＋推奨バッジ＋理由、承認/却下/保留）に置換。
  - 既存 `/api/operations/approvals` を呼ぶだけ。Decision Log 保存は `decideApproval` が自動実行（次回 vloop は `buildDecisionContext` で自動反映）。
  - `/approvals` は横断受信箱として維持。
- Phase3 Factory（設計のみ）:
  - `docs/factory-orchestration-design.md`: 状態遷移図、対象/対象外、AutoResume/Automation/Approval/Decision との境界、Claude/Codex/Both 運用フロー、DecisionPolicy 標準化検討、残作業7項目。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/auto-resume.ts | 新規。evaluateAutoResume/triggerAutoResume。AutoFallbackゲート再利用・executor非依存・ExecutionRun+AutomationLog記録 |
| app/api/operations/auto-resume/route.ts | 新規。GET=評価/POST=再開トリガ |
| components/automation/AutoResumePanel.tsx | 新規。状態/件数/最終再開時刻/再開ボタン/プロンプトコピー |
| components/epic/InlineApprovalPanel.tsx | 新規。Epic詳細の即承認/却下/保留（既存decide API流用） |
| lib/types/operations.ts | AutoResume型追加・AutomationLogEntryにauto_resume追加（後方互換） |
| app/automation/page.tsx | AutoResumePanel配置＋トグル説明文更新 |
| app/epic/[epicId]/page.tsx | 承認待ちをInlineApprovalPanelへ置換 |
| docs/factory-orchestration-design.md | 新規（設計のみ）。Factoryオーケストレーション設計 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit クリーン）
- build: OK（npm run build 成功・/api/operations/auto-resume 登録）
- lint: OK（next lint 警告0）
- 手動確認（pm2 restart 後 curl・実データ data/real）:
  - Auto Resume A: OFF → paused
  - Auto Resume B: ON（実データ）→ blocked[requires_approval, no_codex_candidate]（安全ゲートが正しくブロック）
  - Auto Resume C: clean tmp epic（autonomous/safe nextAction）→ running / canResume / 件数3 / executor=codex / context生成
  - Auto Resume D: POST → auto_resumed / lastResumedAt設定 / ExecutionRun(source=auto_resume, executor=codex) + AutomationLog 1件
  - フロー「Claude上限→AutoFallback→codex_ready→AutoResume→再開」確認
  - Phase2: Epic詳細にインライン承認描画（その場で即処理/認証方式/推奨/却下）、decide(推奨B)→success/decided、Decision Log保存（認証方式→B, epicId付き）
- 機密パターン事前チェック: OK
- 検証後の復元: 161 runs / epics 1 / approvals 0 / auto_resume runs 0 / automation-log 4行 / decisions 0 / autoResume=false（tmp全削除）
- ob sync: Fully synced
- git push: 本レビュー md / 設計md / vault ミラー（progressアプリのコードは未commit・本文参照）

---

## 5. 未対応

- スキップした項目: Factory 本体（指示どおり設計のみ）。Auto Resume の executor 実起動（half-auto のため再開準備＋記録まで。実行は executor が担う）。
- 環境制約: 実データは安全ゲートで blocked のため、正例は tmp epic/run で検証し復元。
- ユーザー判断待ち: progressアプリのコード8ファイルは working tree に未commit（コードレビュー後の commit/push 要否はユーザー判断）。DecisionPolicy 3値標準化の確定。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。Auto Fallback / Codex安全判定 / Approval判定 / Decision判定 / ExecutionRun保存仕様は1行も変更せず、呼び出すだけ。新規APIは読み取り＋限定追記のみ。
- DB / 認証 / 本番 / 機密: なし。Auto Resume の ExecutionRun 追記は既存 addExecutionRun を使用（保存仕様不変）。
- ロールバック手段: 新規4ファイル削除＋4ファイルの追加分 revert で復旧。
- 観察すべき項目: Auto Resume の ExecutionRun(source=auto_resume) がログ画面で過剰に増えないか（トリガ時のみ生成）。再開対象件数の精度（global候補ベース）。

---

## 7. 次にやるべきこと

- フォローアップ: Factory ループ本体（scan→pick→dispatch→record→loop）。executor抽象 pickExecutor(config, limitedSet) を Auto Resume と共通化。
- ユーザー判断待ち: DecisionPolicy 3値標準化（autonomous/approval_required/manual）の確定。progressコードの commit/push。
- 観察項目: Auto Resume を実運用 ON にしたときの挙動。Approval即処理のモバイル操作感。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: AI工場 次フェーズ - Auto Resume実装 / Approval即処理 / Factoryオーケストレーション設計
runId: 20260531-155036
日付: 2026-05-31
GitHub commit: (progressアプリのコードは未commit / Vaultレビューのみ反映)

## 作業目的
- Claude上限後に安全作業だけ自動継続（Auto Resume）と、スマホ即決（Approval即処理）を実装。Factoryは設計のみ。

## 実施内容
- Phase1 Auto Resume: 既存AutoFallback安全ゲートを再利用（feature-toggleのdisabledのみ除外）。state=running/paused/blocked/auto_resumed、再開対象件数・最終再開時刻、ExecutionRun+AutomationLog記録、executor非依存(pickResumeExecutor)。
- Phase2 Approval即処理: Epic詳細でインライン承認/却下/保留→既存decide API→Decision Log自動保存→次回vloop反映。/approvalsは横断受信箱維持。
- Phase3 Factory: 設計docのみ（状態遷移図/対象・対象外/4境界/Claude・Codex・Both運用/DecisionPolicy標準化/残作業）。

## 変更ファイル
- lib/auto-resume.ts（新規）, app/api/operations/auto-resume/route.ts（新規）
- components/automation/AutoResumePanel.tsx（新規）, components/epic/InlineApprovalPanel.tsx（新規）
- lib/types/operations.ts, app/automation/page.tsx, app/epic/[epicId]/page.tsx
- docs/factory-orchestration-design.md（新規・設計のみ）

## 検証結果
- typecheck / build / lint: すべて OK。
- Auto Resume: OFF→paused / ON実データ→blocked(safety) / clean scope→running→POST→auto_resumed+ExecutionRun+AutomationLog。
- Approval即処理: Epic詳細インライン承認→Decision Log保存(epicId付き)。検証後tmp全復元。

## 危険ポイント
- 既存の安全判定/Approval/Decision/ExecutionRun保存仕様は不変・呼ぶだけ。

## 確認したい観点
- Auto Resume が既存ゲートを再利用する設計（disabledのみ除外）は妥当か
- executor非依存の抽象化（pickResumeExecutor）は将来executor追加に耐えるか
- Auto Resume時にExecutionRunを1件作る記録方法は適切か（過剰ログにならないか）
- Approval即処理の承認/却下/保留マッピングは妥当か
- Factory設計の境界（AutoResume/Automation/Approval/Decision）は正しく分離できているか
- DecisionPolicy 3値標準化（autonomous/approval_required/manual）案は妥当か
- Progress唯一正本・新正本なしの原則を守れているか
````

---

## 関連

- progress runId: 20260531-155036（正本）
- 関連 run: 20260531-152019（工場UX改善）, 20260531-145153（Claude上限自動検知）, 20260531-015908（AutoFallback発火）
- Factory設計: [[../06_research/factory-orchestration-design]]
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
