---
date: 2026-06-08
task: Factory判定修正の本番反映（commit/push + rebuild + pm2 restart）
runId: 20260608-184752
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260608-133645, 20260608-143957, 20260608-173559]
commitHashes: [9e6af71]
---

# 2026-06-08 Factory判定修正の本番反映

---

## 1. 作業目的

- 3 セッション分の Factory 判定修正（対象外誤判定 / 承認過多解消 / 実行プロンプトの deploy 注意化）を **本番反映**し、URL一覧Epic・動作確認TodoEpic を Factory で自動実行できる状態にする。

---

## 2. 実施内容

- **commit/push**: 孤立 commit は不可能（`app/epic/page.tsx` が未追跡の `FactoryGuideModal` / `FactoryStatusBar` に依存し、そこから多数の未追跡ファイルへ連鎖）と判明。coherent な最小単位として **progress アプリのソース 95 ファイル**を stage（他アプリ・`data/`・`.next`・`tsbuildinfo` は除外）→ 機密スキャン clean → **commit `9e6af71`** → `push origin main` 成功（`d30959e..9e6af71`）。
- **rebuild + restart**: `npm run build` 成功（41/41）→ `pm2 restart progress`（restarts=1 / online）→ `GET /epic=200`。
- 実 auto 実行（mode:auto+confirm）は**未実施**（autonomous なコード改変を伴うため、候補確認・dispatch プロンプト生成・dry_run までに留めた）。

---

## 3. 変更ファイル / コミット

- commit `9e6af71`: progress ソース 95 ファイル（Factory 判定 4 概念分離・riskFlags 2 分割・`buildExecutionGuard` 等の本セッション修正を含む、進行中だった Factory/recommended/automation 機能一式）。
- `data/`（実行時 JSON）と他アプリ（news-app / ai-trend-sns / anglerlog / birdlog）は**コミット外**。

---

## 4. 検証結果（本番 live 3010）

- typecheck: OK / build: OK（41/41）/ lint: OK
- `GET /epic` = 200。バッジ: ⚙ Factory対象 / ✅ 自律実行可 / ⚠ デプロイ注意 / 🛡 要承認 0 / 🚫 Factory対象外（構造不完全Epicのみ）
- `factory-dispatch` scan: `factoryEnabled=true`、**candidates = [epic-birdlog, epic-progress-url, epic-progress-todo]**、blocked = [epic-91: doneCriteria/priority 未設定]
- **URL一覧 `epic-progress-url`** / **動作確認Todo `epic-progress-todo`**: 個別 plan `safetyStatus=ok` / `executor=claude` / `promptType=claude_factory` / blockedReason なし
- dispatch プロンプト生成: **deploy 禁止文言なし / デプロイ注意あり**
- `factory-run` dry_run = 200（runner 正常）
- 機密スキャン: clean（.env/.key/secret なし。docs/factory-schedule の systemd/crontab はコメントアウト/dry_run のテンプレのみ）

---

## 5. URL一覧Epic / 動作確認TodoEpic の状態

| Epic | candidates | dispatch | プロンプト | 結論 |
|---|---|---|---|---|
| `epic-progress-url`（URL一覧） | ✅ 入る | safetyStatus=ok / claude | deploy禁止なし・注意あり | **自動実行可** |
| `epic-progress-todo`（動作確認Todo） | ✅ 入る | safetyStatus=ok / claude | deploy禁止なし・注意あり | **自動実行可** |

---

## 6. 危険ポイント

- commit `9e6af71` は progress の Factory 機能 WIP 一式を含む大きめのコミット。孤立 commit 不可のための判断。他アプリ・data は除外済み。
- 実 auto 実行は未トリガ（autonomous コード改変）。Factory UI / `factory-run` auto から人が実行する想定。
- `data/real` 実行時 JSON は一部 tracked / 一部 untracked が混在。今回は触らず（非破壊）。

---

## 7. 次にやるべきこと

- 必要なら `mode:auto + confirm` で 2 Epic を実起動（autonomous コード改変が走る点に留意）。
- AI工場オペセンEpic（epic-91）の doneCriteria/priority 補完で候補化。
- `data/real` の git 追跡方針の整理。

---

## 8. ChatGPT レビュー依頼文

```
対象アプリ: progress / runId: 20260608-184752 / commit: 9e6af71（pushed）
前段: 20260608-133645 / -143957 / -173559
実施: Factory判定修正を本番反映。progressソース95ファイルをcommit/push、
  rebuild + pm2 restart。本番(3010)でURL一覧Epic・動作確認TodoEpicが
  Factory candidates入り・dispatch ok・deploy禁止文言なし(デプロイ注意)を確認。

確認観点:
1. coherent最小単位として「progressソース一式」をcommitした判断は妥当か。
   孤立commit不可(依存連鎖)の状況で他に取れる手はあったか。
2. data/real実行時JSONをコミット外にした扱いは正しいか。一部tracked混在の整理方針。
3. 実auto実行を見送り候補確認・dry_runまでに留めた判断は妥当か。
4. 本番反映(rebuild+pm2 restart)とgit pushの責務分離は運用上問題ないか。
```
