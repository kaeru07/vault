---
date: 2026-06-08
task: 実行プロンプトの禁止事項からdeployを除外（注意扱いへ）・riskFlags連動の安全ガード共通化
runId: 20260608-173559
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260608-133645, 20260608-143957]
commitHashes: []
---

# 2026-06-08 実行プロンプトの禁止事項から deploy を除外（注意扱いへ）

---

## 1. 作業目的

- Factory 判定（dispatch→queue→runner）では deploy のみの Epic を自動実行対象にしたが、**Claude / Codex へ渡す実行プロンプトの「禁止事項」に deploy がハードコードで残存**していないか確認し、残っていれば「注意」扱いに直す。
- 期待: deploy のみは止めず、実装・build・lint・typecheck・必要な push まで進める。本番反映/外部公開だけ事後報告・確認事項に。危険6フラグは禁止のまま。

---

## 2. 確認結果（deploy 禁止文言は残っていたか → **2箇所に残っていた**）

| 箇所 | 内容 |
|---|---|
| `lib/factory-dispatch.ts:178`（Claude Factory プロンプト [6]禁止事項） | `課金 / 本番DB / 認証情報 / deploy / migration / destructive / secret・token・.env 操作をしない` ← deploy がハード禁止 |
| `lib/operations-store.ts` `CODEX_SAFETY_GUARD`（Codex 引き継ぎプロンプト） | `Codexで実行してはいけない作業: … / deploy / …` ← deploy がハード禁止 |

deploy のみの Epic でも、実行プロンプト上は「deploy するな」と指示されており、**判定（自動実行可）とプロンプト（deploy禁止）が不一致**だった。

---

## 3. 実施内容（修正）

- **`lib/epic-contract.ts`**: `buildExecutionGuard(riskFlags)` を新設。`forbidden[]`（課金 / 本番DB / 認証・secret・token・.env / migration / destructive / 外部公開 / doneCriteria外変更 / Approval・Decision待ち / 自動ループ）と `cautions[]`（deploy 時のみ「デプロイ注意」）を**分離生成する単一ソース**。
- **`lib/factory-dispatch.ts`**: Claude Factory プロンプトの `[6]禁止事項` を `buildExecutionGuard` 駆動に変更。deploy を禁止リストから除去し、deploy がある場合のみ `[6-1] 注意事項` に「デプロイ注意」を追加。
- **`lib/operations-store.ts`**: 静的 `CODEX_SAFETY_GUARD` を `codexSafetyGuard(riskFlags)` 関数化。deploy を「してはいけない」から除去し、deploy 時は注意行を追加。`generateCodexPrompt` が `epic.riskFlags` 連動でガード生成。
- `CODEX_DENY_SIGNALS`（タイトル語の deploy/デプロイ）は**意図的に不変**（deploy タイトルのタスクは引き続き Codex 非委譲＝Claude 実行に回す安全側維持）。

---

## 4. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `lib/epic-contract.ts` | `buildExecutionGuard(riskFlags)` 新設（forbidden / cautions 分離） |
| `lib/factory-dispatch.ts` | Claude プロンプト [6] を helper 駆動化。deploy→[6-1]注意へ |
| `lib/operations-store.ts` | `codexSafetyGuard(riskFlags)` 化。deploy を禁止から除外・注意行追加 |

---

## 5. 検証結果（data/real 実測・テストEpicは検証後削除し完全復元）

- typecheck: OK（tsc 0 error）/ build: OK（next build 41/41）/ lint: OK（0 error）
- deploy のみ `test-deploy-only`: **candidates 入り=true・picked**。生成プロンプトに「deploy禁止/やるな」系 **出ない**。[6]禁止事項に deploy なし、[6-1] に「デプロイ注意」のみ。
- 本物 **URL一覧 `epic-progress-url`**: candidates 入り=true・deploy 禁止系 **出ない**（[6-1]デプロイ注意）。
- 本物 **動作確認Todo `epic-progress-todo`**: candidates 入り=true・deploy 禁止系 **出ない**。
- `test-billing`: `safetyStatus=blocked` / `blockedReason: riskFlags: billing（要承認）` ← 高リスクは引き続き承認待ち。
- データ: real は開始時スナップショットと**バイト一致**、TEST=0、誤生成 `data/sample/epics.json` も除去済み。

---

## 6. 危険ポイント

- deploy を実行プロンプト上も「進めてよい」に変えたが、**本番反映・外部公開・.env変更・本番破壊は引き続き明示禁止/注意**で多層防御。
- 課金 / 本番DB / 認証 / migration / destructive / 外部公開 は forbidden のまま。billing 等は dispatch でも blocked。
- `buildExecutionGuard` が Claude/Codex 両プロンプトの単一ソースになり、文言のズレを構造的に防止。

---

## 7. 次にやるべきこと

- 前々(9)+前(6)+本(3)=**計18ファイル**を選択 commit/push（無関係 WIP・データ JSON 除外）。
- `pm2 restart progress` で本番反映。
- AI工場オペセンEpic の doneCriteria/priority 補完。

---

## 8. ChatGPT レビュー依頼文

```
対象アプリ: progress / runId: 20260608-173559（前段: 20260608-133645, 20260608-143957）
変更: 実行プロンプトの禁止事項から deploy を除外し「デプロイ注意」へ。
  Claude(factory-dispatch [6]禁止事項) と Codex(CODEX_SAFETY_GUARD) の両方を
  riskFlags連動の共通ヘルパー buildExecutionGuard で生成するよう統一。
  deployのみ→[6-1]注意事項(実装/build/lint/typecheck/必要なpushまで可・本番反映は事後報告)。
  課金/本番DB/認証/migration/destructive/外部公開は禁止のまま。

確認観点:
1. 安全性: deployを注意扱いにしても、本番破壊/.env変更/課金/DB変更が
   プロンプト上で禁止され続けているか。多層防御として十分か。
2. Claude/Codex両プロンプトのガードが buildExecutionGuard 単一ソースで一致しているか。
3. CODEX_DENY_SIGNALSのdeployキーワードを残した判断(deployタイトルはCodex非委譲)は妥当か。
4. 「本番反映は事後報告/確認事項」の運用が前進優先ルールと整合しているか。
```
