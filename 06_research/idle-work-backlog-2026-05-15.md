---
date: 2026-05-15
title: 放置で進められる作業バックログ（Claude/Codex 余剰リソース消化用）
researcher: Claude (Opus 4.7)
target: 深夜放置・人間確認なし・失敗しても被害小さい・大量調査向き
constraints: AI API 前提禁止 / 外部 API 前提禁止 / 大規模破壊リファクタ禁止 / 未確認なのに OK 扱い禁止
themes: [workflow, app-strategy, monetization, market-research]
monetizationImpact: high
commitHashes: [7d70dd0]
related:
  - [[../05_monetization/収益化候補一覧]]
  - [[../05_monetization/収益化ロードマップ]]
  - [[local-first-app-ideas-2026-05-14]]
  - [[../02_apps/途中プロジェクト一覧]]
---

# 放置で進められる作業バックログ（2026-05-15）

> Claude / Codex に「進めておいて」と投げて、寝てる間 / 別作業中に勝手に進んでもらえる作業の網羅リスト。
> 既存資産を活用 + 失敗しても被害が小さい / build・lint・typecheck の自動検証で OK 判定できるものを優先する。

---

## 1. 既存アプリ棚卸し

| アプリ | 状態 | 公開 URL | 収益化距離 | 次の一手 | 放置向き |
|---|---|---|---|---|---|
| shogi-kakoi-trainer | 公開済 | shogi-kakoi-trainer.vercel.app | 中 | 他 6 囲いの盤面動的化 / 学習記録機能 | ◎ |
| ny01/news-app | 公開済 | news-app-delta-lake.vercel.app | 近 | SEO / タグ / 広告枠拡張 | ◎ |
| mahjong | 未公開 | - | 近 | Vercel 公開 + 問題追加 | ◎ |
| ny-ai（待ち牌当て） | 動作確認済 | URL 未確認 | 近 | Vercel URL 確認 + 公開 | ◎ |
| note (Scrape Lab) | 動作確認済 | - | 中 | 公開判断 + 自動調査用途化 | ◎ |
| map (NetScope) | 動作確認済 | - | 中 | Vercel デプロイ（DATABASE_URL pooler 切替） | △ |
| nanikiru-shorts | 進行中 | - | 中 | Shorts 投稿継続 + 台本量産 | ○ |
| progress | 稼働中 | localhost:3010 | 遠（自家用） | レビュー集計 / Activity Mining | ◎ |
| company-helper | active | local | 遠（自家用） | 作業ループ管理 | ○ |
| ny-note | unknown | - | 遠 | .env 設定確認 | △ |
| memo | unknown | - | 遠 | backend 起動 + API 疎通 | △ |
| ai-dev-clone | unknown | - | 遠 | tools/sf6-buckler-export 動作確認 | △ |
| copy-repo | unknown | - | 遠 | Supabase 整理 | △ |
| dashboard | unknown | - | 遠 | 用途確認 | × |
| Docker 訓練環境 | 未確認 | - | 遠 | 環境定義の棚卸し | △ |
| Obsidian Vault (sync) | 稼働中 | n/a | 遠（資産） | 各 02_apps 最新化 | ◎ |
| GitHub mirror (obsidian-vault) | 稼働中 | github.com/kaeru07/vault | 遠（資産） | 整合確認 / 機密スキャン | ◎ |

放置向き: ◎ = 深夜単独実行 OK / ○ = 確認多少必要 / △ = 環境構築要 / × = 用途不明

---

## 2. 収益化近い順ランキング

| 順位 | アプリ | 推定収益化までの距離 | 詰まり要因 |
|---|---|---|---|
| 1 | ny01/news-app | 1〜2 週 | 広告枠追加 + SEO 整備 |
| 2 | mahjong | 2〜4 週 | Vercel 公開 + 問題 100 問追加 + AdMob |
| 3 | shogi-kakoi-trainer | 2〜4 週 | 他 6 囲い動的化 + 学習記録 + 課金導線 |
| 4 | ny-ai 待ち牌当て | 2〜4 週 | Vercel 公開 + 問題追加 + 広告 |
| 5 | nanikiru-shorts | 4〜8 週 | Shorts 投稿継続 + チャンネル成長 |
| 6 | 30 案 本命 1 件目（朝散歩 / 庭タイムラプス / 打牌セルフ採点） | 6〜12 週 | MVP 着手 |
| 7 | note (Scrape Lab) | 8〜16 週 | B2B 寄りで収益化動線が不明確 |
| 8 | map (NetScope) | 8〜16 週 | 専門ユーザー特化、課金 UX 設計 |
| 9 | progress / company-helper | 自家用 | 自分の効率化が収益化代替 |

---

## 3. あと少しで公開可能なものリスト

### 即公開候補（残り 1-3 日）

- **mahjong**: 動作確認済 → Vercel デプロイのみ
- **ny-ai**: 動作確認済 → Vercel URL 確認のみ（既にデプロイされている可能性あり）

### 短期公開候補（残り 1-2 週）

- **note (Scrape Lab)**: スクレイピング動作 OK → 公開判断（公開する場合は ToS / robots.txt 遵守の文言追加）
- **map (NetScope)**: DATABASE_URL を pooler 形式に戻せば Vercel デプロイ可能

### 改修後公開候補（残り 2-4 週）

- **shogi-kakoi-trainer**: 他 6 囲いの盤面動的化 + 学習記録機能
- **mahjong**: 問題 30 問 → 100 問追加で公開価値向上

---

## 4. API なし新規アプリ案 30 個（前ターンと別軸）

前ターンの 30 案（[[local-first-app-ideas-2026-05-14]]）は趣味・健康・習慣系。
ここでは「**既存資産横展開・ニッチ深掘り・既存コード再利用**」軸で別 30 案を補充。

| # | アプリ名 | 一言 | 既存資産活用 | 難 | 収 | 継 | 海 |
|---|---|---|---|---|---|---|---|
| 1 | 何切るデイリー | 1 日 1 問配信、mahjong から派生 | mahjong | 1 | 4 | 5 | 4 |
| 2 | 詰将棋 100 日 | shogi-kakoi-trainer ロジック流用 | shogi-kakoi-trainer | 2 | 4 | 5 | 3 |
| 3 | 囲い当てクイズ | shogi-kakoi-trainer データ流用 | shogi-kakoi-trainer | 1 | 3 | 4 | 2 |
| 4 | 待ち牌 30 秒チャレンジ | ny-ai 流用 | ny-ai | 1 | 3 | 5 | 4 |
| 5 | 役満ハンター | mahjong データ拡張 | mahjong | 2 | 3 | 4 | 4 |
| 6 | 副露判断クイズ | mahjong 派生 | mahjong | 2 | 3 | 4 | 4 |
| 7 | ニュース要約日記 | news-app 派生、要約は手動 | ny01/news-app | 2 | 3 | 4 | 2 |
| 8 | ローカル RSS リーダー | スクレイピング不要 | - | 2 | 2 | 4 | 4 |
| 9 | 1 分作業タイマー | UI シンプル、流用しやすい | - | 1 | 2 | 4 | 5 |
| 10 | ポモドーロ × ペット育成 | タイマー + 育成 | - | 3 | 4 | 5 | 5 |
| 11 | リバーシ AI（ローカル思考） | AI なし、αβ 探索 | - | 4 | 3 | 4 | 5 |
| 12 | 将棋手筋カタログ | shogi-kakoi-trainer の隣 | shogi-kakoi-trainer | 2 | 3 | 4 | 3 |
| 13 | 麻雀点数計算練習 | mahjong 派生 | mahjong | 2 | 3 | 4 | 3 |
| 14 | 囲碁ヨセ問題集 | 静的問題集 | - | 3 | 3 | 4 | 3 |
| 15 | 漢字検定 1 日 5 問 | 静的問題集 | - | 1 | 3 | 4 | 2 |
| 16 | 英単語 1 日 10 問 | 静的問題集 | - | 1 | 3 | 4 | 4 |
| 17 | パケットキャプチャ学習帳 | map 派生 | map | 3 | 2 | 3 | 3 |
| 18 | DNS 解析クイズ | map データ流用 | map | 3 | 2 | 3 | 4 |
| 19 | スクレイピング練習場 | note 派生 | note | 3 | 2 | 3 | 4 |
| 20 | コマンド練習帳 | Linux/Git/Docker コマンド静的問題 | - | 1 | 3 | 4 | 5 |
| 21 | Docker コンテナ図鑑 | Docker 訓練環境派生 | Docker 環境 | 2 | 2 | 3 | 5 |
| 22 | Git コミット練習場 | ローカル Git 操作練習 | - | 3 | 2 | 4 | 5 |
| 23 | 正規表現クイズ | 静的問題集 | - | 1 | 2 | 4 | 5 |
| 24 | SQL クイズ | 静的問題集 + SQLite ローカル | - | 2 | 3 | 4 | 5 |
| 25 | キーボード打鍵速度 | ローカル計測 | - | 1 | 2 | 4 | 5 |
| 26 | 1 日 1 単語英作文 | 静的お題 | - | 1 | 3 | 4 | 4 |
| 27 | 1 日 1 ニュース見出し作成 | news-app 流用 | ny01/news-app | 2 | 2 | 3 | 3 |
| 28 | ローカル日記 × タグ | progress 隣接 | progress | 1 | 3 | 5 | 5 |
| 29 | 家計簿（汎用ではない・節約チャレンジ特化） | - | - | 2 | 3 | 4 | 4 |
| 30 | プログラミング書籍読了管理 | - | - | 1 | 2 | 4 | 5 |

### ⚠️ 既存資産流用の強弱（2026-05-15 追補）

新規 30 案の「既存資産活用」列を以下に再整理する。深夜放置で試験運用するなら**「流用が強い」**から選ぶこと。

**流用が強い（深夜放置・並列実行に向く）**:
- #1 何切るデイリー（mahjong データ流用）
- #2 詰将棋 100 日（shogi-kakoi-trainer ロジック流用）
- #3 囲い当てクイズ（shogi-kakoi-trainer データ流用）
- #4 待ち牌 30 秒チャレンジ（ny-ai 流用）
- #5 役満ハンター（mahjong データ拡張）
- #6 副露判断クイズ（mahjong 派生）
- #12 将棋手筋カタログ（shogi-kakoi-trainer 隣接）
- #13 麻雀点数計算練習（mahjong 派生）
- #28 ローカル日記 × タグ（progress 隣接）

**⚠️ 流用範囲が限定的（再実装多い・「派生」と書いたが実体は別物）**:
- #7 ニュース要約日記 — news-app 由来はデータ取得部分のみ。要約 UI / 蓄積 / 通知は 1 から実装
- #17 パケットキャプチャ学習帳 — map のターゲットが狭く、学習帳としての UX は再設計が必要
- #18 DNS 解析クイズ — map データ流用と言いつつ問題生成ロジックは新規
- #19 スクレイピング練習場 — note の機能を流用ではなく再実装
- #21 Docker コンテナ図鑑 — Docker 訓練環境派生というより別物（コンテナ画像・解説の手作業大）
- #27 1 日 1 ニュース見出し作成 — news-app の見出しを使うがアプリ自体は別物

**⚠️ 既存資産流用なし（1 からの実装・工数大・深夜放置には向かない）**:
- #8 ローカル RSS リーダー / #9 1 分作業タイマー / #10 ポモドーロ × ペット育成 / #11 リバーシ AI / #14 囲碁ヨセ問題集 / #15 漢字検定 1 日 5 問 / #16 英単語 1 日 10 問 / #20 コマンド練習帳 / #22 Git コミット練習場 / #23 正規表現クイズ / #24 SQL クイズ / #25 キーボード打鍵速度 / #26 1 日 1 単語英作文 / #29 家計簿節約チャレンジ / #30 プログラミング書籍読了管理

→ **本当の流用優先 TOP 5（修正版）**: #1 何切るデイリー / #2 詰将棋 100 日 / #3 囲い当てクイズ / #4 待ち牌 30 秒チャレンジ / #28 ローカル日記タグ
（旧版で #7 を挙げていたが、流用が薄いため #3 に差し替え）

---

## 5. Shorts 量産案

### 切り抜き系（撮影なし）

- **打牌セルフ採点**: 雀魂実戦動画切り抜き + 字幕（Mリーグ放送切り抜きは権利 NG / 自分の対局のみ）
- **shogi-kakoi-trainer**: 囲い別 1 分解説（matsuya / ano-ana / yagura...）→ 7 囲い × 3 パターン = 21 本
- **mahjong**: 1 問 30 秒の問題出題型（A/B/C/D 選択）→ 10 問 × 2 パターン = 20 本
- **ny-ai**: 待ち牌当て 10 秒チャレンジ → 5 問 × 4 パターン = 20 本
- **囲い当てクイズ**: 5 秒で答えろ系 → 7 囲い × 3 = 21 本

### 制作系（撮影あり）

- **庭タイムラプス**: 1 ヶ月分の定点写真 → 30 秒動画
- **写経カレンダー**: 1 ヶ月分の写経を 1 枚画像化
- **湯巡りログ**: 月間訪問温泉 5 件をマップ + 写真切り抜き

### 解説系（喋りなし可・字幕で量産可）

- **詰将棋 1 日 1 問**: 解説字幕のみで 30 秒
- **何切る**: 1 日 1 問字幕

→ 30 日分のネタが既存資産だけで 80 本以上組める。

---

## 6. Google Play 向き案

| アプリ | Capacitor / TWA 化 | 課金導線 | 公開難度 |
|---|---|---|---|
| mahjong | Capacitor 推奨 | AdMob + 月 300 円広告除去 | 2 |
| shogi-kakoi-trainer | Capacitor 推奨 | AdMob + 月 480 円問題追加 | 2 |
| ny-ai 待ち牌 | Capacitor 推奨 | AdMob + 課金 | 2 |
| 朝散歩カレンダー（新規） | ネイティブ推奨 | AdMob + サブスク | 1 |
| 詰将棋 500 日（新規） | ネイティブ推奨 | AdMob + 問題集買い切り | 2 |
| 一筆書きパズル日めくり | ネイティブ推奨 | AdMob + 広告除去 | 3 |
| 庭タイムラプス（新規） | ネイティブ推奨 | AdMob + 動画書き出し買い切り | 3 |
| 御朱印台帳デジタル | ネイティブ推奨 | AdMob + 写真無制限 | 2 |

→ **Capacitor で既存 Next.js を Android 化**は 1 アプリ 1〜3 日で可能。最短で Play 公開できるのは **mahjong**。

---

## 7. Web 公開向き案

| アプリ | Vercel 即可 | ドメイン | 集客動線 |
|---|---|---|---|
| mahjong | ◎ | mahjong-quiz.vercel.app | nanikiru-shorts から送客 |
| ny-ai 待ち牌 | ◎ | 確認必要 | Shorts 動画から送客 |
| note (Scrape Lab) | ○（ToS 文言整備後） | - | はてブ / Qiita 紹介 |
| 何切るデイリー（新規） | ◎ | - | nanikiru-shorts から送客 |
| 詰将棋 100 日（新規） | ◎ | - | 将棋ウォーズユーザー狙い |
| 囲い当てクイズ（新規） | ◎ | - | shogi-kakoi-trainer から送客 |
| 待ち牌 30 秒チャレンジ（新規） | ◎ | - | ny-ai 流用 |

---

## 8. README 改善案

### 統一フォーマット（全アプリ共通テンプレ）

```markdown
# アプリ名
<1 行説明>

## ✨ 特長
- 箇条書き 3-5 行

## 🚀 デモ
[Vercel リンク or スクショ]

## 🛠️ 技術スタック
- Next.js / React / TypeScript / Tailwind

## 📦 セットアップ
\`\`\`bash
git clone ...
npm install
npm run dev
\`\`\`

## 🧪 テスト・検証
- npm run typecheck
- npm run build
- npm run lint

## 🤝 ライセンス
MIT
```

### 個別 README 改修対象（優先順）

1. **mahjong**: 公開予定 → ヒーロー画像 + Vercel デプロイバッジ
2. **shogi-kakoi-trainer**: 公開済 → スクショ + 各囲い紹介
3. **ny-ai**: スクショ + デモ URL
4. **note**: 技術紹介 + 使用例（ToS 配慮文言）
5. **map**: NetScope 用途・WireGuard 構成図
6. **progress**: 内部ツールだが Activity Mining 概念を解説

---

## 9. スクショ案

### 必要枚数（Google Play / Vercel OGP / SNS）

- Google Play: 縦長 6 枚（1080 × 1920）
- Vercel OGP: 1 枚（1200 × 630）
- README ヒーロー: 1 枚（1280 × 640）
- SNS 投稿: 各 1080 × 1080 / 1080 × 1920

### 撮影プラン（各アプリ）

- **mahjong**: トップ / 問題一覧 / クイズ画面 / 結果 / 牌盤 / 難易度フィルタ
- **shogi-kakoi-trainer**: トップ / 囲い一覧 / 誘導モード / 五角形駒 / 学習記録 / 決定画面
- **ny-ai**: トップ / 問題 / 牌入力 / 結果 / 統計 / 設定
- **mahjong 1 問**: 6 種の難易度別

### 作成手段

- Figma で枠テンプレ + 端末モックフレーム
- Vercel ローカル画面 + macOS スクショ + Figma 合成

---

## 10. SEO / タグ案

### 各アプリ ASO 候補

- **mahjong**: 麻雀, 何切る, 麻雀問題, 雀魂, 天鳳, 麻雀練習, 牌効率
- **shogi-kakoi-trainer**: 将棋, 囲い, 将棋囲い, 棒銀, 矢倉, 美濃囲い, 居飛車穴熊, 振り飛車
- **ny-ai**: 待ち牌, 麻雀, 当て, 練習, 何が当たり
- **news-app**: ニュース, 国内ニュース, 速報, news jp

### Web SEO（Web 公開分）

- title / description / og:image / og:title / structured data (FAQ, HowTo)
- sitemap.xml / robots.txt
- 各問題ページに個別 OGP

### YouTube / Shorts タグ

- 麻雀 / 何切る / 雀魂 / Mリーグ
- 将棋 / 囲い / 藤井聡太 / 矢倉
- shogi / mahjong / japan game

---

## 11. ストア説明文案

### Google Play 用テンプレ

```
【短い説明（80 字）】
<キャッチコピー + 主要機能 + ターゲット>

【長い説明（4000 字）】
# こんなあなたへ
- 箇条書き
# このアプリでできること
- 箇条書き
# 特長
- 箇条書き
# 主な使い方
1. 〜
2. 〜
# 課金について
# プライバシー
# サポート

【リリースノート】
- v1.0.0: 初回リリース
- v1.0.1: 不具合修正
```

### 個別ドラフト（mahjong 例）

- 短い説明: 「麻雀の何切る問題を 1 日 1 問。雀魂・天鳳プレイヤー向け牌効率練習」
- 主要キーワード: 麻雀 / 何切る / 牌効率 / 雀魂 / 天鳳

---

## 12. 市場調査案（人手少なめ・放置寄り）

### Claude 単独でできる調査

- Google Play 上位アプリの公開情報整理（DL レンジ・課金モデル・レビュー特徴）
- App Store の同ジャンル整理
- 海外類似アプリの英語レビュー要約（手入力ベース）
- 既存アプリの過去 Shorts 投稿 KPI 整理

### note (Scrape Lab) を使った調査（パブリックデータ + 倫理範囲）

- 公開ニュースサイトのカテゴリ別記事数推移
- 自社で運用する公開コンテンツの取得確認（自分のもの限定）
- Wikipedia のカテゴリ別記事数

### 禁忌（やらない）

- ログインが必要な領域のスクレイピング
- robots.txt で禁止された領域
- 個人情報を含むデータ
- 同一 IP からの高頻度アクセス（DDoS リスク）

---

## 13. スクレイピング調査案（note / Scrape Lab 活用）

| テーマ | 取得対象 | 取得頻度 | 倫理 |
|---|---|---|---|
| 政府公開オープンデータ整理 | data.go.jp 系 | 月 1 | ◎ |
| Wikipedia カテゴリ別記事数 | ja.wikipedia.org | 月 1 | ◎ |
| 気象庁の天気予報パターン分析 | jma.go.jp | 日 1 | ◎ |
| 国会会議録 API（公式） | 国会会議録 API | 月 1 | ◎ |
| 自分の Vercel デプロイ URL の OGP 取得 | 自社ドメイン | 任意 | ◎ |
| 自分の GitHub リポジトリ README 取得 | github.com/kaeru07/\* | 任意 | ◎ |

→ **「自分の資産を整理する用途」と「公式オープンデータ」に絞る**ことで Scrape Lab を放置運用できる。

---

## 14. Obsidian 追記案

### 既存ページ最新化（放置可）

- `02_apps/mahjong.md`: Vercel デプロイ後の URL 追記 + 公開後 KPI 1 ヶ月分
- `02_apps/shogi-trainer.md`: 他 6 囲い動的化の進捗
- `02_apps/nanikiru-shorts.md`: Shorts 投稿数推移
- `02_apps/progress.md`: ExecutionRun 件数 / monetizationImpact 集計
- `02_apps/途中プロジェクト一覧.md`: 全アプリ最新ステータス

### 新規ページ候補

- `06_research/google-play-aso-mahjong.md`: 麻雀 ASO 上位 20 整理
- `06_research/google-play-aso-shogi.md`: 将棋 ASO 上位 20 整理
- `06_research/shorts-kpi-tracking.md`: Shorts チャンネル KPI
- `06_research/competitor-deep-dive-X.md`: 競合 1 アプリ深掘り
- `05_monetization/admob-vs-subscription.md`: 収益モデル比較
- `05_monetization/play-vs-web.md`: Play / Web 比較

### テンプレ整備

- `90_templates/play-app-launch-template.md`: Play 公開チェックリスト
- `90_templates/shorts-script-template.md`: Shorts 台本テンプレ

---

## 15. progress 登録用 ToDo 案

各 ToDo は progress アプリの「ToDo」として登録できる粒度で書いた。`POST /api/todos` 相当を想定。

### 即着手可（深夜放置 OK）

1. mahjong: Vercel プロジェクト作成 + デプロイ
2. mahjong: 問題 10 問 → 30 問追加（既存形式 q011〜q030）
3. mahjong: README にヒーロー画像 + Vercel ボタン
4. ny-ai: Vercel デプロイ URL 確認 → app-index.md 反映
5. ny-ai: 問題 10 問追加
6. shogi-kakoi-trainer: 残り 6 囲いの guideSteps データ追加
7. shogi-kakoi-trainer: README に各囲い紹介
8. news-app: SEO メタタグ整備（og:image / description）
9. news-app: タグページ追加
10. note (Scrape Lab): ToS / robots.txt 配慮文言を README に追加

### 中規模（半日〜1 日）

11. mahjong: AdMob 設定 + テスト広告表示
12. shogi-kakoi-trainer: 学習記録機能（localStorage で十分）
13. ny-ai: 統計ページ追加
14. news-app: 広告枠 1 箇所追加
15. nanikiru-shorts: 1 日 3 問動画台本 7 日分作成
16. map: DATABASE_URL pooler 形式に切替 → Vercel デプロイ
17. progress: ExecutionRun の月別集計レポート
18. progress: monetizationImpact 別の theme 円グラフ
19. obsidian-vault: 02_apps 全ページ最新化
20. obsidian-vault: 04_reviews に過去 1 ヶ月の重要レビュー手動キュレーション

### 大規模（1〜2 週）

21. 朝散歩カレンダー MVP（1 タップ + 連続日数 + 月カレンダー）
22. 庭タイムラプス MVP（定点撮影 + ガイド + 月動画生成）
23. 打牌セルフ採点 MVP（牌入力 UI + シャンテン判定）
24. mahjong: Capacitor で Android 化 → Play Console 内部テスト
25. shogi-kakoi-trainer: Capacitor で Android 化

### 調査系（並列実行 OK）

26. 麻雀ジャンル ASO 上位 20 調査 → `06_research/google-play-aso-mahjong.md`
27. 将棋ジャンル ASO 上位 20 調査 → `06_research/google-play-aso-shogi.md`
28. パズル系ジャンル ASO 上位 20 調査 → `06_research/google-play-aso-puzzle.md`
29. Shorts 麻雀チャンネル上位 10 調査
30. Shorts 将棋チャンネル上位 10 調査

---

## ランキング

### 今すぐ回す価値が高い作業 TOP 10

| # | 作業 | 理由 | 期間 |
|---|---|---|---|
| 1 | mahjong Vercel デプロイ | 最短で 1 件公開資産が増える、ASO 開始の前提 | 1 日 |
| 2 | ny-ai Vercel URL 確認 | 既にデプロイされている可能性、確認するだけで「公開済」アプリが 1 件増える | 30 分 |
| 3 | mahjong 問題追加（10→30 問） | 公開後の「中身ない」評価対策、放置で書ける | 半日 |
| 4 | news-app SEO 整備 | 既に公開済、SEO だけで広告収益が伸びる可能性 | 半日 |
| 5 | shogi-kakoi-trainer 残り 6 囲い動的化 | 公開済の中身充実、データ追加のみで build 確認可能 | 1 日 |
| 6 | 麻雀 ASO 上位 20 調査 | 公開前の必須ステップ、Claude 単独で完結 | 半日 |
| 7 | Shorts 台本量産（21 本） | nanikiru-shorts チャネル稼働、撮影なしで台本だけ作る | 1 日 |
| 8 | 全アプリ README 統一 | 公開前提条件、テンプレ流し込みで一気に整備可能 | 半日 |
| 9 | スクショ Figma テンプレ作成 | 各アプリ 6 枚 × N アプリの量産前提条件 | 半日 |
| 10 | progress ExecutionRun 月別集計 | Activity Mining の素材整理 | 半日 |

### 深夜放置おすすめ TOP 10

| # | 作業 | なぜ放置に向くか |
|---|---|---|
| 1 | mahjong 問題追加（手書きデータ） | build / typecheck で品質保証可、JSON 追加のみ |
| 2 | shogi-kakoi-trainer guideSteps 6 囲い分追加 | データのみ、build 確認で OK |
| 3 | README 統一テンプレ流し込み | 全アプリ Markdown 修正、被害ゼロ |
| 4 | ASO 上位 20 アプリ調査 | パブリック情報整理、確認不要 |
| 5 | Shorts 台本 21 本量産 | テキスト作成のみ、公開判断は別途 |
| 6 | obsidian-vault 02_apps 全ページ最新化 | Markdown 更新、git で容易にロールバック可 |
| 7 | progress ExecutionRun 集計レポート | 集計クエリ実装 + Markdown 出力 |
| 8 | スクショ Figma テンプレ作成 | デザイン作業、build 不要、失敗無害 |
| 9 | ストア説明文ドラフト（全アプリ分） | テキスト作成、公開前に推敲できる |
| 10 | プライバシーポリシー / 利用規約雛形 | 法務系雛形整備、テンプレ流用で一気に作れる |

### 収益化インパクト TOP 10

| # | 作業 | 期待効果 |
|---|---|---|
| 1 | mahjong Play Console 公開（Capacitor 化） | 月 1-3 万円の広告収益期待 |
| 2 | shogi-kakoi-trainer サブスク導入 | 月 480 円 × 数十人 |
| 3 | news-app 広告枠拡張 + SEO | 月 数千〜万 円の広告収益 |
| 4 | ny-ai Vercel 公開 + 広告 | 月 数千円 |
| 5 | 朝散歩カレンダー MVP | Streak モデルでサブスク継続率高い |
| 6 | 庭タイムラプス MVP | 単価高い動画書き出し買い切り |
| 7 | 打牌セルフ採点 MVP | 既存麻雀資産から送客できる |
| 8 | mahjong 問題 100 問達成 | コンテンツ量で広告収益と継続率が伸びる |
| 9 | Shorts チャンネル本格稼働（21 本投稿） | チャンネル登録 → アプリ送客 |
| 10 | 30 案中の即 MVP 化（1 件） | 中長期で新規収益源 |

### 1 日で終わる軽作業 TOP 20

| # | 作業 | 所要時間 |
|---|---|---|
| 1 | mahjong Vercel デプロイ | 30 分 |
| 2 | ny-ai Vercel URL 確認 + app-index.md 更新 | 15 分 |
| 3 | mahjong 問題 10 問追加（既存形式） | 2 時間 |
| 4 | shogi-kakoi-trainer 1 囲い分の guideSteps 追加 | 2 時間 |
| 5 | README 統一テンプレ流し込み（1 アプリ） | 30 分 |
| 6 | Vercel OGP 画像作成（1 アプリ） | 30 分 |
| 7 | Google Play スクショ 6 枚作成（1 アプリ・Figma） | 2 時間 |
| 8 | ASO キーワード 30 個リスト化（1 ジャンル） | 1 時間 |
| 9 | ストア説明文ドラフト（1 アプリ） | 1 時間 |
| 10 | Shorts 台本 5 本量産 | 2 時間 |
| 11 | プライバシーポリシー雛形作成 | 1 時間 |
| 12 | 利用規約雛形作成 | 1 時間 |
| 13 | dependabot.yml 1 リポジトリ追加 | 15 分 |
| 14 | GitHub Actions build check 1 リポジトリ | 30 分 |
| 15 | issue / PR テンプレ 1 リポジトリ追加 | 30 分 |
| 16 | tsconfig 統一化（1 アプリ） | 30 分 |
| 17 | eslint / prettier 共通設定（1 アプリ） | 30 分 |
| 18 | package.json メタデータ整理（1 アプリ） | 15 分 |
| 19 | obsidian-vault 02_apps 1 ページ最新化 | 30 分 |
| 20 | progress に上記 ToDo 30 件登録 | 30 分 |

---

## 共通の進め方ガイド

### 「人間確認なしで進めやすい」のチェックリスト

- [ ] build / typecheck / lint で pass / fail が機械的に判定できる
- [ ] git で容易にロールバック可能（commit を作ってから着手）
- [ ] 影響範囲が 1 アプリ内に閉じている
- [ ] 失敗しても本番ユーザーに影響しない
- [ ] 機密情報・認証情報を扱わない
- [ ] 30 案中で本命を選ぶときも上記を優先

### 着手前のお決まり 5 項目（CLAUDE.local.md § 原則 1）

1. 何を直すか
2. どこを触るか
3. どこを触らないか
4. 完了条件
5. 検証方法

### 完了マーカー 7 項目（CLAUDE.local.md § 最終応答の冒頭ルール）

- 完了状態 / ExecutionRun POST / 作業レビュー / Obsidian 追記 / ob sync / GitHub 反映 / iPhone 確認ページ

---

---

# 追補（2026-05-15 圧縮版）

## monetizationImpact: high の根拠

本バックログを `monetizationImpact: high` と評価する根拠を明示する。

1. **公開済み資産 3 件（mahjong / news-app / shogi-kakoi-trainer）の収益化動線整理を含む** — 公開済みアプリへの収益化作業は半年以内に売上として顕在化する確率が他より高い
2. **「あと少しで公開可能」（残り 1-3 日）の特定** — mahjong Vercel デプロイ / ny-ai URL 確認は最短で公開資産を 1〜2 件増やせる。広告収益化のベース母数が増える
3. **30 案 + Shorts 80 本+ の元ネタ整理** — 中長期の新規収益源を量産する仕込み
4. **ASO 上位 20 調査 ToDo を含む** — 既存アプリの Play 公開時に直接 CVR を上げる材料
5. **README / スクショ / ストア説明文テンプレ** — 公開時の必須資産を機械化、複数アプリへ横展開可能

逆に言うと、本バックログ単体での売上は 0 円（資料整理のため）。**「資料を元に着手した作業が」収益化に効く** という意味で high。

---

## すぐ実行できる TOP 5（Claude/Codex 投入用・圧縮版）

「今すぐ TOP 10」から「Claude/Codex にそのまま投げて寝られる」5 件だけを抽出。各作業に投入用プロンプト付き。

---

### 1. ny-ai Vercel URL 確認

| 項目 | 値 |
|---|---|
| 優先度 | ★★★★★（最短・最小コスト） |
| 対象アプリ | ny-ai（/root/company/apps/ny-ai/） |
| リスク | ほぼゼロ（読み取り操作のみ） |
| 収益化インパクト | 中（既デプロイなら 1 件の公開資産が即顕在化） |
| 完了条件 | (a) Vercel デプロイ URL が確定し apps/app-index.md に反映、または (b) 未デプロイと判明し pm/active-projects.md にメモ |
| 人間確認タイミング | 完了後 1 回（公開 URL を開いて動作確認） |
| 深夜放置向き | ◎（15 分・コマンドだけで完結） |
| 所要時間 | 15-30 分 |

**Claude/Codex 投入プロンプト**:

```
目的: ny-ai の Vercel デプロイ状況を確認し、app-index.md を最新化する。

対象: /root/company/apps/ny-ai/

やること:
1. /root/company/apps/ny-ai/.vercel/project.json があれば projectId を読む
2. vercel ls（CLI が無ければ vercel.json のリンクを読む）でデプロイ URL を取得
3. URL が取得できたら curl -sI で 200/3xx を確認
4. /root/company/apps/app-index.md の ny-ai 行の状態と「次にやること」を更新
5. /root/company/pm/active-projects.md にも反映

禁止事項:
- 新規 Vercel プロジェクト作成
- production deploy のトリガ
- .env 系ファイルの読み出し（記録のみ）

検証:
1. app-index.md の差分を git diff で確認
2. URL 200 確認のレスポンス記録

最終報告:
- 取得した URL（または「未デプロイと判明」）
- 更新ファイルの diff サマリー
```

---

### 2. mahjong 問題追加（10 問 → 30 問）

| 項目 | 値 |
|---|---|
| 優先度 | ★★★★（深夜放置の本命） |
| 対象アプリ | mahjong（/root/company/apps/mahjong/） |
| リスク | 小（JSON 追加のみ、build 失敗時はロールバック容易） |
| 収益化インパクト | 高（公開時のコンテンツ量で直接効く） |
| 完了条件 | q011〜q030 が data/questions.json に追加、npm run build / typecheck 緑、既存 q001〜q010 表示に影響なし |
| 人間確認タイミング | (a) 着手前にスキーマ確認 1 回（不要なら省略）、(b) 完了後ブラウザで 1 問サンプル表示確認 |
| 深夜放置向き | ◎（build/typecheck で機械判定可、影響は data/ 配下に閉じる） |
| 所要時間 | 2-4 時間 |

**Claude/Codex 投入プロンプト**:

```
目的: mahjong の問題を 10 問から 30 問に増やす。既存形式を踏襲し、build / typecheck で品質保証する。

対象: /root/company/apps/mahjong/data/questions.json（およびスキーマ定義）

やること:
1. 既存 q001〜q010 のスキーマと situation.players.self.hand / dora / round の構造を読む
2. q011〜q030 を同形式で追加（難易度 easy/medium/hard を 7/7/6 で配分、タグも既存タグから選択）
3. 各問題に正解（answer）+ 解説（explanation）+ 選択肢 4 つ
4. npm run typecheck
5. npm run build
6. npm run dev で /questions と /quiz/q011 を 1 件サンプル動作確認（ブラウザ起動できないなら HTTP 200 のみ確認）

禁止事項:
- 既存 q001〜q010 の変更
- スキーマ自体の変更
- AI API 呼び出し（問題は手書き）
- 新規依存追加

検証:
1. npm run typecheck → 0 error
2. npm run build → success
3. data/questions.json の length が 30
4. git diff で q001〜q010 が変更されていないことを確認

最終報告:
- 追加した問題 ID 一覧
- 難易度・タグ分布
- build / typecheck 結果
- 人間が次にやること: ブラウザで q011〜q030 をサンプル動作確認
```

---

### 3. 麻雀 ASO 上位 20 調査

| 項目 | 値 |
|---|---|
| 優先度 | ★★★★（Play 公開前の必須） |
| 対象アプリ | mahjong / ny-ai 公開前の共通調査 |
| リスク | ほぼゼロ（情報整理のみ、コード変更なし） |
| 収益化インパクト | 高（Play 公開時の CVR に直結） |
| 完了条件 | 06_research/google-play-aso-mahjong.md が新規作成され、上位 20 アプリの名前 / DL レンジ / 課金モデル / レビュー特徴 / キーワード傾向が整理されている |
| 人間確認タイミング | 完了後 1 回（出力された ASO キーワード採用判断） |
| 深夜放置向き | ◎（Web 情報整理のみ、コード触らず） |
| 所要時間 | 半日（3-5 時間） |

**Claude/Codex 投入プロンプト**:

```
目的: Google Play の麻雀ジャンル上位 20 アプリを調査し、自社 mahjong 公開時の ASO キーワード選定材料を作る。

対象: /root/company/obsidian-sync-vault/06_research/google-play-aso-mahjong.md（新規作成）

やること:
1. 既存テンプレ /root/company/obsidian-sync-vault/90_templates/market-research-template.md をコピー
2. Google Play の「麻雀」「マージャン」「mahjong」「何切る」「雀魂」検索結果上位 20 件を整理
3. 各アプリの: 名前 / 開発元 / DL レンジ / 評価 / 主要キーワード / 課金モデル / レビュー上位の不満 / レビュー上位の動機
4. キーワード頻出 TOP 20 を抽出
5. 自社 mahjong に転用できる ASO キーワード候補 15 個を選定
6. 結論セクションに「自社 mahjong の差別化切り口」を 3 行で記載

禁止事項:
- ログインが必要な情報の取得
- スクレイピング（手動の Web 検索のみ）
- 新規 GitHub mirror push（このタスクはファイル作成までで止める）

検証:
1. 上位 20 件すべて名前・DL・キーワードが埋まっている
2. 自社 ASO 候補 15 個が選定されている

最終報告:
- 上位 20 件一覧
- 自社 ASO 候補 15 個
- 差別化切り口 3 行
- 人間が次にやること: ASO 候補から実採用するキーワードを選定
```

---

### 4. Shorts 台本量産（21 本）

| 項目 | 値 |
|---|---|
| 優先度 | ★★★（撮影なしで進む・並列性高い） |
| 対象アプリ | nanikiru-shorts（既存チャンネル） |
| リスク | 小（テキストのみ・公開判断は別途） |
| 収益化インパクト | 中（チャンネル本格稼働の前提資産） |
| 完了条件 | shogi-kakoi-trainer 7 囲い × 3 パターン = 21 本の Shorts 台本が 06_research または 02_apps/nanikiru-shorts.md 配下に整理 |
| 人間確認タイミング | (a) 着手前に台本フォーマットの好み確認 1 回、(b) 完成後 5 本サンプル確認 |
| 深夜放置向き | ◎（テキストのみ・撮影なし・即生成可） |
| 所要時間 | 2-4 時間 |

**Claude/Codex 投入プロンプト**:

```
目的: 既存 shogi-kakoi-trainer の囲い 7 種類について、Shorts 台本を各 3 パターン（解説 / クイズ / 5 秒ダイジェスト）= 計 21 本作成する。

対象: /root/company/obsidian-sync-vault/06_research/shorts-scripts-shogi-kakoi-2026-05-15.md（新規作成）

やること:
1. shogi-kakoi-trainer の castles データ 7 種（matsuya / ano-ana / yagura / mino / anaguma / kakuhane / boushi 等）を確認
2. 各囲いについて 3 パターンの台本を書く:
   - パターン A: 解説型（30 秒・基本手順 + 弱点）
   - パターン B: クイズ型（15 秒・盤面提示 → 次の一手は?）
   - パターン C: 5 秒ダイジェスト（タイトル + 結論のみ）
3. 各台本に: タイトル / フック（最初 3 秒）/ 本文 / 締め / ハッシュタグ
4. 撮影不要・字幕 + 画面録画前提の台本にする

禁止事項:
- 既存動画の権利物利用（Mリーグ放送等）
- 本物の対局録画の前提
- 公開判断（台本作成のみ）

検証:
1. 21 本すべて埋まっている
2. 各台本に 4 構成要素（タイトル / フック / 本文 / 締め）が揃っている
3. 字幕 + 画面録画だけで撮れる構成になっている

最終報告:
- 7 囲い × 3 パターン = 21 本のタイトル一覧
- 推奨ハッシュタグ 10 個
- 人間が次にやること: 5 本サンプル選んで撮影テスト
```

---

### 5. 全アプリ README 統一テンプレ流し込み

| 項目 | 値 |
|---|---|
| 優先度 | ★★★（公開前提条件） |
| 対象アプリ | mahjong / ny-ai / note（公開可能性高い 3 件） |
| リスク | 小（README 変更のみ、コード影響ゼロ） |
| 収益化インパクト | 中（公開時の信頼性 + GitHub Star に効く） |
| 完了条件 | mahjong / ny-ai / note の README が統一フォーマット（特長 / デモ / 技術 / セットアップ / 検証 / ライセンス）で整備、各 git commit / push 完了 |
| 人間確認タイミング | (a) 着手前にデモ URL の有無確認、(b) 完了後 GitHub Web で 3 アプリ README を視認 |
| 深夜放置向き | ◎（テキストのみ・各リポジトリで完結） |
| 所要時間 | 1.5-2 時間（30 分 × 3 + commit/push） |

**Claude/Codex 投入プロンプト**:

```
目的: mahjong / ny-ai / note の README を統一フォーマットに整備し、公開時の信頼性を上げる。

対象:
- /root/company/apps/mahjong/README.md
- /root/company/apps/ny-ai/README.md
- /root/company/apps/note/README.md

統一テンプレ:
# アプリ名
<1 行説明>
## ✨ 特長 (3-5 行箇条書き)
## 🚀 デモ (Vercel URL or スクショ)
## 🛠️ 技術スタック
## 📦 セットアップ (git clone / npm install / npm run dev)
## 🧪 テスト・検証 (typecheck / build / lint)
## 🤝 ライセンス (MIT)

やること:
1. 既存 README をバックアップ（git で履歴管理されているので別途バックアップ不要）
2. 上記テンプレに沿って各アプリの README を書き換え
3. 既存内容で残すべき情報（独自セットアップ手順・既知の注意事項）は新フォーマット内に取り込む
4. 各リポジトリで git add README.md && git commit -m "docs: README を統一テンプレへ整備"
5. git push origin main

禁止事項:
- コード変更（README 以外触らない）
- .env / 認証情報の記載
- 動作未確認のデモ URL を載せる（不明な場合は「準備中」と書く）

検証:
1. 3 つのリポジトリで git status が clean
2. GitHub Web で各 README が表示されることを確認
3. 既存の独自情報が消えていないことを git diff で目視

最終報告:
- 3 リポジトリの commit hash
- push 結果
- 人間が次にやること: GitHub Web で 3 README を視認、Vercel デモ URL の追記が必要な箇所を確認
```

---

## 選ばなかった作業（今すぐ TOP 10 から 5 件除外）の理由

| # | 作業 | 除外理由 |
|---|---|---|
| 1 | mahjong Vercel デプロイ | 本番デプロイは env 設定 / 課金・ドメイン判断で人間確認必須。「投げて寝る」型ではない |
| 4 | news-app SEO 整備 | 公開済みのため SEO 変更は GSC / OGP の即時確認が必要。深夜放置で UX 回帰のリスク |
| 5 | shogi-kakoi-trainer 残り 6 囲い動的化 | データ作成 + 表示ロジック調整 + 6 囲い分の検証で 1 日では収まらない可能性大 |
| 9 | スクショ Figma テンプレ作成 | Figma 作業は人手が中心、Claude/Codex で完結しにくい |
| 10 | progress ExecutionRun 月別集計 | 集計 API 追加 = 実装が伴い、進行中アプリへ影響あり。深夜放置の試験運用には早い |

→ 上記 5 件はいずれ実施するが、**「人間がいるタイミング」で着手する**ほうが事故率が低い。

---

## 関連

- [[local-first-app-ideas-2026-05-14]] — ローカル完結 30 案
- [[../05_monetization/収益化候補一覧]]
- [[../05_monetization/収益化ロードマップ]]
- [[../02_apps/途中プロジェクト一覧]]
- [[../02_apps/mahjong]]
- [[../02_apps/shogi-trainer]]
- [[../02_apps/nanikiru-shorts]]
- [[../02_apps/progress]]
- [[../03_prompts/Claude-Code標準運用]]
