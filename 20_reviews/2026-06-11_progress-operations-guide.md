---
date: 2026-06-11
task: 📖運用タブ(/guide)新設 — Progressが自分の使い方を説明する運用ガイド + 運用ドキュメント自動管理開始
runId: 20260611-124611
targetApp: progress
monetizationImpact: medium
theme: [workflow, obsidian]
relatedRunIds: [20260611-120733, 20260611-022341]
commitHashes: [164c736]
reviewFileCommit:
---

# progress 📖運用タブ新設レポート

## 作業目的

Progressは司令塔化され始めたが「どう使うのか」が分かりづらい。機能追加よりも運用理解を優先し、**Progressアプリ自身が自分の使い方を説明できる状態**を作る。初見ユーザーが5分で「このアプリは何か / 今日何をやるか / AI工場がどう動いているか」を理解できることがゴール。最重要方針: Progressは「AI工場の管理画面」ではなく「人間用の司令塔」。内部構造の複雑さを見せない。

## 実施内容

- BottomNav を6タブ化: 司令塔 / Inbox / Projects / Revenue / **📖 運用(/guide)** / Legacy
- /guide に8セクションの運用ガイドを実装:
  1. このアプリとは（AI調査→実装→レビュー候補→あなたの判断、の流れ図）
  2. 今日の流れ（朝・夜のカード形式図解）
  3. AI工場の流れ（目標→大きな作業→AI作業→レビュー→学習→次の作業。詰まり箇所を動的ハイライト）
  4. 今日やること（Inbox集計から動的生成 + 予想時間）
  5. 用語辞典（lib/command-center.ts の TERMS 9語を動的表示）
  6. 現在の工場状態（自動化率/レビュー待ち/承認待ち/収益化状況を初心者向け文章で）
  7. 収益化ロードマップ（MVP→公開→広告→DL100→収益1円、📍いまここ表示）
  8. よくある質問（5問）
- 最終更新フッター: docs/operations/current-operating-model.md の frontmatter（updated/updateNote）から動的表示（画面ハードコード禁止）
- docs/operations/current-operating-model.md 新設（運用モデルの正本ドキュメント）
- **セット更新ルール**を明文化: 今後、機能追加・UI変更・運用変更を行ったら ①運用ページ ②用語TERMS ③フロー図 ④current-operating-model.md（frontmatter+変更履歴）を必ず同時更新。apps/ny01/CLAUDE.local.md にも追記し将来セッションに強制
- Legacy整理は現状維持（旧URL/案件/ログ/旧ホーム/旧Factory/旧Goalは /legacy 配下のまま）

## 変更ファイル

- progress/app/guide/page.tsx — 新規（運用ガイド本体）
- progress/components/navigation/BottomNav.tsx — 6タブ化
- progress/lib/operating-model.ts — 新規（frontmatterリーダー）
- progress/docs/operations/current-operating-model.md — 新規（運用モデル正本）
- apps/ny01/CLAUDE.local.md — セット更新ルール追記（ローカル運用ファイルのため非コミット）

## 検証結果

- tsc --noEmit: 0 errors / next lint: 0 warnings / npm run build: 成功（/guide ルート生成確認）
- pm2 restart 後 /guide HTTP 200
- iPhoneビューポート（390×844 / DPR2）スクリーンショット3枚撮影。ヘッドレス環境にCJKフォントが無く豆腐表示だったため fonts-noto-cjk を導入して再撮影
- スクショ: ![[../attachments/screenshots/2026-06-11-guide-iphone-top.png]] / ![[../attachments/screenshots/2026-06-11-guide-iphone-full.png]] / ![[../attachments/screenshots/2026-06-11-home-iphone-nav.png]]
- 動的生成の実測値: ボトルネック「レビュー待ち51件 ⚠ここがボトルネックです」/ 今日やること「作業結果の確認4件・約4分」/ 最終更新「2026-06-11 運用ガイドページ新設」
- git push origin main 成功（679467f..164c736）

## 未対応

- 絵文字（🌅🌙📖等）はヘッドレスに絵文字フォントが無く豆腐表示。実機iPhoneでは正常表示の見込み（実機確認はユーザー側）
- FAQは初期5問。実際の疑問に応じて追補する運用
- 朝/夜の2カラムカードは幅320px以下の端末では窮屈な可能性（390pxでは問題なし）

## 危険ポイント

- システム変更1件: スクショ検証のため apt-get install fonts-noto-cjk を実行（フォント追加のみ・可逆・アプリ動作に影響なし）
- セット更新ルールは「守られなければ形骸化」する。次の機能追加時に運用ページ更新がセットで行われるか要観察
- 予想時間は1件=1分の概算。判断が重い項目（公開判断等）では実際より短く出る

## 次にやるべきこと

- iPhone実機で /guide の可読性・絵文字表示・6タブの窮屈さを確認
- 次回の機能追加でセット更新ルールが機能するか観察
- レビュー待ち51件が再滞留中 → Inboxの「AIにまとめて確認させる」での解消を検討

## ChatGPT レビュー依頼文

```
progress アプリ（AI工場OS v2）の運用ガイドページのレビューをお願いします。

対象: progress 📖運用タブ(/guide)
runId: 20260611-124611 / commit: 164c736（kaeru07/ny01 main）

実装概要:
- アプリ自身が使い方を説明する運用ガイドを新設（初見5分で理解が目標）
- 8セクション: このアプリとは/今日の流れ(朝夜図解)/AI工場の流れ(ボトルネック動的表示)/今日やること(動的+予想時間)/用語辞典/現在の工場状態/収益化ロードマップ/FAQ
- 最終更新はdocs/operations/current-operating-model.mdのfrontmatterから動的表示
- セット更新ルール: 機能追加/UI変更/運用変更→運用ページ・用語・図・docを必ず同時更新

確認したい観点:
1. 「初見5分で理解」に対し8セクションは多すぎないか。削るならどれか
2. ボトルネック表示のロジック（レビュー待ち10件以上で警告）の閾値妥当性
3. 予想時間=1件1分の概算は誤解を生まないか
4. セット更新ルールの形骸化を防ぐ仕組み（CIチェック等）は必要か
5. 用語辞典をTERMSと連動させた設計（正本一元化）の妥当性
```
