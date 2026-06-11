---
date: 2026-06-12
task: 工場停止条件の変更（レビュー件数では止めない）+ Inbox 4セクション構成へ
runId: 20260612-005632
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260611-211508, 20260611-022341]
commitHashes: [e9d6ce7]
reviewFileCommit:
---

# progress 工場停止条件変更レポート

## 作業目的

これまでレビュー待ちが増えるとAI工場が自動停止する設計（not_reviewed>10で減速・>20で起動スキップ）だった。重要な運用方針変更として「工場を止める条件」と「後回し可能な項目」を分離し、レビュー100件でも稼働可能にする。

## 実施内容

**新しい停止条件（これのみ）**:
1. 危険判断待ち（本番DB・migration・課金・認証・deploy・external_publish・destructive 系の承認が pending）→ Factory自動実行を全体停止
2. Goal未設定の Epic → 該当Epicのみ選定から除外（全対象Epicが未設定なら実質停止）
3. 人間作業（ストア公開・課金設定・契約・本人確認）→ AIはそもそも触らない（他のEpicは稼働継続）

**停止対象外（放置しても止めない）**: レビュー / 公開確認 / Vercel確認 / 実装確認 / AIレビュー / ExecutionRunレビュー / Knowledgeレビュー / 次Epic候補 / 改善候補 / 収益化候補

**Inbox 4セクション構成**:
- ① 今日の判断 = 工場停止要因のみ（危険判断→方針選択→人間作業の優先順・最大3件・約3分）
- ② レビュー = 検収。「放置しても工場は止まりません」明記。5件ずつ + AIにまとめて確認させるボタン
- ③ Epic候補 = 実行許可（改善案・次Epic・収益化候補）。「放置可能」
- ④ AI保留 = 件数のみ「AIが整理中です」

**ホーム変更**: 「今日の判断 残りN件」を主表示。レビュー件数は「レビュー待ち（参考）/ たまっても工場は止まりません」へ。工場状態も「停止中（確認待ちが多いため）」→ 停止時は「停止中（あなたの判断待ち）」と原因明示。

**副修正**: 入れ子メタ候補（「レビュー起点: おすすめ承認: ×5重 + の次Epic候補×3連」）が見出しに内部語のまま漏れていたのを修正（プレフィックス除去のループ化 + 入れ子2回以上はAI保留へ自動退避）。

## 変更ファイル

- progress/lib/factory-metrics.ts — backpressure を FactoryBlockers 基準へ（slow_down 廃止）
- progress/lib/factory-runner.ts — not_reviewed チェック削除 → 危険判断pendingスキップ + Goal未設定Epic除外
- progress/lib/command-center.ts — InboxView 4セクション化 + メタ候補対応 + 文言更新
- progress/lib/inbox-labels.ts — DANGER_CATEGORIES / isReviewApprovalOptions 共有化
- progress/app/decide/page.tsx・page.tsx・guide/page.tsx・legacy/home/page.tsx — UI追随
- progress/docs/operations/current-operating-model.md — 停止条件表・変更履歴

## 検証結果

- tsc 0 / lint 0 / build 成功 / 全ルート200（pm2反映済み）
- **実測**: metrics API で blockers={danger:0, goalUnset:0, runnable:3} / level=ok / notReviewed=32 → ホーム「稼働中（毎日自動実行）」。旧ロジックなら 32>20 で停止していたケースで稼働継続を確認
- 禁止語スキャン: レビュー起点 / おすすめ承認 / Factory schedule / Knowledge / ExecutionRun / runId / reviewed / not_reviewed / candidate / suggested / epic- / の次Epic候補 → 可視テキスト0件
- iPhoneビューポート（390×844）: ![[../attachments/screenshots/2026-06-12-decide-v4-after-top.png]] / ![[../attachments/screenshots/2026-06-12-decide-v4-after-full.png]] / ![[../attachments/screenshots/2026-06-12-home-v4-after.png]]
- git push: 97a9741..e9d6ce7 成功

## 未対応

- 危険判断pending時の全体停止は現在0件のため未発火（次に危険承認が発生した際に実地確認）
- メタ候補を生成する側（次Epic候補生成）の再帰抑制は未実装（表示側でAI保留に退避するのみ）
- iPhone実機での最終確認はユーザー待ち

## 危険ポイント

- レビューが無制限に溜まる設計になった。Knowledge生成はレビュー済みrunから行われるため、レビュー放置が長期化すると学習ループ（closed_loop_rate）が回らない。「AIにまとめて確認させる」の定期実行化が次の手
- 「人間作業=全体停止ではなく該当項目のみ待ち」は仮説判断（全体を止めると本末転倒のため）。spec の文言とは解釈差がある
- 検収系承認（問題なし/フォローアップ option持ち）は external_publish カテゴリでも停止要因にしない判別を入れた

## 次にやるべきこと

- 危険判断発生時の停止動作の実地確認
- AI一次レビューの定期自動実行（レビュー滞留の自動消化）の検討
- 次Epic候補生成の再帰抑制（メタ候補の発生源対策）

## ChatGPT レビュー依頼文

```
progress アプリのAI工場停止条件変更のレビューをお願いします。

対象: progress Factory停止ロジック + Inbox 4セクション
runId: 20260612-005632 / commit: e9d6ce7（kaeru07/ny01 main）

変更概要:
- 旧: not_reviewed>10で減速・>20で工場停止 → 新: レビュー件数では止めない（32件で稼働継続を実測）
- 新停止条件: 危険判断待ち(全体停止) / Goal未設定Epic(該当Epicスキップ) / 人間作業(AI対象外)のみ
- Inboxを4セクション化: ①今日の判断=停止要因のみ最大3件 ②レビュー(放置可) ③Epic候補(放置可) ④AI保留(件数のみ)

確認したい観点:
1. レビュー無制限滞留を許容した場合のKnowledgeループ(学習)停滞リスクと対策（AI一次レビューの定期自動実行は妥当か）
2. 「人間作業=該当項目のみ待ち(全体は止めない)」の解釈は妥当か
3. 検収系承認をexternal_publishカテゴリでも停止要因から除外する判別の安全性
4. Goal未設定Epicのスキップが「静かな放置」にならないか（①今日の判断に出るので気づける想定）
5. メタ候補(入れ子レビュー起点)の発生源対策の優先度
```
