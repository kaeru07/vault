---
title: market-research-hub 設計（提案ドラフト）
type: monetization-research
status: proposal
created: 2026-05-18
updated: 2026-05-18
source: 00_inbox/chatgpt-import/05_次にやるToDo候補.md（高優先・出典 2026-05-12〜13 リソース活用法/市場調査）
---

# market-research-hub 設計（提案ドラフト）

> `05_次にやるToDo候補.md` 高優先の **設計部分のみ**を提案ドラフト化。
> 「scrape-lab-v2 本体を GitHub 管理化」は**別アプリの repo 操作**のため本ドラフトでは実施せず、
> ユーザー判断に委ねる（[[../CLAUDE.md 由来]] アプリ作業は各リポジトリ内で完結 / 別アプリ開発は /vloop 停止条件）。
> 本ファイルは設計のみ。**実装・本番コード変更はしない。**

## 目的

定期調査（[[../05_monetization/定期調査運用]]）と既存スクレイピング資産（scrape-lab-v2 / note）の出力を、
**収益化インパクト順に集約・再利用できる調査ハブ**として設計する。調査結果が会話ログや散発ファイルに
埋もれず、candidate シナリオ起票の一次入力になる状態を作る。

## スコープ

- 含む: データ構造設計 / 蓄積フォーマット / 収益化インパクト順の集約方法 / 既存資産との接続設計
- 含まない: scrape-lab-v2 の GitHub 管理化（別アプリ repo 操作＝ユーザー）/ 実装 / runner のコード

## データ構造（案）

### 調査レコード（NDJSON 1 行 = 1 調査単位）

```
{
  "researchId": "YYYYMMDD-<topic-slug>",
  "date": "YYYY-MM-DD",
  "topic": "麻雀アプリ市場",
  "手段": ["アプリリリース"],          // 収益化手段台帳の行
  "sources": [{"url":"...", "type":"公式|一次|SNS|推測", "n": 10}],
  "findings": "収益性/競合の要点（機密除外）",
  "scores": {"収益":3,"速度":4,"流用":4,"広告":4,"動画":4,"継続":3,"競合回避":2,"根拠":4},
  "scoreTotal": 28,
  "revenueImpact": "high|medium|low|none",
  "linkedScenario": "candidate-001",      // 起票済みなら
  "freshness": "YYYY-MM-DD",              // 鮮度判定用
  "createdBy": "claude|codex|chatgpt"
}
```

- 蓄積先（案）: `06_research/_hub/research-log.ndjson`（追記専用・1行1レコード）
- 妥当性評価メタは [[../05_monetization/調査手段の妥当性評価]] と同一項目を必須化（推測/件数不明は candidate 止まり）

### 集約ビュー（Markdown 自動生成は Phase 後送り・当面手動）

- `06_research/_hub/index.md`: revenueImpact 降順 → scoreTotal 降順で一覧
- 鮮度切れ（90日 / 変化の速い手段は 30〜60日）を再調査対象としてフラグ

## 既存資産との接続

| 資産 | 接続方法 | 備考 |
|---|---|---|
| scrape-lab-v2 | 取得 JSON を hub の sources 入力に手動転記（当面） | GitHub 管理化は別途ユーザー判断 |
| note（スクレイパ） | 同上 | 用途特定後に統合 |
| 06_research/*.md | 既存調査を researchId 付きで hub log へ後追い登録（任意） | 遡及は必須にしない |
| 05_monetization/scenarios | linkedScenario で双方向参照 | candidate 起票の一次入力 |
| progress ExecutionRun | 調査作業も POST 済（既存ルール） | hub は調査の正本、progress は作業の正本 |

## Claude/Codex 共通 runner（設計のみ・実装しない）

- 入力: 手段台帳の未調査/鮮度切れ行 → 調査ドラフト生成
- 出力: 上記 NDJSON 1 レコード（人間レビュー前は status: draft）
- **runner 実装は本ドラフト外**。定期調査運用の第2段階（半自動）で別途設計（[[../05_monetization/定期調査運用]]）

## 未対応点 / 仮説
- scrape-lab-v2 の GitHub 管理化は別アプリ repo 操作のため未実施（ユーザー判断）。仮説: 管理化後に hub の sources 自動取り込みが可能になる
- NDJSON 蓄積先 `06_research/_hub/` は新設提案。フォルダ作成・運用開始はユーザー採用後
- 自動集約（index.md 生成）は Phase 後送り。当面手動集約の仮説で確定

## 次の一手
1. ユーザーが hub データ構造（NDJSON スキーマ）を採用するか判断
2. 採用時 `06_research/_hub/` を新設し既存3件（candidate-001〜003 由来調査）を初期投入
3. scrape-lab-v2 の GitHub 管理化要否を別タスクでユーザー判断

## 関連
- [[../05_monetization/定期調査運用]] / [[../05_monetization/調査手段の妥当性評価]] / [[../05_monetization/収益化手段台帳]]
- [[2026-05-18_収益化定期調査_初回]]（hub への初期投入候補）
- 出典候補: 00_inbox/chatgpt-import/05_次にやるToDo候補.md
