---
date: 2026-05-16
task: ChatGPT Inbox 取り込み機能の実装
runId: 20260516-171158
targetApp: progress
monetizationImpact: low
theme: [workflow, obsidian]
relatedRunIds: [20260515-221729, 20260516-020102]
commitHashes: []
---

# 2026-05-16 ChatGPT Inbox 取り込み機能の実装

> commitHashes 空。理由: progress は ny01 リポ運用で本作業では push しない（指示外）。本レビュー md は obsidian-vault 側 commit で push。

---

## 1. 作業目的

- なぜ: ChatGPT が Vault に投函した ToDo メモを progress に取り込みたい（投函 `00_inbox/ChatGPT ToDo投函箱の最小テスト.md` の要望）
- 背景: ChatGPT → obsidian-vault → progress の ToDo 連携を成立させ、思いついた ToDo を蓄積する仕組みの第一歩
- 期待効果: 投函を 1 ボタンで progress の pending_approval タスク化し、承認フローに乗せられる

---

## 2. 実施内容

- 投函仕様（ファイル内 ToDo候補）に準拠して実装:
  - `types/inbox.ts` / `lib/inbox-reader.ts`（00_inbox 走査・frontmatter/ToDo候補抽出・`inbox-state.json` で内容ハッシュ冪等管理）
  - `GET /api/inbox`（未取込/取込済一覧）/ `POST /api/inbox/import`（ToDo化）
  - `/inbox` 画面 + `InboxCard`（確認ダイアログ付き ToDo化ボタン）+ TopNav に Inbox 追加
- 取り込み先: `projectId=chatgpt-inbox` / `status=pending_approval` / `assignee=user` / `memo=本文` / `doneCriteria=ToDo候補` / `targetPath=投函相対パス`
- 読み込み元: 既定 `obsidian-vault/00_inbox`（env `CHATGPT_INBOX_PATH` で上書き可）。`source:chatgpt` または `chatgpt-todo/` を対象、運用 md（inbox/README/未整理メモ）は除外

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/ny01/progress/types/inbox.ts | 新規 |
| apps/ny01/progress/lib/inbox-reader.ts | 新規（走査+冪等管理） |
| apps/ny01/progress/app/api/inbox/route.ts | 新規（GET 一覧） |
| apps/ny01/progress/app/api/inbox/import/route.ts | 新規（POST ToDo化・既存 addTask 再利用） |
| apps/ny01/progress/app/inbox/page.tsx | 新規（Inbox 画面） |
| apps/ny01/progress/components/inbox/InboxCard.tsx | 新規（確認付きボタン） |
| apps/ny01/progress/components/navigation/TopNav.tsx | /inbox ナビ 1 行追加 |

既存 addTask / progress-writer / reader / queue / execution-runs ロジックは**非変更**。

---

## 4. 検証結果

- lint: ✔ No ESLint warnings or errors
- build: pass（`/inbox`・`/api/inbox`・`/api/inbox/import` 認識）
- 手動（隔離環境: 別ポート3035 + 一時 PROGRESS_DATA_PATH + CHATGPT_INBOX_PATH=sync-vault/00_inbox）:
  - GET /api/inbox → 投函 1 件 / todoCandidates 4 抽出
  - POST import 1回目 → ok / pending_approval / assignee=user / doneCriteria 4 / targetPath 設定
  - POST 2回目 → alreadyImported=true（冪等）/ chatgpt-inbox の task 件数 1 のまま
  - inbox-state.json 記録 / GET 再取得で imported=true
  - **本番 data/real 無変更**（chatgpt-inbox 不在・inbox-state 不在 = 隔離成立）
- 機密パターン事前チェック: OK
- ob sync: 後述 / git push(vault): 後述

---

## 5. 未対応

- 本番3010 の再起動（/inbox 本番反映には必要・ユーザー判断）
- pending_approval → todo 昇格（承認）フローの UI 整備
- ToDo候補を行単位で個別タスク化するオプション（現状 1 投函 = 1 タスク + doneCriteria）
- `chatgpt-todo/` サブフォルダ運用の ChatGPT 側への周知

---

## 6. 危険ポイント

- 本番未反映（再起動が必要）。再起動するまで本番 /inbox は出ない
- 投函ファイル本体は**非変更**（設計判断: 冪等が内容ハッシュ基準のため、ファイルを書き換えると「未取込」に戻る。処理済み管理は inbox-state.json 側に集約）
- 読み込み元デフォルトは obsidian-vault/00_inbox。現投函は sync-vault 側にあり、本記録の mirror→push で obsidian-vault 側へ反映される
- pending_approval は「Claude は着手しない」ステータス。承認はユーザー操作前提
- テスト用一時 dir `/tmp/tmp.dUBN4p0Xde` は rm -rf がポリシー拒否で残置（/tmp 配下・実害なし）

---

## 7. 次にやるべきこと

- 本番3010 再起動可否の判断（ユーザー）
- pending_approval 承認 UI（chatgpt-inbox の task を todo へ昇格）
- 行単位タスク化オプション
- 観察: inbox-state.json の肥大、投函フォーマットのばらつき耐性

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress (/inbox 機能)
作業: ChatGPT Inbox 取り込み機能の実装
runId: 20260516-171158
日付: 2026-05-16
GitHub: progress は push せず / obsidian-vault 側は別途

## 作業目的
- ChatGPT が Vault に投函した ToDo を progress に取り込む(投函要望に準拠)

## 実施内容
- lib/inbox-reader(00_inbox走査+inbox-state.json冪等), /api/inbox, /api/inbox/import, /inbox画面, TopNav追加
- 取り込み: chatgpt-inbox プロジェクトへ pending_approval/assignee=user で addTask(既存再利用)

## 検証結果
- lint ✔ / build pass(/inbox /api/inbox /api/inbox/import)
- 隔離環境: 投函検出→import(pending_approval)→2回目冪等→state記録→imported反映
- 本番 data/real 無変更(隔離成立)

## 未対応
- 本番3010再起動(ユーザー判断) / 承認→todo昇格UI / 行単位タスク化

## 危険ポイント
- 本番未反映 / 投函ファイル非変更(冪等設計) / pending_approval承認運用

## 確認したい観点
- 冪等を内容ハッシュで持つ設計は妥当か(ファイル更新時の挙動)
- 読み込み元を obsidian-vault 既定にした判断
- pending_approval/assignee=user の運用が承認フローとして妥当か
- 1投函=1タスク+doneCriteria vs 行単位タスク化、どちらを本命にすべきか
- 既存 addTask 再利用でスキーマ非破壊か
````

---

## 関連

- progress runId: 20260516-171158（正本）
- 関連 run: 20260515-221729（Codex連携機能）/ 20260516-020102（Codex SDK実験）
- 投函元: 00_inbox/ChatGPT ToDo投函箱の最小テスト.md
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
