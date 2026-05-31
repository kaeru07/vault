---
date: 2026-05-31
task: Claude上限自動検知の完成（手動ボタン依存を廃止し3ログから自動判定）
runId: 20260531-145153
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260531-015908, 20260531-002500]
commitHashes: []
# reviewFileCommit:   # 任意。本ファイル自身の push commit を残したい場合に手動で埋める
---

# 2026-05-31 Claude上限自動検知の完成

> Auto Resume 実装前の前提整備。「Claude上限として扱う」手動ボタンに依存していた発火を、
> vloop実行ログ / ExecutionRun / Automation Log からの自動判定に置換した（手動ボタンは force 上書きとして残置）。

---

## 1. 作業目的

- なぜこの作業をするのか: Auto Fallback / Auto Resume は「Claude が上限で止まった」ことを検知できないと自動化できない。これまでは人が手動ボタンを押す前提だった。
- 背景: 直前の Phase4（runId 20260531-015908）で Auto Fallback 発火を実装したが、上限検知は「確実検知が難しい」として手動ボタンで保留していた。その run の rawReport に将来の自動検知候補（vloopログ終了理由 / ExecutionRun runStatus=failed+errors の rate/limit/429/quota / Claude CLI 上限文言）が設計として残されていた。
- 期待効果: 上限を自動判定し、検知時のみ既存の厳格な安全判定（Auto Fallback）に橋渡しする。Auto Resume へ進む前提を満たす。

---

## 2. 実施内容

- 主な変更（箇条書きで具体的に）:
  - `lib/claude-limit-detector.ts` 新規。3ログを走査して上限シグナルを収集し、重み付き判定（high/medium/low/none）を返す `detectClaudeLimit()`。
  - `app/api/operations/claude-limit/route.ts` 新規。GET=検知のみ（副作用なし・UIポーリング用）、POST=検知→検知ログ追記→`trigger_fallback` 推奨 or `force` で既存 `triggerAutoFallback` を実行。
  - 型追加（`ClaudeLimitDetection` 他）と `AutomationLogEntry` に `claude_limit_detection` 用フィールドを後方互換で追加。
  - `appendDetectionLog()` で検知の足跡を automation-log.ndjson（追記専用）へ記録。
  - UI: AutoFallbackPanel に自動検知バナーを追加。`detected` のとき自動で1回評価。手動ボタンは「force 上書き」として残置。
- 関連調査・判断:
  - 実データ調査: execution-runs.json に「上限」が38回出現するが、すべて rawReport/summary/title の**話題語**（この機能自体の設計メモ）。runStatus は completed×152 / partial×7 / **failed×0**。errors[] に上限文言を持つ run は無し。
  - 判断: ExecutionRun は **errors[] / fallbackReason / runStatus の構造化フィールドのみ走査**し、rawReport/summary/title は見ない。これが「話題語『上限』での誤検知」を構造的に防ぐ最重要ガード。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/claude-limit-detector.ts | 新規。LIMIT_PATTERNS / matchLimit / detectClaudeLimit / decide。3ログ走査・時間窓180分既定 |
| app/api/operations/claude-limit/route.ts | 新規。GET=検知のみ / POST=検知→ログ→trigger_fallback or force で triggerAutoFallback |
| lib/types/operations.ts | ClaudeLimitDetection 等の型追加。AutomationLogEntry に detection 用フィールド追加（任意化・後方互換） |
| lib/operations-store.ts | appendDetectionLog 追加。evaluateAutoFallback / triggerAutoFallback は未変更 |
| components/codex/AutoFallbackPanel.tsx | 自動検知バナー＋detected時の自動評価。手動ボタンは force 上書きとして残置 |
| app/automation/page.tsx | Auto Fallback セクション説明文を自動検知前提に更新 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit クリーン）
- build: OK（next build 成功・/api/operations/claude-limit 登録確認）
- lint: OK（next lint 警告0）
- 手動確認: curl 7ケース実測（実データは pm2 progress :3010 / data/real）
  - T1 GET既定窓(180分) → none（実データ12h前・話題語『上限』38件で誤検知ゼロ）
  - T2 GET超広窓(100000分) → ambiguous（automation-log の過去 claude_rate_limited × 4 が low）
  - T3 合成 failed+「rate limit 429 usage limit reached」 → detected / high / trigger_fallback
  - T4 合成 generic failed（上限語なし） → ambiguous / block_for_review
  - T5 POST(detected) → autoTriggered=true・fallback=blocked(disabled, requires_approval)＝**既存安全ゲートに到達**
  - T6 POST(ambiguous) → autoTriggered=false・fallback=null（block_for_review 厳守）
  - T7 POST force=true(ambiguous) → autoTriggered=true（手動上書き機能）
- 機密パターン事前チェック: OK（ルール例示のみ・実トークン無し。excerpt は160字に短縮）
- ob sync: Fully synced
- git push: 本レビュー md / vault ミラーのみ（progress アプリのコード commit は未実施・本文「未対応」参照）

検証後、合成データは /tmp バックアップから完全復元（execution-runs.json=159 runs / automation-log.ndjson=4 行 / 最終 GET=none を再確認）。

---

## 5. 未対応

- スキップした項目: Auto Resume 本体（本タスクは検知の完成のみ。明示的に未実装）。push 通知。block_for_review 時の Approval/Decision ワンタップ導線。
- 環境制約で実行できなかったこと: 実データに上限実イベント（failed+limit文言）が存在しないため、正例は合成 run で検証（検証後に復元）。
- ユーザー判断待ち: **progress アプリのコード変更（6ファイル）は working tree に未 commit**。コードレビュー後の commit/push 要否はユーザー判断（Vault は反映済み）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。すべて追加。`evaluateAutoFallback` / `triggerAutoFallback`（Auto Fallback の安全ゲート）は**1行も変更していない**。検知はその**前段**に乗るだけ。
- DB / 認証 / 本番 / 機密に触れたか: なし。読み取り中心。検知 excerpt は機密混入を避けるため短縮。
- ロールバック手段: 新規2ファイル削除＋型/store/UI の追加分 revert で完全に元へ戻る（既存ロジック非依存）。
- 観察すべき項目:
  - 誤判定リスク(a) rawReport話題語 → 構造化フィールド限定で回避済み（T1で実証）。
  - 誤判定リスク(b) generic failed（build失敗等）を上限と誤認 → low→ambiguous=block で自動発火させない（T4/T6で実証）。
  - 誤判定リスク(c) stop_reason の「上限/制限」単独語（例: スコープ制限）→ low 扱いで block。和文単独語の精度は要観察。
  - 誤判定リスク(d) automation-log 自己エコー → low（単独では発火しない）。
  - 誤判定リスク(e) 古い上限イベント → 時間窓180分で除外。

---

## 7. 次にやるべきこと

- フォローアップ作業: Auto Resume 実装（detected→安全な作業の自動再開。安全ゲートは流用）。
- ユーザー判断待ち事項: progress コードの commit/push 要否。VLOOP_LOG_DIR を env で本番値に固定するか。
- 観察項目（数日〜数週間）: stop_reason 和文曖昧語の誤検知有無。実際に上限が起きた時の errors[] 文言フォーマット（Claude CLI の実メッセージ）を採取してパターン精度を上げる。
- 関連 ToDo の追加候補: 実際の上限発生時ログを1件採取して LIMIT_PATTERNS を実測校正。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の管制塔 / Next.js / port 3010）
作業: Claude上限自動検知の完成（手動ボタン依存を廃止し vloop実行ログ/ExecutionRun/Automation Log から自動判定）
runId: 20260531-145153
日付: 2026-05-31
GitHub commit: (progressアプリのコードは未commit / Vaultレビューのみ反映)

## 作業目的
- Auto Resume 実装の前提として、Claude が上限で止まったことを自動検知する層を作る。
- 従来の手動ボタン「Claude上限として扱う」依存を廃止（ボタンは force 上書きとして残置）。

## 実施内容
- detectClaudeLimit(): 3ログを走査し重み付き判定（high/medium/low/none）。
  - ExecutionRun は errors[]/fallbackReason/runStatus の構造化フィールドのみ走査（rawReport/summary/title は見ない＝話題語「上限」での誤検知防止）。
  - vloopログ frontmatter の stop_reason。
  - Automation Log の過去 claude_rate_limited（裏取り・低シグナル）。
- API: GET=検知のみ / POST=検知→検知ログ追記→trigger_fallback推奨 or force で既存 triggerAutoFallback を実行。
- 安全ゲート（evaluateAutoFallback）は未変更。検知はその前段に追加。

## 判定ロジック
- パターン一致 + runStatus=failed → high / 一致のみ → medium / failedだが上限語なし → low / stop_reason一致 → high・上限制限単独 → low / automation-log一致 → low。
- high>=1 or medium>=1 → detected → trigger_fallback。lowのみ → ambiguous → block_for_review（誤判定回避）。無 → none。
- 時間窓 既定180分（上限~5hリセット / 古い誤発火回避）。

## 検証結果
- typecheck / build / lint: すべて OK。
- 手動確認: curl 7ケース（none / ambiguous / detected-high / POST自動発火→既存ゲート到達 / block_for_review / force上書き）。
- 機密スキャン: OK（excerpt 160字短縮）。検証後データは159runs/automation-log4行へ完全復元。

## 未対応
- Auto Resume 本体（本タスクは検知のみ）。push通知。progressコードのcommit/push（ユーザー判断）。

## 危険ポイント
- 安全ゲート不変・全て追加。実データに上限実イベントが無く正例は合成で検証。

## 次にやるべきこと
- Auto Resume 実装。実上限発生時の Claude CLI 実メッセージ採取で LIMIT_PATTERNS を校正。

## 確認したい観点
- 検知ルール（high/medium/low の重み付けと閾値）は妥当か
- 誤判定（特に generic failed と stop_reason 和文単独語）の扱いは安全側に倒せているか
- ExecutionRun を構造化フィールド限定で走査する設計は十分か（取りこぼしリスク）
- 既存 Auto Fallback の安全ゲートに副作用がないか
- 時間窓180分は適切か
- 次に Auto Resume へ進んでよいか
````

---

## 関連

- progress runId: 20260531-145153（正本）
- 関連 run: 20260531-015908（Phase4 Auto Fallback発火 / 自動検知の設計元）
- 関連アプリ: [[../02_apps/progress]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
