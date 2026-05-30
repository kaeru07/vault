---
date: 2026-05-31
task: Phase4 Auto Fallback発火（Claude上限→厳格安全判定→Codexプロンプト自動生成・通知）
runId: 20260531-015908
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260531-002500, 20260530-233519]
commitHashes: []
---

# 2026-05-31 Phase4 Auto Fallback発火

> Claude 上限時に Codex へ安全に半自動引き継ぎできる状態を完成。完全自動 Codex 起動・CLI直叩き・外部連携(Hermes)はしない。新正本は増やさない。

---

## 1. 作業目的

- なぜ: Claude 上限で AI工場が止まったとき、安全な作業だけを Codex へ手動で渡せるよう「発火」を実装する。
- 背景: Phase3 で Codex プロンプト生成・結果戻しは実装済み。今回は「上限検知 → 安全判定 → 自動生成 → 通知」を接続。

---

## 2. 実施内容

- `evaluateAutoFallback(epicId, reason)` / `triggerAutoFallback`（評価＋Automation Log追記）。
- 厳格な多層安全ゲート（優先順・keyword は補助）。
- 安全なら `generateCodexPrompt` を自動生成、不可なら blocked 理由を返す。
- Automation に「Claude上限として扱う」ボタン＋codex_ready/blocked 表示＋ログ。
- `automation-log.ndjson`（追記専用イベントログ）に fallback を記録。

---

## 3. Codex 禁止条件の強化（優先順）

| 順 | 判定 | ブロック種別 |
|---|---|---|
| 0 | autoFallback OFF / executorMode が both・codex でない | disabled |
| 1 | 承認待ち（approvals.json pending） | approval_required |
| 2 | epic.decisionPolicy ≠ autonomous | decision_required |
| 3 | 対象作業 status = pending_approval | requires_approval |
| 4 | 対象作業 requiresClaude | requires_claude |
| 5 | 次アクションの deny/allow（classifyCodexEligibility・**最後の補助**） | destructive / no_codex_candidate |

文字列判定は補助。1つでもブロックがあれば `status=blocked`。

---

## 4. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/types/operations.ts | AutoFallbackResult / FallbackBlock / AutomationLogEntry |
| lib/operations-store.ts | evaluateAutoFallback / triggerAutoFallback / getScopedOpenTasks / append・getAutomationLog、generateCodexPrompt[7]に executorUsed=codex 指示 |
| app/api/operations/auto-fallback/route.ts | 新規 POST |
| app/api/operations/automation-log/route.ts | 新規 GET |
| components/codex/AutoFallbackPanel.tsx | 新規 |
| app/automation/page.tsx | Auto Fallback セクション |

---

## 5. 検証結果（実測）

- typecheck OK / build OK（auto-fallback・automation-log API 登録）/ lint 0 warnings
- **OFF** → blocked（disabled）
- **実 epic-91** → blocked（requires_approval＝progress に pending_approval タスク3件、destructive＝候補に「削除」）＝**正当なブロック**
- **クリーン一時epic（安全な nextAction）** → **codex_ready**：gen=true、safetyGuard=true、プロンプトに安全判定冒頭あり・` ``` `不含・executorUsed=codex 指示あり
- Automation Log 記録確認。検証用の一時 run / epic は削除しデータ復元（epics=epic-91 のみ）。
- 機密スキャン OK / ob sync・push 本応答で実施

---

## 6. blocked 時の表示

「Codexへは渡せません」＋ ブロック種別（approval_required / decision_required / requires_approval / requires_claude / blocked(危険作業) / blocked(候補なし) / 設定OFF）と理由。Codexプロンプトは生成しない。

---

## 7. Automation Log 記録内容

automation-log.ndjson に：fallbackTriggered / fallbackReason(claude_rate_limited) / fallbackTarget(codex) / codexPromptGenerated / codexPromptSourceRunId / safetyGuard / blockedReason / epicId。

---

## 8. 未対応

- Claude 上限の**自動検知**（現状は手動ボタン。将来 vloop 終了理由 / rate limit エラー文 / runStatus=failed のパースで置換）
- blocked 時の Approval/Decision ワンタップ導線
- codex_ready の push 通知 / バッジ
- Auto Resume（安全作業の自動再開）発火

---

## 9. 危険ポイント

- 全て追加専用。既存破壊なし。Codex 自動起動なし・CLI直叩きなし・Hermes不使用。
- 多層ゲートで 承認待ち / 決定待ち / requiresClaude / pending_approval / 危険シグナルは Codex へ渡さない。
- handoff 用語は UI 非表示。Factory 成果物台帳は作っていない。

---

## 10. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress
作業: Phase4 Auto Fallback発火（Claude上限→安全判定→Codexプロンプト自動生成・通知）
runId: 20260531-015908
関連: 20260531-002500(Phase3), 20260530-233519(Phase2)
日付: 2026-05-31

## 目的
- Claude上限時にCodexへ安全に半自動引き継ぎ。完全自動起動はしない。

## 安全ゲート(優先順)
- 設定OFF → 承認待ち → decisionPolicy → pending_approval → requiresClaude → keyword(補助)。
- 1つでもブロックなら blocked。安全時のみ Codexプロンプト自動生成。

## 検証
- OFF→blocked、実epic-91→blocked(requires_approval/destructive=正当)、クリーン→codex_ready(安全判定付き・```不含・executorUsed=codex)。Automation Log記録。検証用temp削除。

## 確認したい観点
- ゲートの優先順・網羅性は十分か(危険作業の漏れ)
- keyword補助の閾値(「削除」でdestructive)は厳しすぎ/緩すぎないか
- Claude上限の自動検知方針(vloopログ/エラー文/runStatus)は妥当か
- Automation Logの項目は十分か
- 次の一手(自動検知 vs Auto Resume)の優先度
````

---

## 関連

- progress runId: 20260531-015908（正本）
- 関連 run: 20260531-002500（Phase3 Codex引き継ぎ）/ 20260530-233519（Phase2 Automation）
- 関連アプリ: [[../02_apps/progress]]
