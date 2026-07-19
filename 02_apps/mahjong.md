---
status: active
last_touched: 2026-07-19
monetization_impact: medium
theme: [mahjong, app-strategy, monetization, app-store, google-play, ads]
phase: testflight
sub_apps: [mahjong-trainer, mahjong-quiz, mahjong-analyzer, nanikiru-shorts]
---

# mahjong（麻雀問題アプリ全体）

> 麻雀系アプリ群（trainer / quiz / analyzer）と Shorts チャネル（[[nanikiru-shorts]]）の**統合戦略ページ**。

---

## 直近の更新

- 2026-07-19: `apps/mahjong` の `ios-codemagic-test` に当たり牌読み練習(`/yomi`)を取り込み、Codemagic build 2（buildId `6a5c8a4ecdcd9fa083e88c5e`）が TestFlight へアップロード完了。次は iPhone TestFlight で `/yomi` 入口 → クイズ → 結果 → レビュー画面を実機確認する。詳細: [[../20_reviews/2026-07-19_mahjong-yomi-testflight-build2]]

---

## サブアプリ

| アプリ | 用途 | パス | 状態 |
|---|---|---|---|
| mahjong-trainer | 牌効率・聴牌判定の練習 | `apps/ny01/mahjong-trainer` | 75%（ロン判定 / RIICHI / デプロイ残） |
| mahjong-quiz | クイズ形式 | `apps/ny01/mahjong-quiz` | クイズフロー全確認済み |
| mahjong-analyzer | 手牌解析 | `apps/ny01/mahjong-analyzer` | 未確認 / 棚上げ候補 |
| nanikiru-shorts | 動画量産（Shorts/TikTok） | `apps/nanikiru-shorts` | 開発中（[[nanikiru-shorts]]） |

---

## 全体戦略

- **入口 = Shorts**（nanikiru-shorts）で認知を取り、**本体アプリへ送客**する構造
- アプリ単体での収益より、**ポータル化したときの合算**で評価する
- 個別アプリは「完璧」より「公開できる状態」を優先

---

## JSON 問題集

- 問題は **JSON で持つ前提**（手牌・ツモ・ドラ・正解・解説・派閥コメント）
- nanikiru-shorts の `src/data/sampleQuestion.ts` 形式を基準にする
- Claude / Codex で量産を補助する：1 問あたり数十秒で生成 → 人がチェック
- 問題のメタデータ: 難易度 / カテゴリ（攻め / 守り / 効率 / 役）/ 派閥傾向

---

## UI

- 「断定しない」「議論を促す」UI 言語を**全アプリで統一**
- マスコット（ロンちゃん）の表情パターンを共有
- 配色: 卓上の緑系をベース、強調は黄・赤
- スマホ縦持ち固定（PC 対応は後回し）

---

## App Store / Google Play 戦略

### App Store

- shogi-kakoi-trainer（[[shogi-trainer]]）の Capacitor 化テンプレを流用
- 課金: 初期無料 → 後で機能解放
- 申請枠を早めに取りに行く

### Google Play

- [[../06_research/GooglePlay市場調査]] を踏まえ ASO / アイコン / スクショを設計
- 競合: 「麻雀 何切る」「麻雀 練習」「麻雀 問題」キーワード上位を分析
- 広告モデル（AdMob）と相性を見る

---

## 問題量産

- 量産の手順:
  1. テーマを 1 つ決める（例: 「序盤の安牌候補」）
  2. Claude / Codex に 5 〜 10 問の JSON 雛形を依頼
  3. 人がチェック・派閥コメントを調整
  4. nanikiru-shorts に流して動画化 → 投稿
  5. アプリ側にも同じ JSON を組み込む
- 「動画用」「アプリ用」を共通フォーマットにすることで二重作業を防ぐ

---

## 広告収益

- アプリ: AdMob インタースティシャル（問題 N 問ごと） / リワード（ヒント表示）
- Shorts: YouTube Shorts ファンド / TikTok 収益化
- Web 版（mahjong-trainer Vercel 公開時）: AdSense

---

## 将来案

- **麻雀学習ポータル**: trainer / quiz / analyzer / Shorts を統合した LP
- **会員制コミュニティ**: 上位ユーザーに対するクローズドな解説 / 添削
- **教材販売**: 「何切る 100 問集」「強くなるパターン集」を電子書籍 or note
- **コラボ**: 麻雀プロ・配信者とのタイアップ動画 / 監修

---

## 現状の課題

- mahjong-trainer のロン判定 / RIICHI 完全実装の必要性が判断保留
- Vercel デプロイの是非（公開する / 寝かせる）
- analyzer の役割が不明 → 棚上げ候補
- ポータル統合 LP のスケッチがない

---

## 次アクション

- [ ] mahjong-trainer の Vercel デプロイ判断
- [ ] mahjong-analyzer を [[途中プロジェクト一覧]] に正式登録
- [ ] ポータル LP の 1 枚スケッチ
- [ ] JSON 問題集の共通スキーマを 1 ファイルに集約
- [ ] [[../06_research/GooglePlay市場調査]] の麻雀カテゴリ調査を完了

---

## 関連

- [[nanikiru-shorts]]
- [[途中プロジェクト一覧]]
- [[../05_monetization/収益化ロードマップ]]
- [[../05_monetization/収益化候補一覧]]
- [[../06_research/GooglePlay市場調査]]
