---
date: 2026-06-15
task: 設計レビュー 自走化ゴール最優先＋達成メール通知＋dry-runテスト（Codex実装監督）
runId: 20260615-212004
targetApp: ny01/progress
monetizationImpact: medium
theme: [workflow, app-strategy, prompt-template]
relatedRunIds: [20260613-125025]
commitHashes: []
---

# 2026-06-15 設計レビュー: 自走化ゴール最優先＋達成メール通知＋dry-run

> 実装はしない。設計レビュー＋Codex実装プロンプト監督。Fable担当。

## 結論
条件付きGo。着手前に3点を確定すること:
1. `goal-ai-factory-os`「AI工場OS自走化」(既存・active・main)を**再利用**（新規 factory-autonomy は重複正本）。
2. 自走化の実行は **doneCriteria付き P0・factoryEligible=true の具体Epic**で行う（Goal単体は実行されない＝epic-91と同じ対象外化リスク）。
3. completion=その具体Epicの doneCriteria 達成、と定義（抽象「自走化完成」では永久未達/誤判定。これがメールtrigger）。

## 重大懸念
- **Goal単体は実行されない**（buildAutoQueue/runFactoryはdoneCriteria付きEpicを動かす）。
- **永久ピンによる飢餓**（終わらないゴールがP0固定→他作業全停止）。→ executable候補内の最上位に留める＋連続無進捗で警告。
- **表示と実行の2系統**: buildAutoQueue(/queue表示) と scanFactoryDispatch/runFactory(実行)に**両方**反映しないとズレる（ユーザーの課題そのもの）。
- **factory現在Blocked**（危険承認5件pending）→ 自走化P0でも実行されない。dry-runは wouldRunNext=false を返すべき。

## データ整合
- 選定ログ正本=**ExecutionRun.selection**（selectedGoalKey/Title/WorkItemId/Reason/priority/decisionPolicy/riskFlags/hasFixPrompt/selectedAt）。Goalは lastSelectedRunId/lastSelectedAt/autonomyNotifiedAt の最小ポインタのみ（二重管理回避）。

## dry-runレビュー
- 実選定関数(buildAutoQueue/scanFactoryDispatch)を**read-onlyのまま**呼ぶ（ドリフト防止）。書き込み・メール送信ゼロ。Blocked/disabled時 wouldRunNext=false。「自走化Epicが実行可能か(doneCriteria/factoryEligible)」も確認。

## メール通知レビュー
- email依存なし＝新規。Resend/SMTPいずれか1つに固定（Yahoo宛はSPF/DKIM対応の正規送信推奨）。
- 順序: Epic doneCriteria達成→Goal done永続化→autonomyNotifiedAt未設定なら送信→成功後にautonomyNotifiedAt=now(不可逆)。送信前にフラグを立てない。
- 二重送信防止=autonomyNotifiedAt不可逆＋active→done遷移時のみ評価。dry-runから送らない。
- 失敗は工場を止めない(try/catch)。自動リトライは autonomyNotifyAttempts 上限3＋以後アラートのみ。
- NOTIFY_EMAIL_TO は設定値（秘密でない）。秘密はSMTP/APIキーのみ→.env、.env.exampleは変数名のみ、ログ/Run/Vault/commitに出さない。

## Codexに必ず指示
既存Goal再利用 / 実行は具体Epic / 表示と実行の両系統に反映 / 安全ゲート・review-fix優先・caps温存(自走化はexecutable候補内最上位) / dry-runは実関数read-only・Blocked時false / selectionはExecutionRunに集約 / メールはcompleted永続化後・未通知時のみ・隔離・不可逆フラグ・秘密.env・テストはスタブ。

## Codexに触らせない
deriveWorkItemStatusの危険/判断要ゲート緩和、review-fix/prompt-queueの安全ゲート、自動Epic生成の承認バイパス、危険承認の自動承認、scheduler lock/Blocked判定の作り替え、.env実値commit、秘密のログ出力、本番デプロイ/force push/データ削除、実メール送信テスト。

## Codex完成版プロンプト
（progress 側 ExecutionRun rawReport / 本レビュー応答に全文。目的/前提/変更OK・禁止/role/実装手順10/検証条件/最終報告形式を含む。要点: goal-ai-factory-os再利用・anchorEpicにP0/doneCriteria・buildAutoQueueとscanFactoryDispatch両方にanchor boost・GET /api/operations/factory-autonomy-next-run-selection-test(read-only)・ExecutionRun.selection記録・lib/notifyでcompleted後1回送信・autonomyNotifiedAt不可逆・.env.exampleは変数名のみ・build/lint/tsc/dry-run通す）

## 次にやるべきこと（ユーザー判断待ち）
1. 自走化の具体doneCriteria（=completion=メールtrigger）を定義。
2. メールプロバイダ(Resend/SMTP)を1つ選定し送信元を用意。
3. factory Blocked解除（危険承認5件処理）。
→ 確定後、上記Codexプロンプトで実装依頼。

## ChatGPT レビュー依頼文
```
対象: progress 自走化ゴール最優先＋達成メール通知＋dry-run の設計レビュー（runId 20260615-212004・実装前）
論点: 1.Goalでなく実行Epic必須/既存goal-ai-factory-os再利用の妥当性 2.永久ピン飢餓の防止策(executable候補内最上位＋無進捗警告)で十分か 3.表示(buildAutoQueue)と実行(scanFactoryDispatch)2系統への反映漏れ 4.メールidempotency(autonomyNotifiedAt不可逆)と順序(done永続化→送信→フラグ)に穴がないか 5.completion=具体Epic doneCriteria達成 という定義の妥当性(自走化は本当に"完了"するのか) 6.Codex完成版プロンプトの過不足。
```
