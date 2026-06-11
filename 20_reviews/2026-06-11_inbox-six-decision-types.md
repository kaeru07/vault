---
date: 2026-06-11
task: Inboxを「人間が何を判断するか」の6分類へ再設計（検収/実行許可/方針選択/人間作業/危険判断/AI保留）
runId: 20260611-211508
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy]
relatedRunIds: [20260611-203856, 20260611-194652]
commitHashes: [97a9741]
reviewFileCommit:
---

# progress Inbox 6分類再設計レポート

## 作業目的

これまでの改善でも分類が「タスクの種類」軸のままで、社長が「何の話なのか・なぜ自分が判断するのか・はいを押していいのか・AIに任せていいのか」を判断できなかった。分類軸を「人間が何を判断するのか」へ変更し、社長が3分で終わる意思決定アプリにする。

## 実施内容

6分類への再設計（優先順: 危険判断 → 検収 → 方針選択 → 実行許可 → 人間作業、最大3件表示）:

1. **検収** — AIの作業が終わった。結果だけ確認。「〇〇の作業が完了しました」+ AIがやったこと / 人間がやること: 結果を見るだけ。[問題なし][修正する][あとで]
2. **実行許可** — AIが作業したい。「〇〇が起きています」+ 放置すると / AIがやること / 人間がやること: 進めるか選ぶだけ。[進める][あとで][やめる]
3. **方針選択** — AIでは決められない。「〇〇の目的が決まっていません」+ 選ばないと。目標名を直接ボタン化（AI工場OS自走化 / 収益化 / 価値検証…）+ [不要]
4. **人間作業** — AIでは実行できない（ストア公開申請・AdMob・課金・契約・本人確認）。「〇〇はあなたの作業が必要です」+ AIでは実行できません注記。[完了した][あとで][不要]
5. **危険判断** — 本番DB・課金・認証・deploy・external_publish・destructive 系承認。「〇〇を実行しようとしています」+ 影響行。[許可する][許可しない]
6. **AI保留** — 人間に見せない。Factory schedule / routine / health check / metrics / 内容不足（主語4文字未満）/ 重複候補 / 同テーマ大量候補（MVP作成・公開申請・課金設定は各テーマ先頭1件のみ）。カードは出さず「AI保留 55件 AIが整理中です」のみ

その他:
- 「あとで」（検収のneeds_human分）は状態を変えず今日の画面から閉じるだけ（api: null のローカル動作）
- ホーム①②③に分類ラベルチップを追加（ホームも同じ分類で表示）
- セット更新: /guide セクション4を6分類集計に変更+FAQ「AI保留って何？」追加 / TERMS に5分類追加（用語辞典に自動反映）/ current-operating-model.md に6分類表・優先順・表示ルール

## 変更ファイル

- progress/lib/command-center.ts — buildInbox 6分類再構築・AI保留フィルタ・テーマ重複排除・TERMS追加
- progress/lib/inbox-labels.ts — 新規（分類ラベル。クライアント共有用・fs依存なし）
- progress/components/newux/InboxActions.tsx — カード全体クライアント化（説明行・Goalボタン・あとで=閉じる）
- progress/app/decide/page.tsx / app/page.tsx / app/guide/page.tsx — 6分類対応
- progress/docs/operations/current-operating-model.md — 6分類表・変更履歴

## 検証結果

- tsc 0 / lint 0 / build 成功 / 全ルート200（pm2反映済み）
- ビルド失敗を1件検出・修正: クライアントが KIND_CHIP_LABEL を fs 依存の command-center から値 import → lib/inbox-labels.ts へ分離
- 禁止語スキャン: 可視テキストに Factory schedule / Knowledge / ExecutionRun / runId / reviewed / not_reviewed / candidate / suggested / レビュー起点 / Knowledge起点 / おすすめ承認 / epic- / needs_followup → **0件**
- 実測: 今日の判断=検収3件・約3分 / AI保留 55件（suggested 41件の大半がテーマ重複・定期実行系として退避）
- Before: ![[../attachments/screenshots/2026-06-11-decide-v2-after-full.png]]（🚨問題3件・タスク名ベース見出し）
- After: ![[../attachments/screenshots/2026-06-11-decide-v3-after-full.png]]（検収3件 + AIがやったこと/人間がやること行 + AI保留55件 AIが整理中です）
- 詳細展開: ![[../attachments/screenshots/2026-06-11-decide-v3-after-detail.png]] / ホーム: ![[../attachments/screenshots/2026-06-11-home-v3-after.png]]
- git push: 37f5e55..97a9741 成功

## 未対応

- 実機iPhoneでの1周確認（3分で終わるか）はユーザー確認待ち
- 「あとで」のローカル動作は翌日も再表示される（スヌーズ永続化なし）
- 人間作業の「完了した」は rejected+detail で記録（done 相当の候補ステータスが無いため）

## 危険ポイント

- 人間作業判定・AI保留判定はタイトル文字列ヒューリスティック。新パターンで誤分類の可能性（誤分類しても実行許可カードとして出るだけで、内部語漏れはフォールバックで防止）
- 同テーマ大量候補の「先頭1件のみ」は priority ソート順に依存。重要な候補がAI保留に沈む可能性は観察対象
- 「完了した」=rejected の意味のずれはデータ分析時に注意（detail に完了報告と明記済み）

## 次にやるべきこと

- iPhone実機確認（3分1周・分類チップの分かりやすさ）
- 「あとで」スヌーズの永続化（明日まで非表示等）の要否判断
- RecommendationStatus に done 相当を追加するかの設計判断
- 検収カードを処理してAI保留から次の3件が補充される回転の観察

## ChatGPT レビュー依頼文

```
progress アプリ Inbox の6分類再設計（社長向け意思決定アプリ）のレビューをお願いします。

対象: progress /decide「今日の判断」+ ホーム
runId: 20260611-211508 / commit: 97a9741（kaeru07/ny01 main）

実装概要:
- 分類軸を「タスクの種類」→「人間が何を判断するか」へ変更
- 6分類: 検収[問題なし/修正する/あとで] / 実行許可[進める/あとで/やめる] / 方針選択[目標名ボタン/不要] / 人間作業[完了した/あとで/不要] / 危険判断[許可する/許可しない] / AI保留(カード非表示・件数のみ)
- 優先順: 危険判断→検収→方針選択→実行許可→人間作業、最大3件・約3分
- AI保留: 定期実行・内容不足・重複・同テーマ大量候補を自動退避(55件)
- カード本文はラベル付き説明行(AIがやったこと/人間がやること/放置すると/影響/選ばないと)

確認したい観点:
1. 6分類の網羅性と境界（どの判断にも当てはまらないケースは無いか）
2. 優先順（危険判断→検収→方針選択→実行許可→人間作業）の妥当性
3. AI保留の自動退避ヒューリスティック（重要候補が沈むリスク）
4. 「あとで」がローカルで閉じるだけ（翌日再表示）の挙動は社長UXとして適切か
5. 人間作業「完了した」をrejected+detailで記録する暫定設計の是非
```
