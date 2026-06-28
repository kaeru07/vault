---
date: 2026-06-28
task: 運用ガイド(/guide)を図ベースのスライド型(パワポ風/iPhone前提)に刷新
runId: 20260628-190524
targetApp: progress
monetizationImpact: none
theme: [workflow, obsidian]
relatedRunIds: [20260625-160719, 20260627-104233]
commitHashes: [aa27a65]
---

# 運用ガイド(/guide)を図ベースのスライド型に刷新

## 1. 作業目的
ユーザー指示「運用ページをもっと図でわかりやすく表現してほしい。文字ベースではなく図ベースにしてほしい。もちろんiPhoneで見る前提で。パワポみたいなイメージで。」への対応。**前回この作業中にJSONが破損して中断していた**ため、その再開。再発防止として「データファイルに触れない」ことを最優先制約に置く。

## 2. 実施内容
- 中断状態の調査: progress履歴・全リポジトリの未コミット変更・data/real全JSONの妥当性を確認。**現時点で全JSON妥当**＝前回の破損は復旧済みと判定。
- レイアウト方向をユーザーに確認 →「縦スクロール・スライド型」を選択。
- 実装をCodex(gpt-5.x)へ委譲、設計仕様確定・検証・安全判断・commit/POSTはClaude（標準運用）。
- `/guide` の使い方ガイドタブを、各セクション=1スライド（パワポ1枚相当の大カード+番号バッジ）の縦スクロール構成に再構成。
- 図解: AI工場の流れ=循環図(↻最初に戻る)、今日の流れ=朝/夜フロー図、工場状態=KPIタイル、収益化=ロードマップ、キュー/動作確認の状態=色分け凡例、FAQ=折りたたみアコーディオン。
- **文言・件数・データ・3タブ(report/system/research)・footerは不変、表示のみ刷新**。

## 3. 変更ファイル
- `progress/app/guide/page.tsx` — guideタブをSlide型の図ベース構成に再構成（内容不変）
- `progress/components/guide/SlideKit.tsx`（新規）— Slide/FlowDiagram/LoopDiagram/StatTiles/Roadmap/LegendGrid/IconChip
- `progress/components/guide/FaqAccordion.tsx`（新規・'use client'）— 折りたたみFAQ
- `progress/docs/operations/current-operating-model.md` — frontmatter(updated/updateNote)+変更履歴を更新

## 4. 検証結果
- `npx tsc --noEmit`: pass
- `npm run build`: pass
- 実描画: pm2(next start=本番モード)再起動で新ビルド反映 → `/guide` HTTP200・bytes 117K→157K・LoopDiagram「↻ 最初に戻る」・FAQ `<details>` 35件・全10スライド・ボトルネック強調・エラーマーカー0
- data/real 全JSON妥当（作業前後とも）・data/配下に今回の変更なし
- 機密スキャン: ヒットなし
- commit `aa27a65` → main push済

## 5. 未対応
- iPhone実機での目視確認（スライドの図解崩れ・FAQ開閉・ダークモード）は人間確認待ち。
- 同リポジトリに今回と無関係な既存未コミット変更（data/real runtime・lib・他アプリ）が残置。これは本作業の対象外として意図的に触れていない。

## 6. 危険ポイント
- 前回の破損経緯から、data/real への巻き込みが最大リスク。今回は表示用.tsx/コンポーネントのみに限定し、data/には一切触れていない。
- pm2が`next start`（本番モード）で常駐するため、ビルドしてもrestartしないと旧ビルドを配信し続ける（stale build事故の温床）。今回はrestartで反映を確認済み。次回以降のUI変更でも「build後pm2 restart→実描画確認」を必須に。

## 7. 次にやるべきこと
- iPhoneで `/guide` を開き、各スライドの図が読みやすいか・横はみ出しがないか・FAQアコーディオン・ダークモードを確認。
- 崩れがあればSlideKitの余白/フォントサイズを微調整。

## 8. ChatGPTレビュー依頼文
```
対象アプリ: progress (/root/company/apps/ny01/progress) / runId: 20260628-190524 / commit: aa27a65
GitHub: kaeru07/ny01

運用ガイド画面 /guide を「文字ベース→図ベースの縦スクロール・スライド型(パワポ風/iPhone前提)」に刷新しました。
変更は表示のみで、文言・データ・3タブ・footerは不変です。新設 components/guide/SlideKit.tsx と FaqAccordion.tsx。

以下の観点でレビューしてください:
1. iPhone(375px幅)前提で、図(循環図/フロー/KPIタイル/ロードマップ/色分け凡例)が横はみ出し・潰れなく読めそうか
2. 「パワポ風」として情報量と視認性のバランスは妥当か（1スライド=1メッセージになっているか）
3. 文字情報を図に寄せた結果、欠落・誤解を生む箇所がないか
4. 前回JSON破損の再発防止として「data/realに触れない」方針は十分か、他に注意点はあるか
5. SlideKitの再利用設計（プリミティブ分割）は他ページ展開に耐えるか
```
