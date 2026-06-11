---
date: 2026-06-11
task: Inboxを「今日の判断」（社長向け承認アプリ）に全面リデザイン
runId: 20260611-194652
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260611-124611, 20260611-120733]
commitHashes: [705f43a]
reviewFileCommit:
---

# progress Inbox→「今日の判断」リデザインレポート

## 作業目的

現在のInboxはExecutionRun・Knowledge起点の内部概念が多く、人間が何を判断すればいいのか分かりにくい。ユーザーのゴールは「毎日5〜15分だけ意思決定する」こと。人間は「AI工場OSを運用する人」ではなく「社長」— Inboxを内部管理画面から社長向け承認アプリへ刷新し、「はい・いいえ・あとで」を選ぶだけで3〜5分で終わる状態を作る。

## 実施内容

- カードを画面上3分類に統一（内部分類 approval / needs_human run / orphan epic / suggested candidate はサーバ側で畳み込み）:
  - ① 作業結果の確認: 「『〜』の作業が終わりました。問題ありませんか？」[問題なし][修正する]
  - ② 次の作業: 「『〜』を進めますか？」[はい][あとで][やめる]
  - ③ Goal紐付け: 「この作業はどの目標に紐付けますか？」[目標選択+紐付け][不要]
- 表示禁止の徹底: ExecutionRun / reviewed / not_reviewed / runId / Knowledge / candidate / suggested / needs_followup / 次Epic候補 / Run一次レビュー はカード本文に出さない。内部ID・AIの説明は「詳細を見る」トグル内のみ
- 質問文の人間語化: humanizeTitle（`Run一次レビュー:` `epic-xxx:` `Factory(auto):` `の次Epic候補` `(schedule/systemd)` を除去）+ humanizeOptionLabel（問題なし/修正する/あとで/はい/やめる へ正規化）
- ホーム連携: 司令塔の判断セクションを「今日の判断 残りN件 [Inboxを開く]」カードに置換（内部タイトルの露出を排除、件数と入口だけ）
- セット更新ルール遵守: 運用ページ(/guide)セクション4を新3分類集計に変更・FAQ更新 / TERMSに「Inbox=今日の判断」追加（用語辞典へ自動反映）/ current-operating-model.md にカード3分類対応表+変更履歴+frontmatter更新

## 変更ファイル

- progress/lib/command-center.ts — buildInboxをInboxCardビューモデル化 + TERMS追加 + 判断カウント統一
- progress/components/newux/InboxActions.tsx — 全面書き換え（汎用ボタン+詳細トグル）
- progress/app/decide/page.tsx — 「今日の判断」化
- progress/app/page.tsx — ホーム判断カード置換
- progress/app/guide/page.tsx — 3分類集計・FAQ更新
- progress/docs/operations/current-operating-model.md — 対応表・変更履歴

## 検証結果

- tsc --noEmit: 0 errors / next lint: 0 / npm run build: 成功 / 全ルート200（pm2反映済み）
- 禁止語スキャン: /decide の可視テキストに内部用語12種 → **0件**
- Before/After スクショ（iPhone 390×844）:
  - Before: ![[../attachments/screenshots/2026-06-11-decide-before-full.png]]（「Run一次レビュー: epic-vault-4suq」「reviewed ExecutionRun 20260609-140007 から Knowledge know-… を生成」が本文に露出）
  - After: ![[../attachments/screenshots/2026-06-11-decide-after-full.png]]（3分類チップ+人間語の質問+はい/あとで/やめる）
  - 詳細展開: ![[../attachments/screenshots/2026-06-11-decide-after-detail.png]]（内部ID・元の作業履歴は詳細内のみ）
  - ホーム: ![[../attachments/screenshots/2026-06-11-home-after-decision-card.png]]（今日の判断 残り5件 [Inboxを開く]）
- 動的実測: 残り5件（作業結果の確認2 + 次の作業3）/ /guide「作業結果の確認2件・次の作業を進めるか3件・約5分」
- git push: 164c736..705f43a 成功

## 未対応

- ホーム「AI工場の状態」の直近自動作業表示に `20260610-230524: partial（max_runs_reached）` という内部文字列が残存（今回の表示禁止スコープはカード本文のため対象外だが、社長向け方針的には次に人間語化したい）
- 実機iPhoneでの操作感確認（はい連打で3〜5分で終わるか）はユーザー確認待ち

## 危険ポイント

- 内部分類→3分類の畳み込みはヒューリスティック（承認オプションのラベル文字列で confirm/proceed を判定）。新しい承認カテゴリが増えた場合に誤分類の可能性 → humanizeTitle / humanizeOptionLabel のパターン追随が必要
- 「あとで」の挙動が種別で異なる（候補=hold状態に変更 / 承認=保留オプション / 作業結果確認=ボタンなし=放置）。ユーザーに同じ言葉で違う動きをさせている点は観察対象
- 詳細情報はDOM内に存在（視覚的に閉じているだけ）。機密ではないため問題ないと判断

## 次にやるべきこと

- iPhone実機で「今日の判断」を1周し、3〜5分で終わるか・文言が自然かを確認
- ホームAI工場状態の内部文字列（runId・max_runs_reached）の人間語化
- 「あとで」を選んだ候補（hold）の再浮上ルールの設計（いつ戻ってくるか）

## ChatGPT レビュー依頼文

```
progress アプリの Inbox 全面リデザイン（社長向け承認アプリ化）のレビューをお願いします。

対象: progress /decide「今日の判断」
runId: 20260611-194652 / commit: 705f43a（kaeru07/ny01 main）

実装概要:
- Inboxを「今日の判断」に改名し、カードを3分類に統一（作業結果の確認[問題なし/修正する]・次の作業[はい/あとで/やめる]・Goal紐付け[目標選択/不要]）
- 内部概念(ExecutionRun/runId/reviewed/suggested等)はカード本文表示禁止、「詳細を見る」内のみ
- 質問文はhumanizeTitle/humanizeOptionLabelで内部命名を除去した人間語1文
- ホームは「今日の判断 残りN件 [Inboxを開く]」の件数+入口カードのみ

確認したい観点:
1. 3分類への畳み込みヒューリスティック（承認オプションのラベル文字列判定）の頑健性
2. 「あとで」の挙動が種別ごとに異なる設計（hold/保留/放置）の是非
3. 詳細を見る内に内部IDを残す情報設計は社長向けとして適切か
4. はい連打UXの誤操作リスク（取り消し導線が無い）への対策要否
5. humanizeTitleのパターンメンテが将来の内部命名変更に追随できるか
```
