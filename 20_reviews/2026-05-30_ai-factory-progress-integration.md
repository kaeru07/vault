---
date: 2026-05-30
task: AI工場設計を統合路線へ転換 / handoff内部化・Progress中心設計
runId: 20260530-210159
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: []
commitHashes: []
---

# 2026-05-30 AI工場設計を統合路線へ転換 / handoff内部化・Progress中心設計

> 設計レビュー（実装変更ゼロ・方針確定）。実装はユーザー承認後に Phase0 から着手予定。

---

## 1. 作業目的

- なぜ: AI工場を「別システム新設」ではなく「ProgressアプリをAI工場へ進化させる」統合路線に確定したい。
- 背景: 現状 `/operations`（工場オペレーション）と `/ai-drive`（AI自走）が Progress に並ぶ独立プロダクトに見え、handoff など内部用語がユーザーUIに露出している。
- 期待効果: ユーザーは前回作業／決定事項／次回予定／承認待ちの4観点だけを見れば回る。正本は4つに固定し、機能分離より統合を優先。

---

## 2. 実施内容

- 現行実装を調査し、データ層では既に handoff が派生ビュー化されている事実を確認（`generateHandoffView()` / source:generated / 「正本ではない」明記 / forbiddenに「handoffを独立した正本にしない」）。→ 正本問題ではなくIA問題と特定。
- handoff が2系統あることを特定: (1) `session.handoffText`（自由文・ユーザー編集可・/queue HandoffEditor→/morning表示）＝準正本としてUI露出している本体、(2) `generateHandoffView()` 派生ビュー。
- 統合設計案（Progress中心・正本4固定・operations/ai-drive をProgress配下へ解体）を10成果物として作成。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| （なし） | 設計レビューのみ。実装変更ゼロ。 |

---

## 4. 検証結果

- typecheck / build / lint: n/a（実装変更なし）
- 手動確認: 現行コード読解（operations-store / operations page / ai-drive / nav / handoff route）
- 機密パターン事前チェック: OK（ルール例示・パス名のみ、実値なし）
- ob sync: 本応答で実行
- git push: 本応答で mirror→commit→push

---

## 5. 未対応

- 実装（Phase0〜4）は未着手。ユーザー承認後に Phase0（UIラベル撤去）から。
- handoff ユーザー向け名称の最終確定はユーザー判断待ち（推奨は「名詞destination化せず『続きから実行』アクション」）。

---

## 6. 危険ポイント

- 既存機能への影響: Phase0 はUIラベル撤去のみで非破壊。Phase1以降で `session.handoffText` 廃止時は /queue・/morning の参照を派生ビューへ差し替える必要あり（影響範囲: HandoffEditor / morning / queue）。
- DB / 認証 / 本番 / 機密: 触れない。
- ロールバック: 各Phaseを独立commitにし段階的に戻せるようにする。
- 観察項目: ナビ統合後にユーザーが従来導線を見失わないか。

---

## 7. 次にやるべきこと

- Phase0: `/operations` ページの「handoff」h3見出しを撤去 or 「継続実行情報（内部）」へ改名。data/APIは不変。
- Phase1: Epic詳細画面 `/epic/[epicId]` 新規。既存 operations API（epics/approvals/decisions/next-actions/handoff）を集約し「続きから実行」プロンプトコピーを設置。
- ルール追記候補: CLAUDE系に「handoffはUI主要概念にしない / 正本はVault・Progress・ExecutionRun・Decision Logの4つ固定」を明文化。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の設計レビュー報告です。レビューしてください。

対象アプリ: progress
作業: AI工場設計を統合路線へ転換 / handoff内部化・Progress中心設計
runId: 20260530-210159
日付: 2026-05-30

## 目的
- AI工場を別システムにせず Progress を AI工場へ進化させる。handoff をユーザー向け主要概念から外し内部ビュー化。正本は Vault/Progress/ExecutionRun/Decision Log の4つに固定。

## 現状
- データ層は既に handoff 派生済み（generateHandoffView, source:generated, 「正本ではない」明記）。
- handoff 2系統: session.handoffText（自由文・UI露出）と generateHandoffView（派生）。
- /operations と /ai-drive が独立プロダクト化、/ai-drive は大半モック。
- ユーザーが欲しい Epic詳細画面（前回/決定/次回/承認/履歴）が未実装。

## 設計案
- handoff は内部API維持・UIラベル撤去・「続きから実行」アクション化。
- session.handoffText（準正本）は廃止し派生ビューへ寄せる。
- Progress を単一シェル化: Dashboard/Epic/ToDo/Approval/Decision/ExecutionRun/Automation/Factory(将来)。
- Epic詳細=縦串、全Approval/全Decision/全Run=横串受信箱。

## 確認したい観点
- handoff 内部化で executor(Claude/Codex)再開文脈が劣化しないか
- session.handoffText 廃止の影響範囲（/queue, /morning）の見落としがないか
- Progress中心への統合でナビ過密が解消されるか / ユーザーが導線を見失わないか
- Codex連携(/goal・汎用JSON API)を壊さない設計になっているか
- 実装優先順位(Phase0→4)は妥当か
````

---

## 関連

- progress runId: 20260530-210159（正本）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
