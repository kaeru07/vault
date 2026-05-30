---
title: CLI実行プロンプト — Epic #91 /operations ページ実装
type: cli-prompt
issue: kaeru07/vault#91
created: 2026-05-30
usage: VPS上のClaude Code CLIに貼り付けて実行する
---

# 使い方

1. VPS上で progress app のディレクトリに移動: `cd /root/company/apps/ny01/progress`
2. Claude Code CLI 起動: `claude`
3. 以下のプロンプトをそのまま貼り付ける

---

# 貼り付けるプロンプト（ここから）

```
Epic #91「AI工場オペレーションセンター」の /operations ページを実装してください。

## 作業ディレクトリ
現在の /root/company/apps/ny01/progress で作業する。

## 前提確認（最初に実行）
ls data/real/
pm2 list
npx tsc --noEmit

## 実装する内容

### 1. データファイルを data/real/ に作成

data/real/epics.json:
[{"epicId":"epic-91","githubIssue":91,"title":"AI工場オペレーションセンター","goal":"ProgressをAI工場の管制塔にし、AIが止まらずToDoを継続消化できる運用基盤を作る","progress":20,"remainingWork":["/operations ページ実装","ヘルスダッシュボード","選択式承認カード","Epic進行カード"],"latestRunId":null,"nextAction":"Step 2以降を実装する","decisionPolicy":"autonomous","status":"active","updatedAt":"2026-05-30"}]

data/real/approvals.json:
[]

data/real/operational-decisions.ndjson:
（空ファイル）

### 2. lib/types/operations.ts を新規作成

以下の型を定義する:

export type DecisionPolicy = 'autonomous' | 'approval_required' | 'budget_sensitive' | 'destructive_sensitive'
export type ApprovalPriority = 'critical' | 'high' | 'low'
export type ApprovalCategory = 'goal_change' | 'billing' | 'destructive' | 'monetization' | 'multi_option'

export interface Epic {
  epicId: string; githubIssue?: number; title: string; goal: string
  progress: number; remainingWork: string[]; latestRunId?: string | null
  nextAction: string; decisionPolicy: DecisionPolicy; autoApprovalRule?: string
  status: 'active' | 'paused' | 'blocked' | 'done'
  relatedTodoIds?: string[]; updatedAt: string
}

export interface ApprovalOption {
  key: string; label: string; detail?: string; flag?: 'billing' | 'destructive'
}

export interface Approval {
  approvalId: string; epicId?: string; title: string
  priority: ApprovalPriority; category: ApprovalCategory
  options: ApprovalOption[]; recommended: string; reason: string
  status: 'pending' | 'decided' | 'expired'
  decidedOption?: string; decidedBy?: string; decidedAt?: string
  createdRunId?: string; createdAt: string
}

export interface OperationalDecision {
  decisionId: string; epicId?: string; topic: string; decision: string
  approvalId?: string; decidedAt: string
}

export interface HealthSummary {
  runnable: number; running: number; pendingApproval: number
  limitWaiting: number; stopped: number; epicsActive: number; stale: number
}

### 3. lib/operations-store.ts を新規作成

- getEpics() / updateEpic() — epics.json の読み書き
- getApprovals() / getPendingApprovals() / decideApproval() — approvals.json の読み書き
  - getPendingApprovals は priority 順（critical→high→low）でソート
  - decideApproval は approvals.json を更新し、operational-decisions.ndjson に1行 append する
- getOperationalDecisions() — operational-decisions.ndjson を読む（ndjson = 1行1JSON）
- computeHealthSummary() — 既存JSONから以下を集計する（新規保存はしない）
  - runnable: work-queue.json で status==='queued' の件数
  - running: work-queue.json で status==='in_progress' の件数
  - stopped: work-queue.json で status==='blocked' の件数
  - pendingApproval: getPendingApprovals().length
  - epicsActive: getEpics() で status==='active' の件数
  - stale: app-progress.json で status が in_progress または active かつ updatedAt から7日以上経過しているプロジェクトの件数
  - limitWaiting: 0（固定・将来拡張）
  - ※ work-queue.json と app-progress.json の実際の構造を先に確認してから実装する

### 4. APIルートを新規作成

app/api/operations/health/route.ts — GET: computeHealthSummary() を返す
app/api/operations/epics/route.ts — GET: getEpics() を返す
app/api/operations/approvals/route.ts
  - GET: getPendingApprovals() を返す
  - POST: { approvalId, decidedOption } を受け取り decideApproval() を呼ぶ
app/api/operations/autoexec/route.ts
  - pm2 list で vloop プロセス名を確認してから実装する
  - GET: pm2 の該当プロセスの status を返す（pm2 jlist でJSON取得）
  - POST: { action: 'start'|'stop'|'restart' } を受け取り pm2 コマンドを実行する
  - プロセス名は VLOOP_PM2_NAME 環境変数で設定（未設定時は pm2 list の結果から推定する）

### 5. app/operations/page.tsx を新規作成（'use client'）

モバイル縦1カラム（max-w-lg mx-auto）で以下のセクションを上から順に表示:

① ヘルスバー（/api/operations/health から取得）
  - 実行可 / 実行中 / 承認待ち / 上限待ち / 停止 / Epic / 放置 の7チップを横スクロール1行
  - 放置が1件以上なら ⚠ を表示

② 自動実行コントロール（/api/operations/autoexec から取得）
  - pm2 ステータスを表示
  - 開始 / 停止 / 再開 の3ボタン

③ 承認待ち（/api/operations/approvals から取得・件数0なら非表示）
  - priority 順（critical→high→low）で表示
  - 各カードに A/B/C の選択ボタン（recommended を青・その他をグレー）と保留ボタン
  - タップ1回で決定 → 再取得

④ Epic進行（/api/operations/epics から取得・status=activeのみ）
  - 各カードに title / goal / 進捗バー / nextAction / latestRunId

### 6. ナビゲーションに追加
既存のナビ（layout または header）に /operations へのリンクを追加する。
ラベルは「工場」または「Operations」で構わない。

### 7. .env.local に追記
VLOOP_PM2_NAME=<pm2 list で確認した vloop のプロセス名>

### 8. 検証・本番反映
npx tsc --noEmit  # エラーゼロを確認
npm run build     # ビルド成功を確認
pm2 restart progress

## 継続実行ポリシー（autonomous）
以下の場合だけ止まってよい:
- 課金が発生する
- ファイル削除・本番データ破壊などdestructiveな変更が必要
- Epicのゴール自体を変えようとしている

それ以外（技術選定・実装方式・UI細部・pm2プロセス名の推定）はClaudeが自律判断して進める。途中でユーザー確認を求めない。

## 絶対に守るルール
- 既存の app-progress.json / project-tasks.json / work-queue.json / execution-runs は変更しない（追加のみ）
- API課金・外部サービスは使わない
- tsc / build が通らないままにしない

## 完了後
20_reviews/YYYY-MM-DD_epic91-operations-impl.md にExecutionRunレビューを生成し、
20_reviews/_review_queue.md の未レビューセクションに1行追記する。
```

---

# 補足

- `.env.local` がない場合は新規作成してよい
- `pm2 list` でプロセス名が vloop でない場合はそのまま使う（承認不要・autonomous）
- ビルドエラーが出た場合は自律修正して進める（tsc エラーは止まらずに直す）
- `/operations` の見た目は Tailwind で整えてよい（既存スタイルに合わせる）

## 関連ファイル（参照用）
- 設計詳細: `05_monetization/AI工場オペレーションセンター設計レビュー.md`（vault）
- 詳細Runbook: `05_monetization/AI工場_operations実装Runbook.md`（vault）
- 継続実行ポリシー: `03_prompts/継続実行ポリシー.md`（vault）
</content>
