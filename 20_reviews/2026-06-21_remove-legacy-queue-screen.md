---
date: 2026-06-21
task: 旧運用画面/legacy/queueを削除＋残り旧画面の削除可否を今日の判断へ
runId: 20260621-101304
targetApp: progress
monetizationImpact: none
theme: [workflow, app-strategy]
relatedRunIds: [20260621-095414]
commitHashes: [d44cf3e]
---

# 2026-06-21 旧運用画面 /legacy/queue 削除＋残りを今日の判断へ

## 1. 作業目的
- ユーザー指示「progressアプリの旧運用専用画面は消す。迷うなら今日の判断に加える」。

## 2. 実施内容
- 被リンク棚卸しで判定:
  - **削除**: `/legacy/queue`（非正本・互換のみ・正本は /queue と明示された旧 WorkQueue 画面）。
  - **迷う→エスカレーション**: /legacy/home(TopNav旧ナビ分岐に依存)・/approvals(7箇所被リンク)・/inbox(Vault連携)・/radar・/morning(work-queue依存の機能画面)。
- 削除実施: `git rm app/legacy/queue/page.tsx` ＋ 参照4箇所整理(TopNav.tsx 旧キューリンク削除 / app/queue/page.tsx 旧キューLink削除 / lib/nav-menu.ts エントリ削除 / SystemSpecification.tsx 記述を「旧キュー画面は廃止」へ更新)。
- エスカレーション: `【要判断】残りの旧運用画面を消すか決める(旧ダッシュボード/承認待ち/旧受信箱/レーダー/朝会)` を proposed ゴール(source=focus_decision)で今日の判断(ゴール承認)へ追加。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| app/legacy/queue/page.tsx | 削除 |
| components/navigation/TopNav.tsx | 旧キューリンク削除 |
| app/queue/page.tsx | 旧キューリンク削除 |
| lib/nav-menu.ts | /legacy/queue エントリ削除 |
| components/operations/SystemSpecification.tsx | 旧キュー廃止を反映 |

## 4. 検証結果
- next build OK / pm2 restart後 /legacy/queue→404、/queue・/legacy・/・/decide→200(他画面無事)・残参照0
- git push: d44cf3e

## 5. 未対応
- 残り旧画面(旧ダッシュボード/承認待ち/旧受信箱/レーダー/朝会)の取捨はユーザー判断待ち(今日の判断に追加済み)

## 6. 危険ポイント
- ページ削除は git で可逆。/approvals は7箇所被リンクのため自走削除せず escalate(壊さない判断)。

## 7. 次にやるべきこと
- ユーザーが残り旧画面の取捨を承認/選択 → その範囲で削除＋参照修正

## 8. ChatGPT レビュー依頼文
````text
対象: progress 旧運用画面の整理
runId: 20260621-101304 / commit d44cf3e
非正本の旧キュー/legacy/queueを削除し参照4箇所整理(404確認・他画面200)。残る旧画面
(旧ダッシュボード/承認待ち/旧受信箱/レーダー/朝会)は被リンク・依存差で迷うため今日の判断へ。
確認観点: /approvals(7被リンク)・/radar/morning(work-queue依存)は移植して消すべきか残すべきか。
````
