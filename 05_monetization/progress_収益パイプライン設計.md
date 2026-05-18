---
title: progress 収益パイプライン画面 設計
type: monetization-design
issue: kaeru07/vault#4
created: 2026-05-18
updated: 2026-05-18
status: active
---

# progress 収益パイプライン画面 設計

> Issue #4。candidate→approved→progress実行 の流れを見える化し、放置防止と「次の作業をやりたくなる状態」を作る。
> **実装しない。Markdown 設計まで。** approved 化・公開・課金は人間判断（[[収益化シナリオ承認フロー]]）。

## 調査メタ（妥当性）
- どこを調べたか: progress 実コード `apps/ny01/progress/types/execution-run.ts`、`app/api/execution-runs/`、05_monetization 既存設計群 / 2026-05-18 / n=1アプリ実体+設計6ファイル
- 情報源種別: 一次（progress 実型定義）+ 一次（Vault 設計）
- 再現性: 高（同ファイルで再現）/ 鮮度: 当日

## 1. 目的とスコープ

- 目的: アイデア→調査→承認→実装→公開→収益化→改善 の **状態を1画面で俯瞰**し、停滞案件を可視化して着手を促す
- スコープ: 画面構成 / データ構造 / UIイメージ / 既存progress統合案 / 次に実装するToDo
- 非スコープ: 実装、外部公開、課金、approved 自動化

## 2. パイプライン段階の定義（正本は [[収益化シナリオ承認フロー]]）

```
アイデア      調査           承認待ち          承認         実装中        公開        収益化         改善
candidate ─▶ candidate ─▶ pending_approval ─▶ approved ─▶ in_progress ─▶ published ─▶ (収益計測) ─▶ reviewing
  (台帳B)    (06_research)   (AI評価完了)     (人間のみ)  (ExecutionRun) (人間確認)              (人間/AI)
```

- 段階の正本は **Vault のシナリオ status**（[[収益化シナリオ承認フロー]]）
- progress 側は **approved 以降の実行状況**（ExecutionRun）を担当
- approved より前（candidate/pending_approval）は **表示のみ**。progress から承認・着手はしない

## 3. 表示形式 3 案の比較

| 案 | 形式 | 長所 | 短所 | 放置防止力 | 採否 |
|---|---|---|---|---|---|
| A. パイプライン（カンバン列） | 段階=列、シナリオ=カード | 全段階の滞留が一目。WIP が見える | 時間軸が弱い | 高 | **採用（主画面）** |
| B. ガント（時間軸バー） | シナリオ×時間 | 着手〜公開の所要時間が見える | 段階の滞留が見にくい・データ点が少ない初期は過剰 | 中 | 補助（Phase2） |
| C. 状態遷移図 | ステートマシン可視化 | ルール逸脱（candidate→直progress 等）の検出に有効 | 件数が増えると一覧性低い | 低 | 補助（監査用） |

→ **主画面=A（パイプライン列）**。各カードに「最終更新からの経過日数」を出し**滞留を赤表示**（放置防止の核）。B/C は Phase2 以降の補助。

## 4. 画面構成（主画面: 収益パイプライン）

- ヘッダ: 集計（候補数 / 承認待ち / 承認済 / 実装中 / 公開済 / 停滞件数）
- 本体: 7 列のカンバン（上記段階）。列ごとに WIP 件数
- カード（1 シナリオ）:
  - タイトル / 対象アプリ / revenueImpact バッジ（high/medium/low）
  - status / 最終更新からの経過日数（閾値超で赤）
  - スコア合計（妥当性評価）/ 次の1作業
  - approved 以降は紐づく ExecutionRun の runStatus / 最新 runId
- フィルタ: revenueImpact / 対象アプリ / 停滞のみ
- ソート: **収益化インパクト優先**（既定）→ 停滞日数

## 5. データ構造

### 5-1. 新規: MonetizationScenario（Vault シナリオの投影。progress 側は読み取り主体）

```
MonetizationScenario {
  scenarioId: string            // candidate-001 等
  title: string
  status: 'candidate' | 'pending_approval' | 'approved' | 'rejected'
        | 'hold' | 'in_progress' | 'published' | 'reviewing'
  revenueImpact: 'high' | 'medium' | 'low' | 'none'
  targetApp: string
  scoreTotal?: number           // 妥当性評価 合計 /40
  firstAction?: string          // 最初の1作業
  vaultPath: string             // 05_monetization/scenarios/xxx.md
  createdAt: string
  updatedAt: string             // 停滞判定の基準
  approvedBy?: string           // 人間のみ。空=未承認
  linkedRunIds: string[]        // approved 後の ExecutionRun.runId
}
```

### 5-2. 既存 ExecutionRun との接続（型は変更しない）

- 既存 `ExecutionRun`（`types/execution-run.ts`）はそのまま利用
- 接続キー: `ExecutionRun.targetTodoId` または新設 `scenarioId` を `targetTodoTitle` 規約で紐付け
- 段階マッピング:
  - `approved` 着手 → 関連 ExecutionRun `runStatus: running/partial` = 列「実装中」
  - ExecutionRun `completed` かつ シナリオ `published` = 列「公開」
  - `reviewing` = 列「改善」
- **progress スキーマ破壊的変更はしない**。MonetizationScenario は別 JSON（例 `data/monetization-scenarios.json`）として追加し、ExecutionRun と疎結合

## 6. 既存 progress との統合案

- 追加ルート（実装は別ToDo）: `/monetization`（パイプライン画面）
- データ供給: 当面は **Vault の scenarios/*.md を人手 or バッチで JSON 化**（Phase A 思想に合わせ自動同期は後段）
- 既存 ExecutionRun 一覧画面とは**相互リンク**（カード→該当 run、run→該当シナリオ）
- approved 化 UI は **置かない**（誤操作で AI/画面が承認する事故を構造的に防ぐ。承認は Vault の status 人手編集が正本）

## 7. 次に実装するToDo（実装は承認後・本Issueでは作らない）

1. `data/monetization-scenarios.json` スキーマ確定＋サンプル投入（候補3件流用）
2. Vault scenarios/*.md → JSON 変換スクリプト設計（手動実行）
3. `/monetization` パイプライン画面（A 案・読み取り専用）実装
4. 停滞ハイライト（updatedAt 経過日数閾値）実装
5. ExecutionRun 相互リンク

> 1〜5 は **approved 後に人間が progress 投入 ToDo 化**する。AI は progress へ送らない。

## 未対応点
- Vault→progress のデータ供給を手動とするか半自動とするかは Phase B 判断（[[../05_monetization/progress連携基準]] と整合確認が必要）
- 停滞日数の閾値（何日で赤か）は未確定。仮に「pending_approval=7日 / approved未着手=3日」を初期仮説とした

## 次の一手
1. ChatGPT で本設計＋画面モックをレビュー（観点: 統合案の疎結合性・放置防止設計）
2. 人間が「実装ToDo 1〜5」を progress 投入するか判断（承認後のみ）
3. 閾値（停滞赤表示）を運用しながら調整

## 関連
- 画面モック → [[progress_画面モック]]
- [[収益化シナリオ承認フロー]] / [[progress連携基準]] / [[既存資産_収益転用候補]]
- 連動シナリオ: [[scenarios/candidate-001]]
- Issue: kaeru07/vault#4
