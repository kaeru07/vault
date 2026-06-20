---
date: 2026-06-20
task: 試した方がいい系(残16件)に「試すと何ができる/どんな良い影響か」を詳細追記＋enrichエンドポイント新設
runId: 20260620-141529
targetApp: progress
monetizationImpact: medium
theme: [market-research, app-strategy, workflow]
relatedRunIds: [20260620-140003, 20260620-135942]
commitHashes: [0bc6394]
---

# 2026-06-20 試した方がいい系ゴールに詳細を追記

## 1. 作業目的
- ユーザー要望「残した試した方がいい系に、もっと試したらどういい影響があるか・どんなことができるかを詳細に追記」。
- 対象16件は enables/pros が空（先の normalizeGoal 脱落バグで消失）で、承認カードが summary フォールバックの薄い表示だった。

## 2. 実施内容
- `app/api/goals/enrich-proposals/route.ts` 新設: body `items[{title, enables, pros[], detail}]` を proposed ゴールへタイトル一致で適用（enables/pros 上書き・detail は notes 先頭へ「🔍試す価値」マーカー付きで追記・重複防止）。
- 16ツール個別に詳細を作成し適用（updated=16 / notFound=0）。各々ユーザー文脈に接続:
  - Claude Code / Agent SDK → 工場の自律度・changedFiles記録の根治
  - OpenAI Codex SDK / Goal mode / Appshots → Fallback自動化・ゴール自走・UI自動検証
  - Codex Sites / Google AI Studio → 収益化LP・モバイルプロト高速化
  - GitHub MCP secret scanning → push前機密チェック自動化
  - Figma MCP → iPhone UI改善 / Remote MCP → モバイル↔VPS接続
  - MCPセキュリティ対策/全般 → 連携の安全土台 / ADK・AI Studio → 新規収益アプリの種

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| app/api/goals/enrich-proposals/route.ts | 新規: proposedゴールにenables/pros/detailをタイトル一致で追記 |

（データ操作: research系proposed 16件に enables/pros/notes を追記）

## 4. 検証結果
- typecheck/build/lint: OK
- 手動: pm2 restart後 /decide?tab=goalApproval 200。「できるようになること」「メリット」「🔍試す価値」「収益化ロードマップに直結」等の追記が描画。research proposed 16件すべて enables/pros 有。
- 機密スキャン: OK / ob sync: n/a / git push: 0bc6394 pushed

## 5. 未対応
- アプリ系27件への同様の肉付けは未（必要なら別途）。
- 追記文面の質・粒度のユーザー確認。

## 6. 危険ポイント
- 提案カードの表示充実のみ。承認するまで自動実行されない安全側。

## 7. 次にやるべきこと
- アプリ系ゴールにも同様の詳細追記をするか判断。
- enrich APIをUI(ゴール編集)から呼べるようにするか検討。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: 試した方がいい系16件に詳細(できること/良い影響)を追記+enrich API新設
runId: 20260620-141529 / commit: 0bc6394

## 目的
承認待ちの試した方がいい系16件に、試すと何ができて・どんな良い影響があるかを
ユーザー環境(AI工場/Claude Code+Codex/複数アプリ/収益化/iPhone運用)に紐づけて詳述。

## 実施
/api/goals/enrich-proposals 新設(title一致でenables/pros/detail追記)。16ツール
個別に詳細作成し適用(updated16)。

## 検証
tsc/build/lint OK。/decide?tab=goalApproval 実描画200で追記描画確認。

## 確認したい観点
1. 各ツールの「できること/良い影響」の記述は具体的で承認判断に役立つか。
2. ユーザー環境への紐づけ(工場/収益化/iPhone)は的確か。
3. アプリ系27件にも同様の肉付けをすべきか。
````
