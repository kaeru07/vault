---
date: 2026-05-31
task: Phase3 Codex半自動引き継ぎ（プロンプト生成・モバイルコピー・結果戻し）
runId: 20260531-002500
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260530-233519, 20260530-215557, 20260530-210159]
commitHashes: []
---

# 2026-05-31 Phase3 Codex半自動引き継ぎ

> Claude 上限で止まったら Codex へ手動でスムーズに引き継ぐ。完全自動切替・CLI直叩き・外部連携(Hermes)は使わない。新正本ゼロ・追加専用。

---

## 1. 作業目的

- なぜ: 最大ボトルネックの Claude 上限で作業が止まったとき、Progress/vloop が Codex 用プロンプトを生成し、ユーザーがモバイルで貼るだけで続行できる状態にする。
- 背景: Phase2 で Executor 設定・Codex ポリシーは用意済み。今回は実際に「引き継げる」導線を完成。
- 期待効果: Claude 上限 → Codex 続行 → 結果を ExecutionRun へ戻す、の半自動ループ。

---

## 2. 実施内容

- `generateCodexPrompt(epicId?)`: 既存正本（ExecutionRun/Decision/NextActions/Approval/Epic/変更ファイル/未完了）を集約し**単一プレーンテキスト**を生成（markdownフェンス・長JSONを含まない）。
- 冒頭に `CODEX_SAFETY_GUARD`（Codex 側の安全判定指示）を固定挿入。禁止該当時は blocked / approval_required で報告させる。
- UI: Epic詳細[次回予定]に「Codexへ引き継ぐ」パネル、Automation に「Codexへ引き継ぐ」+「Codex結果を戻す」フォーム。
- 結果戻し: `CodexReportForm` → `/api/execution-runs`（executorUsed=codex / source=codex_mobile / epicId自動解決）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| types/execution-run.ts | `source?` 追加（codex_mobile 記録用） |
| lib/types/operations.ts | `CodexPrompt` 型追加 |
| lib/operations-store.ts | `generateCodexPrompt` + `CODEX_SAFETY_GUARD` |
| app/api/operations/codex-prompt/route.ts | 新規 GET（?epicId） |
| app/api/execution-runs/route.ts | POSTで `source` 受理 |
| components/codex/CodexHandoffPanel.tsx | 新規（Codexへ引き継ぐ） |
| components/codex/CodexReportForm.tsx | 新規（結果戻し） |
| app/epic/[epicId]/page.tsx | 次回予定にパネル追加 |
| app/automation/page.tsx | 引き継ぎ/結果戻しセクション追加 |

---

## 4. 検証結果

- typecheck: OK / build: OK（codex-prompt API 登録）/ lint: 0 warnings
- ルート: /automation・/epic/epic-91 HTTP200
- codex-prompt（epic-91）: hasSafetyGuard=true、` ``` ` 不含、1628字、meta（元Run=20260530-233519 / 次5件 / 承認0 / targetApp=progress）
- **結果戻しE2E**: codex_mobile を POST → executorUsed=codex / source=codex_mobile / epicId=epic-91 自動付与を確認 → テストRun(20260531-002405)を削除し155件へ復元（データ非汚染）
- 機密スキャン: OK / ob sync・push: 本応答で実施

---

## 5. 未対応

- Codex報告の**構造化自動パース**（今は本文をrawReportへそのまま格納）
- **Auto Fallback の発火**（Claude上限検知→自動生成・通知）
- Codex結果戻しの Epic詳細インライン配置
- Factory 成果物台帳（今回作らない）

---

## 6. 危険ポイント

- 全変更が追加専用。既存155 Run・既存API・既存画面に破壊なし。
- Codex 自動起動なし・CLI直叩きなし・外部連携(Hermes)不使用。
- 未承認 / Decision待ち / destructive はプロンプト安全判定＋フォーム運用で渡さない。
- handoff 用語は UI 非表示（「Codexへ引き継ぐ」表記）。
- execution-runs.json は E2E 後 `JSON.stringify(,null,2)` で再整形（データファイル・コミット対象外）。

---

## 7. 次にやるべきこと

- Auto Fallback 発火実装（Claude上限検知 → Codexプロンプト自動生成 → 通知）。
- Codex報告の構造化パース（変更ファイル/検証結果の自動抽出）。
- 確認したいこと（確認1〜7）への回答は本文＋下記レビュー依頼に集約済み。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress
作業: Phase3 Codex半自動引き継ぎ（プロンプト生成・モバイルコピー・結果戻し）
runId: 20260531-002500
関連: 20260530-233519(Phase2), 20260530-215557(Phase1)
日付: 2026-05-31

## 目的
- Claude上限で止まったらCodexへ手動でスムーズに引き継ぐ半自動切替。完全自動・CLI直叩き・Hermesは使わない。

## 実装
- generateCodexPromptが既存正本から単一プレーンテキスト(安全判定付き・入れ子フェンスなし・長JSONなし)を生成。
- Epic詳細/AutomationにCodexへ引き継ぐパネル、AutomationにCodex結果戻しフォーム(executorUsed=codex/source=codex_mobile)。
- 安全判定を冒頭固定(禁止該当はblocked/approval_requiredで報告)。

## 検証
- tsc/build/lint OK。codex-prompt安全判定付き1628字・```不含。結果戻しE2E(codex_mobile/executorUsed=codex/epicId自動)確認後テストRun削除。

## 確認したい観点
- Codexプロンプトの安全判定リストに過不足はないか(危険作業漏れ)
- 単一プレーンテキスト/モバイルコピーの形式は実用的か
- 結果戻しの最小フォーム(targetApp/title/report/runStatus)で十分か
- executorUsed=codex/source=codex_mobileの記録設計は妥当か
- Auto Fallback発火を後回しにした判断は妥当か
````

---

## 関連

- progress runId: 20260531-002500（正本）
- 関連 run: 20260530-233519（Phase2 Automation）/ 20260530-215557（Phase1 Epic詳細）
- 関連アプリ: [[../02_apps/progress]]
