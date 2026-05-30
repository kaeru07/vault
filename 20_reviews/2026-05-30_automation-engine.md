---
date: 2026-05-30
task: Phase2 AI工場エンジン(Automation)強化 / ExecutionRun↔Epic自動結合・/automation統合・Executor設定
runId: 20260530-233519
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260530-215557, 20260530-210159]
commitHashes: []
---

# 2026-05-30 Phase2 AI工場エンジン(Automation)強化

> Progress→Epic→Automation→ExecutionRun→Approval→Decision の流れを完成させる回。Factoryは作らない。新正本ゼロ・追加専用。

---

## 1. 作業目的

- なぜ: AI工場のエンジン(Automation)を完成へ近づける。最大ボトルネックの Claude 上限で止まらない基盤(Executor/Auto Resume/Auto Fallback)を用意する。
- 背景: Phase1でEpic詳細を主画面化済み。次は継続実行基盤の強化。
- 期待効果: Run↔Epicが自動結合し、/automation で実行制御を一元化、Codex切替ポリシーを明文化。

---

## 2. 実施内容

- **ExecutionRun↔Epic結合強化**: `resolveEpicId()` をPOSTに組込み、epicId未指定でも targetApp/targetTodoId から自動結合。
- **operations→automation整理**: `/operations` を `/automation` へ307統合。承認=/approvals、決定=/decisions、Epic進行=/epic、全体ヘルス=Dashboard へ役割分離。
- **Automation画面**: 現在状態(running/paused/blocked)・承認待ち件数・次回実行予定・vloop制御・Executor設定(Claude/Codex/Both)・Auto Resume/Auto Fallbackトグル・Codexポリシー・Factory配置。
- **設定永続化**: `automation-config.json` + GET/POST API。発火(enforcement)は後続。
- **Codex切替ポリシー**: `CODEX_ALLOW_SIGNALS`/`CODEX_DENY_SIGNALS` + `classifyCodexEligibility()` を共有定数化し canRunOnCodex/isRiskyNextAction を統一。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/operations-store.ts | resolveEpicId / classifyCodexEligibility / CODEX_ALLOW・DENY_SIGNALS / get・updateAutomationConfig 追加、判定統一 |
| lib/types/operations.ts | AutomationConfig 型追加 |
| app/api/execution-runs/route.ts | POST時に resolveEpicId で epicId 自動解決 |
| app/api/operations/automation-config/route.ts | 新規 GET/POST |
| app/automation/page.tsx | 新規（自動実行制御専用） |
| app/approvals/page.tsx | 新規（承認待ち横断・決定UI） |
| app/decisions/page.tsx | 新規（Decision Log一覧） |
| app/operations/page.tsx | /automation へ307リダイレクト |
| app/epic/[epicId]/page.tsx | 自動実行状態カードに「制御へ」リンク |
| components/navigation/TopNav.tsx | 自動化/承認タブ追加 |

---

## 4. 検証結果

- typecheck: OK / build: OK（/automation・/approvals・/decisions・automation-config 全登録）/ lint: 0 warnings
- ルート: /automation /approvals /decisions HTTP200、/operations 307→/automation
- automation-config: GET既定値、POST永続化（updatedAt付与）確認
- **epicId自動解決の実証**: runId 20260530-233519 を targetApp=ny01/progress・epicId省略でPOST → epicId=epic-91 が自動付与、Epic詳細に runScope:epic で latestRun として反映。epicId付きRunが1→2へ増加。
- 機密スキャン: OK / ob sync・push: 本応答で実施

---

## 5. 未対応

- Auto Resume / Auto Fallback の**発火ロジック**（設定保存のみ。誤起動防止のため自動実行はまだしない）。
- 130 orphan Run（obsidian-vault等）の結合は、対応Epic作成+targetApps付与が前提（運用で漸進）。
- Approval即決のEpic詳細インライン化。
- Factory（無人連続実行）本体。

---

## 6. 危険ポイント

- 全変更が追加専用。既存155 Run・既存API・既存画面に破壊なし。
- /operations は redirect 化（旧実装は /tmp/operations-page-backup.tsx にバックアップ）。
- Auto Resume/Fallback は設定値の保存のみで自動実行を発火させない（安全側）。
- pm2 はビルド済み .next を配信するため code 変更後は build→restart 必須。

---

## 7. 次にやるべきこと

- Auto Resume/Auto Fallback の発火実装（vloop/executor連携・Claude上限検知）。
- 他アプリの Epic 作成 → targetApps 付与で過去 Run を段階結合。
- Approval即決のEpic詳細インライン化。
- Factory 設計詳細化（Automation との境界＝承認不要部分の無人化）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress
作業: Phase2 AI工場エンジン(Automation)強化
runId: 20260530-233519
関連: 20260530-215557(Phase1), 20260530-210159(設計確定)
日付: 2026-05-30

## 目的
- AI工場のエンジン(Automation)強化。Claude上限で止まらない基盤(Executor/Auto Resume/Auto Fallback)を用意。

## 実装
- ExecutionRun↔Epic自動結合(resolveEpicId: epicId明示→relatedTodoIds→targetApps)。DB変更なし・後方互換。
- /operations→/automation統合(307)。承認/approvals・決定/decisions・Epic/epic・ヘルス=Dashboardへ役割分離。
- Automation画面: 現在状態/vloop制御/Executor設定/Auto Resume/Auto Fallback/Codexポリシー/次回予定/Factory配置。
- automation-config.jsonへ設定永続化(発火は後続)。
- Codex切替を共有定数(ALLOW/DENY)+classifyCodexEligibilityで統一。

## 検証
- tsc/build/lint OK。全ルートHTTP200/307。epicId自動解決を実Runで実証(ny01/progress→epic-91)。

## 確認したい観点
- resolveEpicIdの結合優先順位は妥当か(誤結合リスク)
- /operations redirect統合で失われる機能がないか
- Codex ALLOW/DENYシグナルの過不足(危険作業がCodexへ漏れないか)
- Auto Resume/Fallbackを「設定のみ・発火後続」にした判断は妥当か
- AutomationとFactoryの境界定義は妥当か
````

---

## 関連

- progress runId: 20260530-233519（正本）
- 関連 run: 20260530-215557（Phase1 Epic詳細）/ 20260530-210159（統合路線確定）
- 関連アプリ: [[../02_apps/progress]]
