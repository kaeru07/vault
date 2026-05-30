---
title: AI工場オペレーションセンター 設計レビュー（Epic #91）
type: monetization-design-review
issue: kaeru07/vault#91
created: 2026-05-30
updated: 2026-05-30
userDecisions:
  route: /operations（A案・単一集約）
  approvalPriority: critical/high/low（3段階）
  decisionLog: 別ファイル分離（A案・operational-decisions.ndjson）
  autoExecution: MVP最優先（Phase2送りではなく最初から含める）
status: review（設計レビューのみ・実装しない）
relatedIssues: [kaeru07/vault#42, kaeru07/vault#50, kaeru07/vault#87]
tags: [progress, automation, vloop, approval, epic, mobile]
---

# AI工場オペレーションセンター 設計レビュー（Epic #91）

> Epic [#91](https://github.com/kaeru07/vault/issues/91) の設計レビュー。**実装しない。設計レビューのみ。**
> 目的: ProgressアプリをAI工場の管制塔にする。モバイル（Claude/Codex）から選択式で承認・継続実行できる運用基盤を、自動実行より先に固める。
> 前提方針: API/課金なし・ローカルJSON・**既存スキーマ非破壊（追加のみ）**（[[progress連携基準]] / [[アプリ案工場_Phase1設計]] 踏襲）。

---

## 0. 調査した既存構造（整合性の前提）

| 領域 | 正本 | 要点 |
|---|---|---|
| Progress アプリ | [[../02_apps/progress]] | Next.js / ローカルJSON。`app-progress.json`(案件) `project-tasks.json`(タスク) `work-queue.json`(今日のキュー) `work-log.ndjson` / ExecutionRun は `POST /api/execution-runs` |
| ToDo 正本 | [[../20_reviews/案件別ToDo一覧]] / [[../20_reviews/案件別ToDo運用ルール]] | 状態: open/planned_only/in_progress/artifact_exists/user_check/done/merged/blocked |
| vloop 実行対象 | [[../20_reviews/vloop_queue]] | `[x]`=実行承認済/承認不要、`[ ]`=承認待ち。`[x]` を止めずに最後まで実行 |
| Epic 正本 | [[epics]] / [[../03_prompts/Epic単位運用]] | Epic A/B/C/D を目的・完了条件・現状で管理（**narrative・構造化フィールドなし**） |
| 承認 | [[../20_reviews/ChatGPT承認待ち]] / [[../90_templates/chatgpt-approval-template]] | 既に「推奨案＋比較候補」形式（モバイル選択式の土台あり）。ただし長文 |
| 判断履歴 | [[decision-log]] / [[../90_templates/承認判断テンプレート]] | **収益化シナリオの承認判断（人間のみ）**専用。技術判断用ではない |
| 停滞検知 | [[../20_reviews/2026-05-17_progress-stagnation-indicator]] | `getStagnation()`/`sortByAttention()` 実装済。aging≥3日 / stalled≥7日 |

**重要な整合点**: #91 の「Decision Log」例（JWT採用 / Clerk不採用 / Supabase継続）は**技術・運用の判断**であり、既存 `decision-log.md`（収益化シナリオの承認判断・人間のみ）とは**別物**。混同すると正本責務が壊れる（§G6）。

---

## 1. 現行設計レビュー（#91 の評価）

| # | #91 の項目 | 評価 | コメント |
|---|---|---|---|
| 1 | vloop運用の統合可視化（Vault/Issue/queue/tasks/ExecutionRun） | 🟡 方向OK・粒度不足 | 関係整理の意図はあるが、Progress上で何を表示するか未定義。§4 で画面化 |
| 2 | 自動実行管理ページ（/automation・/operations） | 🟡 表示は良・**集計なし** | 状態表示（実行中/停止中/上限待ち/エラー/一時停止）はあるが**件数ダッシュボード**がない（§G4） |
| 3 | 承認待ち管理（A案/B案選択） | 🟡 例示のみ | 選択式の例はあるが「長文入力前提にしない」が**明文化されていない**（§G5）。優先度もなし（§G3） |
| 4 | Decision Log | 🟡 二層未分離 | 運用判断ログを既存 `decision-log.md` と混同するリスク（§G6） |
| 5 | 継続実行ポリシー | 🟢 良好 | 停止条件リストは妥当。既存 [[../20_reviews/vloop_queue]] §停止条件・[[epics]] と整合。§6 で停止条件を確定 |
| 6 | Epic属性（Decision Policy / Auto Approval Rule） | 🟡 一部のみ | Policy は定義済。だが **Goal/Progress/Remaining Work/Latest ExecutionRun/Next Action が欠落**（§G1） |
| 7 | 将来対応（VPS再開・上限回復後再開・成果物台帳） | 🟢 後回し明示 | 運用基盤優先の判断は妥当。§7 へ |

**総評**: 方針（管制塔化・継続実行優先・運用基盤先行）は正しい。不足は「**Progress上で扱うデータ構造と画面の具体**」と「**モバイル選択式承認の明文化**」。実装ではなくまず構造を確定すべき段階。

---

## 2. 不足点一覧（Gap List）

| ID | 不足点 | #91の現状 | 影響 | 対応(§) |
|---|---|---|---|---|
| **G1** | Epic構造化フィールド | Decision Policy / Auto Approval Rule のみ | Epicの進捗・残作業・直近Run・次の1手がProgressから見えず、自動実行中に情報分散で停止 | §3.1 |
| **G2** | ToDo `source` 未定義 | 「AIが次ToDo生成」と書くのみ | 誰が出したToDoか追跡不能。AI生成ToDoの暴走/重複検知ができない | §3.2 |
| **G3** | Approval 優先度未定義 | なし | モバイルで「今すぐ判断すべき承認」が埋もれる | §3.3 |
| **G4** | ヘルスダッシュボードのカウント指標 | 状態表示のみ・件数集計なし | 工場全体の停滞（承認待ち滞留・放置）が数字で見えない | §3.4 / §4 |
| **G5** | 選択式承認の明文化 | A/B/C例示のみ | 「長文入力前提にしない」が担保されず、モバイル承認が重くなる | §3.3 / §4.2 |
| **G6** | Decision Log 二層分離 | 単一「Decision Log」 | 収益化承認(人間のみ)と運用判断(Claude読取)を混同し正本破壊 | §3.5 |
| **G7** | ExecutionRun→新ToDo の追跡リンク | レビュー→新ToDo生成と書くのみ | レビュー由来ToDoの出所が辿れず、ループが台帳化されない | §3.2 (`sourceRunId`) |
| **G8** | 既存 vloop `[x]`/`[ ]` との橋渡し | 言及なし | 新Approval構造と既存承認の二重管理リスク | §3.3 / §5整合 |
| **G9** | 放置(stale)定義の流用明示 | 「放置案件数」と書くのみ | 既存 `getStagnation` を再実装し重複・閾値ズレ | §3.4（既存流用） |

---

## 3. 推奨データ構造

> 原則: **既存JSON（app-progress / project-tasks / work-queue / execution-runs）は変更しない。新ファイル追加 or 任意フィールド追加のみ**。未指定の既存データは後方互換で動く。

### 3.1 Epic（新規 `epics.json`）— G1

```jsonc
Epic {
  epicId: string                 // "epic-91" / "ai-factory-ops"
  githubIssue?: number           // 91
  title: string
  goal: string                   // ★ 1文ゴール（明確なら継続実行の根拠になる）
  progress: number               // ★ 0..100（子ToDoのdone率から派生可・手動上書き可）
  remainingWork: string[]        // ★ 残作業（Epic完了条件の未充足分）
  latestRunId?: string           // ★ 直近 ExecutionRun（execution-runs を参照のみ）
  nextAction: string             // ★ 次の1手
  decisionPolicy: 'autonomous' | 'approval_required' | 'budget_sensitive' | 'destructive_sensitive'
  autoApprovalRule?: string      // 自律判断の範囲（例「Goal非変更の技術選定はautonomous」）
  status: 'active' | 'paused' | 'blocked' | 'done'
  relatedTodoIds?: string[]
  updatedAt: string
}
```

- ★=#91に欠けていた5項目（Goal/Progress/Remaining Work/Latest ExecutionRun/Next Action）。
- `progress` は `project-tasks.json` の子タスク done率から自動算出可（手入力を増やさない）。
- 既存 [[epics]] の narrative（目的/完了条件/現状）を**そのまま構造化**したもの。二重管理を避けるため epics.md を人間向け要約、epics.json を機械可読にする。

### 3.2 ToDo `source`（`project-tasks.json` に任意フィールド追加）— G2 / G7

```jsonc
Task {
  // ...既存フィールドは非変更...
  source?: 'user' | 'ai_generated' | 'execution_review' | 'market_research' | 'vault' | 'github_issue'
  sourceRunId?: string           // source==='execution_review' のとき生成元 ExecutionRun（★ループ追跡）
  epicId?: string                // 所属Epic
}
```

- 既存タスクで未指定のものは `user` とみなす（後方互換）。
- `execution_review` + `sourceRunId` で「ExecutionRunレビュー → 新ToDo生成」を台帳化（#91のループ完結）。
- [[../20_reviews/案件別ToDo運用ルール]] の状態（open/in_progress/done…）と直交。状態は進捗、source は出所。

### 3.3 Approval（新規 `approvals.json`）— G3 / G5 / G8

```jsonc
Approval {
  approvalId: string
  epicId?: string
  title: string
  priority: 'critical' | 'high' | 'low'                   // ★ G3（3段階・ユーザー確定）
  category: 'goal_change' | 'billing' | 'destructive' | 'monetization' | 'multi_option'  // 停止条件と対応
  options: { key: string; label: string; detail?: string; flag?: 'billing'|'destructive' }[]  // ★ A/B/C 選択式
  recommended: string            // options[].key（Claudeの推奨・承認ではない）
  reason: string                 // 推奨理由（短文・1〜2行）
  status: 'pending' | 'decided' | 'expired'
  decidedOption?: string         // 選ばれた key（1タップで確定）
  decidedBy?: string
  decidedAt?: string
  createdRunId?: string          // ★ この承認待ちを生んだ ExecutionRun
  createdAt: string
}
```

- **選択式が主**（G5）。`options` 必須・`recommended` 必須。長文は `detail` の任意1行のみ。自由記述入力は不要。
- 既存 [[../90_templates/chatgpt-approval-template]] の「推奨案＋比較候補」と1:1対応（構造化しただけ・運用は連続）。
- **既存 vloop `[x]`/`[ ]` との橋渡し（G8）**: `status: decided` かつ承認系の決定 → 対応ToDoを `work-queue` / [[../20_reviews/vloop_queue]] の `[x]` へ昇格。Approval は「`[ ]` 承認待ちのリッチ版」であり二重管理しない。

### 3.4 ヘルスサマリー（派生・保存しない／集計エンドポイント）— G4 / G9

```jsonc
HealthSummary {              // 既存JSONから都度算出（新規保存なし）
  runnable: number           // 実行可能（work-queue queued / vloop [x] 相当）
  running: number            // 実行中（in_progress）
  pendingApproval: number    // 承認待ち（approvals status=pending + vloop [ ]）
  limitWaiting: number       // Claude上限待ち
  stopped: number            // 停止中（blocked）
  epicsActive: number        // Epic進行件数（epics status=active）
  stale: number              // 放置案件（既存 getStagnation の stalled を流用 ★G9・再実装しない）
}
```

- `stale` は既存 `getStagnation()`（aging≥3 / stalled≥7）を**そのまま流用**。閾値の二重定義を避ける。

### 3.5 運用 Decision Log（新規 `operational-decisions.ndjson`）— G6

```jsonc
OperationalDecision {       // 技術・運用の決定（Claude Code が次回実行時に読む）
  decisionId: string
  epicId?: string
  topic: string             // "認証方式" / "DB方針"
  decision: string          // "JWT採用 / Clerk不採用" / "Supabase継続"
  approvalId?: string       // 由来の Approval
  decidedAt: string
}
```

- **既存 [[decision-log]]（収益化シナリオの承認判断・人間のみ）とは別ファイル・別責務**。
- Claude Code は実行開始時にこれを読み、既決の技術判断を再質問せず継続（#91「次回実行時にClaude Codeが読み取り継続」を満たす）。
- 収益化方針の判断は従来どおり [[decision-log]]（人間のみ）を正本にする。

---

## 4. 推奨画面構成（`/operations` 推奨）

> ルートは `/operations` を推奨（/automation より広義。承認・Epic・ヘルスを含む管制塔）。モバイル縦1カラム・タップ操作前提。

```
┌ AI工場 オペレーションセンター ───────────── 2026-05-30 ┐
│ [ヘルスバー：横スクロールのカウントチップ]                  │
│ 実行可 3 ┃ 実行中 1 ┃ 承認待ち 2 ┃ 上限待ち 0 ┃ 停止 1 ┃ Epic 4 ┃ 放置 1⚠ │
├──────────────────────────────────────┤
│ ① 承認待ち（優先度順・選択式）   ← 最上段（モバイルで最速処理）   │
│ ② Epic進行（Goal/進捗/Next Action/直近Run/放置）              │
│ ③ 自動実行状態（実行中/次予定/履歴/起動理由/次回起動）          │
│ ④ Decision Log（読み取り履歴）                              │
└──────────────────────────────────────┘
```

### 4.1 ヘルスバー（§3.4 を表示）
- カウントチップを横スクロール1行。`放置`は stale>0 で⚠。タップで該当一覧へ。

### 4.2 承認待ち（選択式カード・**長文入力なし**）— モバイル最適化の核

```
┌ 🔴critical  認証方式の選択 ──────────── Epic #91 ┐
│ A) JWT自前         追加課金なし・軽量                  │
│ B) Clerk導入  💳   速いが月額課金が発生                 │
│ C) Supabase Auth   既存DBと統合                       │
│ ───────────────────────────────────── │
│ 推奨: A（課金なし・既存方針／継続実行ポリシー準拠）          │
│ [ A を承認 ]  [ B ]  [ C ]  [ 保留 ]                  │
└─────────────────────────────────────────┘
```

- **1タップで決定**。理由入力は任意（既定で不要）。
- 優先度バッジ 🔴critical / 🟠high / ⚪low（3段階）。critical を最上部に固定ソート。
- 💳=課金フラグ / ⚠=destructiveフラグ を option に表示（停止条件と視覚連動）。
- 決定 → `operational-decisions.ndjson` 追記 → 対応ToDoを `[x]` 昇格 → 次回実行へ引き継ぎ。

### 4.3 Epic進行カード（§3.1 を表示）
```
┌ Epic #91 AI工場オペレーションセンター  [autonomous] ┐
│ Goal: Progressを管制塔にし継続実行基盤を作る            │
│ 進捗 ▓▓▓▓▓▓░░░░ 60%      放置: なし                 │
│ Next: ヘルスダッシュボードMVP実装                       │
│ 直近Run: 20260530-...  残: 承認選択式UI / 自動再開      │
└────────────────────────────────────┘
```
- 既存 [[../05_monetization/progress_画面モック]] の停滞バッジ・収益インパクト表示と同じ流儀。

### 4.4 自動実行状態（#91 §2 を踏襲・**MVP最優先**）
- 実行中タスク / 次に実行予定 / 最近の実行履歴 / 起動理由 / 次回起動予定。
- 操作（**開始/停止/再開**）は**MVP内で実装**（pm2/プロセス制御含む・ユーザー確定）。

### 4.5 Decision Log（読み取り）
- `operational-decisions.ndjson` を時系列表示（JWT採用 等）。Claude Code が読む正本の人間向けビュー。

---

## 5. 実装優先順位（依存順）

> 方針: **自動実行制御を最優先で含める**（ユーザー確定）。各段は既存スキーマ非破壊。

| 順 | 項目 | 理由 | 依存 |
|---|---|---|---|
| 1 | **継続実行ポリシー確定**（§6 の停止条件リスト） | コード不要・即時。AIが止まる/止まらないの基準を先に固定 | なし |
| 2 | **自動実行管理 UI**（開始/停止/再開 + 状態表示） | ★ユーザー最優先。管制塔の中核機能。pm2/実プロセス制御含む | 1 |
| 3 | **Epic構造化**（`epics.json`・§3.1） | 情報分散で止まらない核。Goal明確＝継続実行の根拠 | 1 |
| 4 | **Approval優先度＋選択式**（§3.3 / §4.2） | モバイル承認の核。承認を軽くしAIを止めない | 1 |
| 5 | **ヘルスダッシュボード**（§3.4 / §4.1） | 既存JSON＋getStagnation流用で派生表示のみ | 2,3,4 |
| 6 | **ToDo `source`**（§3.2） | AI生成ToDo/レビュー由来を追跡。ループ台帳化 | 3 |
| 7 | **運用Decision Log**（§3.5） | 次回実行引き継ぎ。再質問を消す | 4 |

→ 1〜7 で「自動実行制御も含む管制塔」が成立。

---

## 6. 継続実行ポリシー（確定版・停止条件）

> #91 §5 と本タスク前提を統合。AIは**原則継続実行**。承認待ちを作るのは以下**のみ**。

| 停止する（承認待ちを作る） | category |
|---|---|
| Epicゴールの変更 | goal_change |
| 課金の発生（API/有料サービス導入） | billing |
| destructive変更（削除・本番破壊・外部公開） | destructive |
| 収益化方針の変更 | monetization |
| 複数案で結果が大きく変わる場合 | multi_option |

**それ以外はClaudeが自律判断して継続**（Goal達成に影響しない技術選定・リファクタ・文書化・ToDo消化）。

- 既存 [[../20_reviews/vloop_queue]] §「止まってよい条件」・[[epics]] §「止まってよいのは」と整合（pull/push失敗・実行環境なし・利用制限は技術的停止として別枠で維持）。
- Epic `decisionPolicy: autonomous` のときは上記5条件以外で停止しない。`approval_required` のEpicだけ広く承認を取る。

---

## 7. MVPで実装する範囲 / 将来拡張

### 7.1 MVP（API/課金なし・既存非破壊・ユーザー確定スコープ）
1. `/operations` ページ（縦1カラム・モバイル・A案確定）
2. **自動実行 開始/停止/再開**（pm2/プロセス制御・★優先①）
3. 自動実行状態表示（実行中/次予定/履歴/起動理由/次回起動）
4. ヘルスダッシュボード（§3.4・既存JSON＋getStagnationから**派生表示のみ**）
5. `epics.json`（§3.1・Goal/Progress/Remaining/LatestRun/NextAction・手動更新可）
6. `approvals.json` ＋ 選択式承認カード（§3.3 / §4.2・**3段階**優先度ソート・1タップ決定）
7. `operational-decisions.ndjson`（§3.5・次回実行引き継ぎ・A案確定）
8. 継続実行ポリシー明文化（§6）
9. ToDo `source`/`sourceRunId`/`epicId` 追加（§3.2・既存はuser既定）

### 7.2 Phase2（自動化強化）
- ExecutionRunレビュー → **自動ToDo生成**（`source=execution_review` 自動付与・`sourceRunId` 自動リンク）
- Epic `progress` の子タスクdone率からの**自動算出**
- 実行中/次予定の**リアルタイム表示**強化

### 7.3 将来（#91「将来対応」踏襲）
- VPS `@reboot` 自動再開 / 毎朝11時自動再開 / Claude上限回復後の自動再開
- **一定時間 無応答時の推奨案 自動採用**（#91「Auto Approval Rule」の将来検討・`recommended` を既定採用）
- Factory成果物台帳（#91明記の後回し項目）
- 自動取得指標との連携（[[収益結果ダッシュボード設計]]）

---

## 8. Progressレビュー用要約

> ChatGPT/Progress レビュー入口用の自己完結サマリー。

- **対象**: Epic #91 AI工場オペレーションセンターの設計レビュー（実装なし）。
- **結論**: 方針（管制塔化・継続実行優先・運用基盤先行）は妥当。不足は「Progress上のデータ構造と画面の具体」と「モバイル選択式承認の明文化」。
- **主要な不足9件**: G1 Epic構造化(Goal/Progress/Remaining/LatestRun/NextAction) / G2 ToDo source / G3 Approval優先度 / G4 ヘルスカウント / G5 選択式承認の明文化 / G6 Decision Log二層分離 / G7 ExecutionRun→ToDo追跡 / G8 vloop`[x]`との橋渡し / G9 放置定義の流用。
- **推奨データ構造**: `epics.json` / `project-tasks.json`へsource追加 / `approvals.json`(優先度+選択式) / HealthSummary(派生) / `operational-decisions.ndjson`。**全て既存スキーマ非破壊・API/課金なし**。
- **推奨画面**: `/operations`（モバイル縦1カラム）= ヘルスバー → 承認待ち(選択式・1タップ) → Epic進行 → 自動実行状態 → Decision Log。
- **承認のモバイル最適化**: A/B/C＋推奨＋短文理由を**1タップで決定**、自由記述は任意。長文入力前提を排除。
- **継続実行**: 停止は5条件のみ（ゴール変更/課金/destructive/収益化方針/複数案で結果が大きく変わる）。それ以外はClaude自律継続。
- **優先順位（確定）**: ①ポリシー確定 ②自動実行制御 ③Epic構造化 ④選択式承認（3段階） ⑤ヘルス ⑥source ⑦運用Decision Log。
- **ユーザー確定事項**（2026-05-30）:
  1. ルート: `/operations`（単一集約・A案）✅
  2. Approval優先度: `critical/high/low`（3段階）✅
  3. Decision Log: 別ファイル分離（A案・operational-decisions.ndjson）✅
  4. 自動実行制御: **MVP最優先**（Phase2送りではなくMVPに含める）✅

---

## 9. 既存運用との整合性チェック（実装時の遵守事項）

- ✅ 既存JSON（app-progress / project-tasks / work-queue / execution-runs）**非変更**。新ファイル追加 or 任意フィールド追加のみ（[[アプリ案工場_Phase1設計]] 方針）。
- ✅ vloop `[x]`=実行可 / `[ ]`=承認待ち を壊さない。Approval決定→`[x]`昇格で**二重管理しない**（[[../20_reviews/vloop_queue]]）。
- ✅ ExecutionRun（`POST /api/execution-runs`）スキーマ非変更。Epic.latestRunId は**参照のみ**。
- ✅ 放置は既存 `getStagnation`（aging≥3/stalled≥7）を流用・再実装しない。
- ✅ 収益化承認は [[decision-log]]（人間のみ）が正本。運用Decision Logと混同しない。
- ✅ API/課金なし・ローカルJSON維持（[[progress連携基準]]）。
- ✅ Progress本番（:3010）反映の pm2 restart は**ユーザー判断**（[[../02_apps/progress]] 運用メモ）。

## 関連
- Epic: kaeru07/vault#91
- [[epics]]（#50 Epic正本）/ [[../03_prompts/Epic単位運用]]（#42）
- [[../20_reviews/vloop_queue]] / [[../20_reviews/案件別ToDo運用ルール]]
- [[../20_reviews/ChatGPT承認待ち]] / [[../90_templates/chatgpt-approval-template]] / [[decision-log]]
- [[progress連携基準]] / [[アプリ案工場_Phase1設計]] / [[progress_画面モック]] / [[収益結果ダッシュボード設計]]
- [[../02_apps/progress]] / [[../20_reviews/2026-05-17_progress-stagnation-indicator]]
</content>
</invoke>
