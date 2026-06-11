---
date: 2026-06-11
task: 今日の判断を「社長向け意思決定アプリ」へ強化（状況文タイトル・影響1行・3分類・3件制限+AI保留）
runId: 20260611-203856
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260611-194652, 20260611-124611]
commitHashes: [37f5e55]
reviewFileCommit:
---

# progress 今日の判断 v2（社長向け意思決定アプリ）レポート

## 作業目的

前回のリデザインでInboxは人間語化されたが、まだ「市場調査ビューのVault更新停止を調査・修正」のようなタスク名中心で、見た瞬間に「何が起きているか・なぜ必要か・放置するとどうなるか」が分からなかった。ゴール「毎日5〜15分だけ判断すればAI工場が回る」に向け、タイトルの状況文化・影響1行必須・3件制限を実装する。

## 実施内容

- **状況文変換**: describeSituation() がタスク名を「何が起きているか」へ自動変換。実例:
  - 「レビュー起点: 市場調査ビューのVault更新停止を調査・修正 の次Epic候補」→ 🚨 市場調査ビューの自動更新が止まっています
  - 「（Factory失敗の解消）epic-p2-factory-multiple-run- の失敗 Run を調査・修正する」→ 🚨 AI工場の自動作業が失敗しています
- **影響1行必須**: 全カードに「放置するとどうなるか / なぜ必要か」を表示（例: 放置すると毎日の市場調査結果が更新されません。）。ドメイン語からルールベースで推定
- **3分類変更**: 🚨問題（止まっている/失敗/修正必要）/ 📈改善（良くなる/自動化/効率化）/ ✅確認（公開確認/結果確認/レビュー確認/目標未設定）。カード左上チップのみ
- **3件制限+AI保留**: 優先順（問題→確認→目標未設定→改善）で今日は最大3件表示。残り40件は「AI保留」として件数のみ。上部サマリーに「今日の判断 残り3件 約3分 / AI保留 40件」
- **ホーム改善**: 今日やることカードに ①②③ の判断見出しリスト + 約3分（ほか40件はAIが預かり中）+ [Inboxを開く]
- **内部ID漏れ修正**: humanizeTitle が epic-xxx を文中どこでも除去し、Factory schedule→AI工場の定期実行 / 失敗Run→失敗した作業 / Review→レビュー 等を翻訳
- **確認系の優先判定**: 完了済み作業はタイトルに障害語があっても「修正作業が完了しました」（誤って「止まっています」と出さない）
- **セット更新**: /guide セクション4を🚨📈✅集計へ・工場状態に今日の判断/AI保留表示 / TERMSに「AI保留」追加 / current-operating-model.md にカード表示ルール・3分類表・変更履歴

## 変更ファイル

- progress/lib/command-center.ts — describeSituation/subjectOf/humanizeTitle強化・InboxView（today/deferredCount/estimatedMinutes）・KIND_CHIP_LABEL・TERMS
- progress/app/decide/page.tsx — 状況見出し+影響行+問いかけレイアウト・上部サマリー
- progress/app/page.tsx — 今日やること①②③リスト統合
- progress/app/guide/page.tsx — 3分類集計・AI保留表示
- progress/docs/operations/current-operating-model.md — 表示ルール・3分類表・変更履歴

## 検証結果

- tsc 0 / lint 0 / build 成功 / 全ルート200（pm2反映済み）
- 禁止語スキャン: 可視テキストに ExecutionRun/runId/Knowledge/reviewed/not_reviewed/candidate/suggested/needs_followup/approval_required/レビュー起点/Knowledge起点/epic-/Run一次レビュー → **0件**
- 実カード3枚すべて状況文+影響行+ボタンを確認（iPhone 390×844）
- Before: ![[../attachments/screenshots/2026-06-11-decide-after-full.png]]（タスク名中心・全件表示）
- After: ![[../attachments/screenshots/2026-06-11-decide-v2-after-full.png]]（状況文+影響行+3件+AI保留）/ 詳細展開 ![[../attachments/screenshots/2026-06-11-decide-v2-after-detail.png]] / ホーム ![[../attachments/screenshots/2026-06-11-home-v2-after.png]]
- git push: 705f43a..37f5e55 成功

## 未対応

- 実機iPhoneでの1周確認（3〜5分で終わるか）はユーザー確認待ち
- ホーム「AI工場の状態」の直近自動作業の内部文字列（前回からの持ち越し）
- AI保留40件の「処理すると次の3件が出る」挙動は実運用で観察

## 危険ポイント

- 状況文変換はルールベース（正規表現）。新しいタイトルパターンで不自然な見出しになる可能性 → フォールバック（「〜で問題が起きています」「〜を改善できます」）で内部語漏れだけは防止
- 影響1行はドメイン語からの推定であり、実際の影響と微妙にずれる可能性（例: 「データの自動更新が止まっています」は対象を特定できないケースの汎用文）
- AI保留は表示分離のみで状態変更なし。明示的な「あとで」（hold）とは別概念な点が将来混乱の種になり得る

## 次にやるべきこと

- iPhone実機で今日の判断を1周し、見出しの自然さ・3分で終わるかを確認
- describeSituation の変換辞書をタイトルパターンの増加に合わせて育てる
- 変換品質が頭打ちになったら、候補生成時にAIが「状況文・影響文」を直接書くフィールド（headline/impact）をデータ側に持たせる設計へ移行検討

## ChatGPT レビュー依頼文

```
progress アプリ「今日の判断」v2（社長向け意思決定アプリ化）のレビューをお願いします。

対象: progress /decide + ホーム
runId: 20260611-203856 / commit: 37f5e55（kaeru07/ny01 main）

実装概要:
- カードタイトルをタスク名→状況文に自動変換（「市場調査ビューのVault更新停止を調査・修正」→「🚨市場調査の自動更新が止まっています」）
- 全カードに「放置するとどうなるか」影響1行を必須表示（ドメイン語からルールベース推定）
- 分類を🚨問題/📈改善/✅確認の3種に変更
- 表示3件制限（優先順: 問題→確認→目標未設定→改善）+残り40件はAI保留として件数のみ
- ホームに①②③の判断リスト+約3分+[Inboxを開く]

確認したい観点:
1. ルールベースの状況文変換 vs データ側にAI生成のheadline/impactを持たせる設計、どちらに寄せるべきか
2. 3件制限の優先順（問題→確認→目標未設定→改善）は妥当か
3. 「AI保留」（表示分離）と「あとで」（hold状態変更）の概念が並存する設計の混乱リスク
4. 影響1行の推定が外れた場合の害（誤った緊急度の演出）への対策
5. 見出し26文字省略の妥当性
```
