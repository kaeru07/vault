---
title: AI工場 /operations ページ実装 Runbook
type: implementation-runbook
issue: kaeru07/vault#91
created: 2026-05-30
updated: 2026-05-30
status: ready（progress app アクセス時に即実行可）
targetApp: ny01/progress
targetPath: /root/company/apps/ny01/progress
---

# AI工場 /operations ページ実装 Runbook

> Epic [#91](https://github.com/kaeru07/vault/issues/91)。
> **progress app (`/root/company/apps/ny01/progress`) にアクセスできる環境でこのRunbookを読んで実装を開始する。**
> 設計詳細: [[AI工場オペレーションセンター設計レビュー]]。
> 継続実行ポリシー: [[../03_prompts/継続実行ポリシー]]（autonomous・Epicゴール非変更の判断は自律実行）。

---

## 前提確認チェックリスト（実装開始前）

```bash
cd /root/company/apps/ny01/progress
ls data/real/          # app-progress.json / project-tasks.json / work-queue.json を確認
pm2 list               # progress が running か確認
npx tsc --noEmit       # 既存の型エラーがないか確認
npm run build          # 既存のビルドが通るか確認
```

---

## Step 1: seed データのデプロイ（5分）

```bash
cd /root/company/apps/ny01/progress

# Vault seed からコピー（Vault repo が clone されている場合）
cp /home/user/vault/05_monetization/seeds/epics-seed.json         data/real/epics.json
cp /home/user/vault/05_monetization/seeds/approvals-seed.json     data/real/approvals.json
cp /home/user/vault/05_monetization/seeds/operational-decisions-seed.ndjson data/real/operational-decisions.ndjson
```

手動作成の場合は以下の内容で作成:

**`data/real/epics.json`**
```json
[
  {
    "epicId": "epic-91",
    "githubIssue": 91,
    "title": "AI工場オペレーションセンター",
    "goal": "ProgressをAI工場の管制塔にし、AIが止まらずToDoを継続消化できる運用基盤を作る",
    "progress": 20,
    "remainingWork": [
      "/operations ページ実装（自動実行 開始/停止/再開・pm2制御）",
      "ヘルスダッシュボード",
      "選択式承認カード",
      "Epic進行カード",
      "operational-decisions.ndjson 連携"
    ],
    "latestRunId": null,
    "nextAction": "Step 2 以降を実装する",
    "decisionPolicy": "autonomous",
    "autoApprovalRule": "Epicゴール非変更・課金なし・非destructiveはClaude自律決定",
    "status": "active",
    "relatedTodoIds": [],
    "updatedAt": "2026-05-30"
  }
]
```

**`data/real/approvals.json`**
```json
[]
```

**`data/real/operational-decisions.ndjson`**
```
(空ファイル)
```

---

## Step 2: 型定義の追加（`lib/types/operations.ts` 新規）

```typescript
// lib/types/operations.ts

export type DecisionPolicy =
  | 'autonomous'
  | 'approval_required'
  | 'budget_sensitive'
  | 'destructive_sensitive'

export interface Epic {
  epicId: string
  githubIssue?: number
  title: string
  goal: string
  progress: number               // 0..100
  remainingWork: string[]
  latestRunId?: string | null
  nextAction: string
  decisionPolicy: DecisionPolicy
  autoApprovalRule?: string
  status: 'active' | 'paused' | 'blocked' | 'done'
  relatedTodoIds?: string[]
  updatedAt: string
}

export type ApprovalPriority = 'critical' | 'high' | 'low'
export type ApprovalCategory =
  | 'goal_change'
  | 'billing'
  | 'destructive'
  | 'monetization'
  | 'multi_option'

export interface ApprovalOption {
  key: string
  label: string
  detail?: string
  flag?: 'billing' | 'destructive'
}

export interface Approval {
  approvalId: string
  epicId?: string
  title: string
  priority: ApprovalPriority
  category: ApprovalCategory
  options: ApprovalOption[]
  recommended: string            // options[].key
  reason: string
  status: 'pending' | 'decided' | 'expired'
  decidedOption?: string
  decidedBy?: string
  decidedAt?: string
  createdRunId?: string
  createdAt: string
}

export interface OperationalDecision {
  decisionId: string
  epicId?: string
  topic: string
  decision: string
  approvalId?: string
  decidedAt: string
}

export interface HealthSummary {
  runnable: number
  running: number
  pendingApproval: number
  limitWaiting: number
  stopped: number
  epicsActive: number
  stale: number
}

export type AutoExecStatus =
  | 'running'
  | 'stopped'
  | 'limit_waiting'
  | 'error'
  | 'paused'
```

---

## Step 3: データ読み書きユーティリティ（`lib/operations-store.ts` 新規）

```typescript
// lib/operations-store.ts
import fs from 'fs'
import path from 'path'
import { Epic, Approval, OperationalDecision, HealthSummary } from './types/operations'

const DATA_DIR = process.env.PROGRESS_DATA_PATH ?? path.join(process.cwd(), 'data/real')

function readJson<T>(filename: string, fallback: T): T {
  try {
    return JSON.parse(fs.readFileSync(path.join(DATA_DIR, filename), 'utf-8'))
  } catch {
    return fallback
  }
}

function writeJson<T>(filename: string, data: T): void {
  fs.writeFileSync(path.join(DATA_DIR, filename), JSON.stringify(data, null, 2), 'utf-8')
}

export function getEpics(): Epic[] {
  return readJson<Epic[]>('epics.json', [])
}

export function updateEpic(epicId: string, patch: Partial<Epic>): void {
  const epics = getEpics()
  const idx = epics.findIndex(e => e.epicId === epicId)
  if (idx >= 0) {
    epics[idx] = { ...epics[idx], ...patch, updatedAt: new Date().toISOString().slice(0, 10) }
    writeJson('epics.json', epics)
  }
}

export function getApprovals(): Approval[] {
  return readJson<Approval[]>('approvals.json', [])
}

export function getPendingApprovals(): Approval[] {
  return getApprovals()
    .filter(a => a.status === 'pending')
    .sort((a, b) => {
      const order = { critical: 0, high: 1, low: 2 }
      return order[a.priority] - order[b.priority]
    })
}

export function decideApproval(approvalId: string, decidedOption: string): void {
  const approvals = getApprovals()
  const idx = approvals.findIndex(a => a.approvalId === approvalId)
  if (idx >= 0) {
    approvals[idx] = {
      ...approvals[idx],
      status: 'decided',
      decidedOption,
      decidedAt: new Date().toISOString(),
    }
    writeJson('approvals.json', approvals)

    // operational-decisions.ndjson へ追記
    const approval = approvals[idx]
    const decision: OperationalDecision = {
      decisionId: `dec-${Date.now()}`,
      epicId: approval.epicId,
      topic: approval.title,
      decision: `${approval.options.find(o => o.key === decidedOption)?.label ?? decidedOption}（${decidedOption}）を選択`,
      approvalId,
      decidedAt: new Date().toISOString(),
    }
    const ndJsonPath = path.join(DATA_DIR, 'operational-decisions.ndjson')
    fs.appendFileSync(ndJsonPath, JSON.stringify(decision) + '\n', 'utf-8')
  }
}

export function getOperationalDecisions(): OperationalDecision[] {
  try {
    const raw = fs.readFileSync(path.join(DATA_DIR, 'operational-decisions.ndjson'), 'utf-8')
    return raw.trim().split('\n').filter(Boolean).map(line => JSON.parse(line))
  } catch {
    return []
  }
}

// 既存 getStagnation を流用して stale カウントを取得
export function computeHealthSummary(): HealthSummary {
  // 既存 work-queue から実行可能・実行中を集計
  let runnable = 0, running = 0, stopped = 0
  try {
    const wq = JSON.parse(fs.readFileSync(path.join(DATA_DIR, 'work-queue.json'), 'utf-8'))
    const items = Array.isArray(wq) ? wq : (wq.items ?? [])
    runnable = items.filter((i: any) => i.status === 'queued').length
    running = items.filter((i: any) => i.status === 'in_progress').length
    stopped = items.filter((i: any) => i.status === 'blocked').length
  } catch { /* fallback 0 */ }

  const pendingApproval = getPendingApprovals().length
  const epicsActive = getEpics().filter(e => e.status === 'active').length

  // stale: app-progress から既存 getStagnation ロジック流用
  let stale = 0
  try {
    const ap = JSON.parse(fs.readFileSync(path.join(DATA_DIR, 'app-progress.json'), 'utf-8'))
    const projects = Array.isArray(ap) ? ap : (ap.projects ?? [])
    const STALE_DAYS = 7
    const now = Date.now()
    stale = projects.filter((p: any) => {
      if (!['in_progress', 'active'].includes(p.status)) return false
      if (!p.updatedAt) return false
      const diffDays = (now - new Date(p.updatedAt).getTime()) / 86400000
      return diffDays >= STALE_DAYS
    }).length
  } catch { /* fallback 0 */ }

  return { runnable, running, pendingApproval, limitWaiting: 0, stopped, epicsActive, stale }
}
```

---

## Step 4: APIルート（`app/api/operations/` 新規・3ファイル）

### `app/api/operations/health/route.ts`
```typescript
import { NextResponse } from 'next/server'
import { computeHealthSummary } from '@/lib/operations-store'

export async function GET() {
  return NextResponse.json(computeHealthSummary())
}
```

### `app/api/operations/approvals/route.ts`
```typescript
import { NextResponse } from 'next/server'
import { getPendingApprovals, decideApproval } from '@/lib/operations-store'

export async function GET() {
  return NextResponse.json(getPendingApprovals())
}

export async function POST(req: Request) {
  const { approvalId, decidedOption } = await req.json()
  if (!approvalId || !decidedOption) {
    return NextResponse.json({ error: 'approvalId and decidedOption required' }, { status: 400 })
  }
  decideApproval(approvalId, decidedOption)
  return NextResponse.json({ ok: true })
}
```

### `app/api/operations/epics/route.ts`
```typescript
import { NextResponse } from 'next/server'
import { getEpics } from '@/lib/operations-store'

export async function GET() {
  return NextResponse.json(getEpics())
}
```

### `app/api/operations/autoexec/route.ts`（pm2制御）
```typescript
import { NextResponse } from 'next/server'
import { exec } from 'child_process'
import { promisify } from 'util'

const execAsync = promisify(exec)

// pm2 の vloop プロセス名（実際の名前に合わせる）
const VLOOP_PM2_NAME = process.env.VLOOP_PM2_NAME ?? 'vloop'

async function getPm2Status(): Promise<string> {
  try {
    const { stdout } = await execAsync(`pm2 jlist`)
    const list = JSON.parse(stdout)
    const proc = list.find((p: any) => p.name === VLOOP_PM2_NAME)
    if (!proc) return 'not_found'
    return proc.pm2_env?.status ?? 'unknown'
  } catch {
    return 'error'
  }
}

export async function GET() {
  const status = await getPm2Status()
  return NextResponse.json({ status, processName: VLOOP_PM2_NAME })
}

export async function POST(req: Request) {
  const { action } = await req.json()
  if (!['start', 'stop', 'restart'].includes(action)) {
    return NextResponse.json({ error: 'action must be start|stop|restart' }, { status: 400 })
  }
  try {
    await execAsync(`pm2 ${action} ${VLOOP_PM2_NAME}`)
    const status = await getPm2Status()
    return NextResponse.json({ ok: true, status })
  } catch (e: any) {
    return NextResponse.json({ error: e.message }, { status: 500 })
  }
}
```

> **注意**: `VLOOP_PM2_NAME` を `.env.local` に設定する（例: `VLOOP_PM2_NAME=vloop`）。実際のpm2プロセス名を確認して合わせること。

---

## Step 5: /operations ページ（`app/operations/page.tsx` 新規）

```typescript
'use client'
import { useEffect, useState } from 'react'
import type { HealthSummary, Epic, Approval } from '@/lib/types/operations'

// ── ヘルスバー ─────────────────────────────────────────────────────────
function HealthBar({ h }: { h: HealthSummary }) {
  const chips = [
    { label: '実行可', value: h.runnable, color: 'bg-green-100 text-green-800' },
    { label: '実行中', value: h.running, color: 'bg-blue-100 text-blue-800' },
    { label: '承認待ち', value: h.pendingApproval, color: 'bg-yellow-100 text-yellow-800' },
    { label: '上限待ち', value: h.limitWaiting, color: 'bg-orange-100 text-orange-800' },
    { label: '停止', value: h.stopped, color: 'bg-red-100 text-red-800' },
    { label: 'Epic', value: h.epicsActive, color: 'bg-purple-100 text-purple-800' },
    { label: '放置', value: h.stale, color: h.stale > 0 ? 'bg-red-200 text-red-900 font-bold' : 'bg-gray-100 text-gray-600' },
  ]
  return (
    <div className="flex gap-2 overflow-x-auto pb-1">
      {chips.map(c => (
        <span key={c.label} className={`shrink-0 rounded-full px-3 py-1 text-sm ${c.color}`}>
          {c.label} {c.value}{c.label === '放置' && c.value > 0 ? ' ⚠' : ''}
        </span>
      ))}
    </div>
  )
}

// ── 承認カード（選択式） ────────────────────────────────────────────────
const PRIORITY_STYLE: Record<string, string> = {
  critical: '🔴',
  high: '🟠',
  low: '⚪',
}

function ApprovalCard({ approval, onDecide }: { approval: Approval; onDecide: () => void }) {
  const [loading, setLoading] = useState(false)

  const decide = async (key: string) => {
    setLoading(true)
    await fetch('/api/operations/approvals', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ approvalId: approval.approvalId, decidedOption: key }),
    })
    setLoading(false)
    onDecide()
  }

  return (
    <div className="rounded-lg border border-gray-200 p-4 space-y-3">
      <div className="flex items-center gap-2">
        <span>{PRIORITY_STYLE[approval.priority]}</span>
        <span className="font-medium">{approval.title}</span>
        {approval.epicId && <span className="text-xs text-gray-400">{approval.epicId}</span>}
      </div>
      <div className="space-y-1">
        {approval.options.map(opt => (
          <div key={opt.key} className="flex items-center gap-2 text-sm">
            <span className="w-6 font-mono text-gray-500">{opt.key})</span>
            <span>{opt.label}</span>
            {opt.flag === 'billing' && <span>💳</span>}
            {opt.flag === 'destructive' && <span>⚠️</span>}
            {opt.detail && <span className="text-gray-400">{opt.detail}</span>}
          </div>
        ))}
      </div>
      <div className="text-xs text-gray-500">推奨: {approval.recommended} — {approval.reason}</div>
      <div className="flex flex-wrap gap-2">
        {approval.options.map(opt => (
          <button
            key={opt.key}
            onClick={() => decide(opt.key)}
            disabled={loading}
            className={`rounded px-4 py-2 text-sm font-medium transition
              ${opt.key === approval.recommended
                ? 'bg-blue-600 text-white hover:bg-blue-700'
                : 'bg-gray-100 text-gray-700 hover:bg-gray-200'}`}
          >
            {opt.key}
          </button>
        ))}
        <button
          onClick={() => decide('hold')}
          disabled={loading}
          className="rounded px-4 py-2 text-sm bg-gray-50 text-gray-500 hover:bg-gray-100"
        >
          保留
        </button>
      </div>
    </div>
  )
}

// ── Epic進行カード ──────────────────────────────────────────────────────
function EpicCard({ epic }: { epic: Epic }) {
  return (
    <div className="rounded-lg border border-gray-200 p-4 space-y-2">
      <div className="flex items-center justify-between">
        <span className="font-medium text-sm">{epic.title}</span>
        <span className="text-xs text-gray-400">[{epic.decisionPolicy}]</span>
      </div>
      <div className="text-xs text-gray-600">Goal: {epic.goal}</div>
      <div className="flex items-center gap-2">
        <div className="flex-1 bg-gray-200 rounded-full h-2">
          <div className="bg-blue-500 h-2 rounded-full" style={{ width: `${epic.progress}%` }} />
        </div>
        <span className="text-xs text-gray-500">{epic.progress}%</span>
      </div>
      <div className="text-xs text-blue-700">Next: {epic.nextAction}</div>
      {epic.latestRunId && (
        <div className="text-xs text-gray-400">直近Run: {epic.latestRunId}</div>
      )}
    </div>
  )
}

// ── 自動実行コントロール ────────────────────────────────────────────────
function AutoExecControl() {
  const [status, setStatus] = useState<string>('...')
  const [loading, setLoading] = useState(false)

  const fetchStatus = async () => {
    const res = await fetch('/api/operations/autoexec')
    const data = await res.json()
    setStatus(data.status)
  }

  useEffect(() => { fetchStatus() }, [])

  const control = async (action: 'start' | 'stop' | 'restart') => {
    setLoading(true)
    await fetch('/api/operations/autoexec', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ action }),
    })
    await fetchStatus()
    setLoading(false)
  }

  const statusColor: Record<string, string> = {
    online: 'text-green-600',
    stopped: 'text-red-600',
    errored: 'text-red-800',
    not_found: 'text-gray-400',
  }

  return (
    <div className="rounded-lg border border-gray-200 p-4 space-y-3">
      <div className="flex items-center justify-between">
        <span className="font-medium text-sm">自動実行（vloop）</span>
        <span className={`text-sm font-mono ${statusColor[status] ?? 'text-gray-600'}`}>
          {status}
        </span>
      </div>
      <div className="flex gap-2">
        <button
          onClick={() => control('start')}
          disabled={loading}
          className="flex-1 rounded bg-green-600 text-white py-2 text-sm font-medium hover:bg-green-700 disabled:opacity-50"
        >
          開始
        </button>
        <button
          onClick={() => control('stop')}
          disabled={loading}
          className="flex-1 rounded bg-red-600 text-white py-2 text-sm font-medium hover:bg-red-700 disabled:opacity-50"
        >
          停止
        </button>
        <button
          onClick={() => control('restart')}
          disabled={loading}
          className="flex-1 rounded bg-yellow-500 text-white py-2 text-sm font-medium hover:bg-yellow-600 disabled:opacity-50"
        >
          再開
        </button>
      </div>
    </div>
  )
}

// ── メインページ ────────────────────────────────────────────────────────
export default function OperationsPage() {
  const [health, setHealth] = useState<HealthSummary | null>(null)
  const [approvals, setApprovals] = useState<Approval[]>([])
  const [epics, setEpics] = useState<Epic[]>([])

  const fetchAll = async () => {
    const [h, a, e] = await Promise.all([
      fetch('/api/operations/health').then(r => r.json()),
      fetch('/api/operations/approvals').then(r => r.json()),
      fetch('/api/operations/epics').then(r => r.json()),
    ])
    setHealth(h)
    setApprovals(a)
    setEpics(e)
  }

  useEffect(() => { fetchAll() }, [])

  return (
    <main className="max-w-lg mx-auto px-4 py-6 space-y-6">
      <h1 className="text-xl font-bold">AI工場 オペレーションセンター</h1>

      {/* ヘルスバー */}
      {health && <HealthBar h={health} />}

      {/* 自動実行コントロール ← 最優先 */}
      <section>
        <h2 className="text-sm font-semibold text-gray-500 mb-2">自動実行</h2>
        <AutoExecControl />
      </section>

      {/* 承認待ち */}
      {approvals.length > 0 && (
        <section>
          <h2 className="text-sm font-semibold text-gray-500 mb-2">
            承認待ち {approvals.length}件
          </h2>
          <div className="space-y-3">
            {approvals.map(a => (
              <ApprovalCard key={a.approvalId} approval={a} onDecide={fetchAll} />
            ))}
          </div>
        </section>
      )}

      {/* Epic進行 */}
      {epics.length > 0 && (
        <section>
          <h2 className="text-sm font-semibold text-gray-500 mb-2">Epic進行</h2>
          <div className="space-y-3">
            {epics.filter(e => e.status === 'active').map(e => (
              <EpicCard key={e.epicId} epic={e} />
            ))}
          </div>
        </section>
      )}
    </main>
  )
}
```

---

## Step 6: ナビゲーションへ追加

既存の nav コンポーネント（`components/layout/` 等）に `/operations` リンクを追加:

```tsx
<Link href="/operations">🏭 工場</Link>
```

---

## Step 7: 環境変数（`.env.local` に追記）

```
VLOOP_PM2_NAME=vloop
```

実際の pm2 プロセス名を `pm2 list` で確認して合わせる。

---

## Step 8: ビルド確認・本番反映

```bash
npx tsc --noEmit    # 型エラーなし確認
npm run build       # ビルド成功確認
pm2 restart progress
```

ブラウザで `http://localhost:3010/operations` を確認。

---

## 完了条件（Epic #91 完了条件と照合）

- [ ] `/operations` ページが表示される
- [ ] 自動実行の開始/停止/再開ボタンが動作する（pm2制御）
- [ ] ヘルスバーが7指標を表示する
- [ ] 承認カードが選択式で表示され1タップで決定できる
- [ ] Epic進行カードが Goal/進捗/NextAction を表示する
- [ ] `operational-decisions.ndjson` に判断が追記される
- [ ] tsc / build / lint が全 OK
- [ ] pm2 restart 後に本番反映確認

---

## 未実装（Phase2）

- ExecutionRunレビュー → 自動ToDo生成（`source=execution_review` / `sourceRunId`）
- Epic進捗の子タスク done率からの自動算出
- リアルタイム更新（WebSocket or polling 強化）
- VPS @reboot 自動再開

## 関連
- 設計レビュー: [[AI工場オペレーションセンター設計レビュー]]
- 継続実行ポリシー: [[../03_prompts/継続実行ポリシー]]
- seed データ: [[seeds/README]]
- Issue: kaeru07/vault#91
</content>
