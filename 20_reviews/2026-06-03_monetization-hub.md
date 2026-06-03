---
date: 2026-06-03
task: Progress に収益化候補管理（Monetization Hub）機能を追加（/monetization・/monetization/[id]）
runId: 20260603-144109
targetApp: progress
monetizationImpact: high
theme: [app-strategy, monetization, workflow]
relatedRunIds: [20260602-204657, 20260602-202214]
commitHashes: []
---

# Monetization Hub 実装（2026-06-03）

## 1. 作業目的
AI工場が発掘した収益化候補を Progress 上で管理し、「発掘 → 調査 → 人間レビュー → Epic化承認 → Factory実行」の導線を作る。候補発掘と Epic化を分離し、人間の承認ゲートを強化する。Epic化は人間操作のみ（自動Epic化を禁止）。

## 2. 実施内容
- データ型 `types/monetization.ts`、ストア `lib/monetization-store.ts`、UI色分け `lib/monetization-ui.ts` を新設。
- API: `/api/monetization`（GET一覧/POST作成）、`/api/monetization/[id]`（GET/PATCH: status・research・部分更新）、`/api/monetization/[id]/promote`（Epic化）。
- ページ: `/monetization`（一覧・状態フィルタ・カード）、`/monetization/[id]`（全セクション詳細）。
- コンポーネント: MonetizationList / ApprovalActions（保留/却下/再調査/承認/Epic化）/ AddCandidateButton。
- ナビ（Top/Bottom）に「収益化」を追加。
- `data/real/monetization-candidates.json` を新設し、発掘済み6件（BirdLog/AnglerLog 他）を seed。

## 3. 変更ファイル
- 新規: types/monetization.ts, lib/monetization-store.ts, lib/monetization-ui.ts, app/api/monetization/route.ts, app/api/monetization/[id]/route.ts, app/api/monetization/[id]/promote/route.ts, app/monetization/page.tsx, app/monetization/[id]/page.tsx, components/monetization/{MonetizationList,ApprovalActions,AddCandidateButton}.tsx, data/real/monetization-candidates.json
- 変更（最小）: components/navigation/TopNav.tsx, BottomNav.tsx（リンク追加のみ）
- 触らない: factory-runner / factory-schedule / done-criteria / 既存API・既存ページ・既存Epic（createEpicの追記のみ）

## 4. 検証結果
- typecheck（tsc --noEmit）OK / lint OK / build OK / pm2 restart deploy。
- API実機: 一覧6件、詳細200、候補追加、status更新、調査ログ追加、Epic化（epic生成+status EpicCreated+ExecutionRun source=monetization_hub）を確認。
- 重複防止: 二重Epic化=409 / 既存アプリ mahjong=409 / 既存slug progress=409 でブロック。
- テスト生成物（test epic / test run / test candidate 3件）は確認後に除去しデータをクリーン化。

## 5. 未対応
- AI工場の 11:00/23:00 定例との配線（候補自動作成）は統合点 `POST /api/monetization` を用意したのみ。factory-schedule への自動投入配線は本実装では未実施（リスク回避・別Epic推奨）。
- Vault↔Hub の双方向同期は未実装（下記方針提案のみ）。

## 6. 危険ポイント
- Epic化は epics.json を追記する（createEpic）。重複チェックを通すが、slug衝突の判定は英数スラッグベースで日本語のみ名称は弱い → targetApp の明示を推奨。
- 自動Epic化は実装しない設計を厳守（automationから promote を呼ばない）。

## 7. 次にやるべきこと
- 定例発掘業務が候補を `POST /api/monetization` で投入するよう配線。
- Vault→Hub 初期同期、Hub→Vault status 書き戻しの方針合意（下記）。
- BirdLog 等の競合ASO実査 → score確定 → 人間がEpic化判断。

### Vault との同期方針（提案）
- **正本の分離**: 「候補の状態・スコア・承認」は **Hub（monetization-candidates.json）を正本**、「発掘の経緯・調査ノート」は **Vault を正本** とする。二重管理を避ける。
- **Vault → Hub（取り込み・自動）**: 定例発掘が候補を作る時、Vault の収益化候補一覧/research を参照して `POST /api/monetization` する（links.vault に出典パスを残す）。
- **Hub → Vault（書き戻し・手動/半自動）**: Approved / EpicCreated になった候補だけ、Vault の収益化候補一覧テーブルに status と epicId を1行反映（Phase A は手動、将来 export API）。
- **競合回避**: Hub は JSON、Vault は ob sync という既存の責務分離に合わせ、相互の自動上書きはしない。

## 8. ChatGPTレビュー依頼文
```
対象: progress / Monetization Hub 実装 / runId 20260603-144109
Progress に収益化候補管理機能を追加しました（/monetization 一覧, /monetization/[id] 詳細）。発掘→調査→人間レビュー→Epic化承認→Factory実行の導線で、Epic化は人間ボタンのみ（自動Epic化禁止）。重複チェック（既存アプリ/Epic/Epic化済み候補）を通った時だけ epics.json に追記し、ExecutionRun(source=monetization_hub)を記録します。
確認したい観点:
1. 「候補発掘は自動・Epic化は人間のみ」のガードは設計上十分か（automationからpromoteを呼べない構造になっているか）。
2. 重複チェックの粒度（英数slug照合）に穴はないか。日本語のみ名称への対策は。
3. Epic Contract自動生成（doneCriteria/riskFlags=external_publish/approval_required）の妥当性。
4. Vault↔Hubの同期方針（正本分離・書き戻し手動）の妥当性。
5. iPhone優先UI（横スクロールなし・カード・色分け）の使い勝手。
```
