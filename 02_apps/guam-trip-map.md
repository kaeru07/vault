---
status: active
last_touched: 2026-06-08
theme: [travel, map, pwa, nextjs, leaflet, guam]
phase: deployed-protected
url: https://web-ny1.vercel.app
local_url: http://localhost:3011
repo_path: /root/company/apps/map/web
---

# guam-trip-map（グアム旅行インタラクティブ地図）

> The Tsubaki Tower を中心に、グアム旅行中に iPhone でスポット・食事・移動手段・スケジュールを確認するための地図Webアプリ。

---

## 概要

- パス: `/root/company/apps/map/web`
- ローカルURL: `http://localhost:3011`
- Vercel URL: `https://web-ny1.vercel.app`
- 個別デプロイURL: `https://web-ombfyd9i5-ny1.vercel.app`
- 状態: Vercelデプロイ済み。ただし Vercel Deployment Protection により外部HTTP確認は `401`。
- スタック: Next.js 16 App Router / TypeScript / React 19 / Tailwind CSS v4 / Leaflet / OpenStreetMap / Vercel
- 目的: AI画像地図で起きた「位置関係の崩れ」「番号と場所の不一致」「ツバキタワー位置ズレ」を避けるため、実座標を使う。

---

## 採用した技術

### Next.js App Router

- `app/page.tsx` をトップページにして、Vercelでそのまま表示できる構成。
- `app/layout.tsx` でPWA向けのメタデータ、viewport、manifestを設定。
- Leafletはブラウザ専用ライブラリなので、SSR時に `window is not defined` が出ないように、地図本体を `useEffect` 内の遅延importで読み込む。

重要ファイル:

- `/root/company/apps/map/web/app/page.tsx`
- `/root/company/apps/map/web/app/layout.tsx`
- `/root/company/apps/map/web/components/GuamTripApp.tsx`
- `/root/company/apps/map/web/components/GuamMap.tsx`

### Leaflet + OpenStreetMap

- 地図表示は Leaflet。
- タイルは `https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png`。
- 表示クレジットは `OpenStreetMap contributors`。
- 番号付きピンは Leaflet 標準アイコン画像ではなく `L.divIcon` で生成。
- これにより、番号一覧だけ存在して地図上に番号がない問題を避ける。

### leaflet.markercluster

- Tumon中心部は店舗が密集しており、ピンが重なりやすい。
- `leaflet.markercluster` を使い、ズームアウト時はクラスタ化。
- `spiderfyOnMaxZoom: true` により、最大ズーム付近で重なったピンをスパイダー表示できる。
- `disableClusteringAtZoom: 17` で詳細ズーム時は個別ピンを見やすくする。

### TypeScriptデータ定義

スポット、カテゴリ、交通手段、距離リング、mobi推定エリア、食事候補、スケジュール、チェックリストを `lib/guam-trip-data.ts` に集約。

重要ファイル:

- `/root/company/apps/map/web/lib/guam-trip-data.ts`

型の中心:

- `Spot`
- `SpotCategory`
- `TransportMode`

各スポットに持たせている主な情報:

- 番号
- 表示名
- カテゴリ
- 緯度経度
- 取得元
- 取得元URL
- 確度: `confirmed` / `needs-check`
- 推奨交通手段
- 交通手段別メモ
- 注意書き

### PWA対応

- `public/manifest.webmanifest` を追加。
- `display: standalone`
- `orientation: portrait`
- iPhoneでホーム画面追加したときの最低限の起動情報を設定。

重要ファイル:

- `/root/company/apps/map/web/public/manifest.webmanifest`

---

## 画面構成

### 文言方針

- 旅行中に見る画面では専門用語を使わない。
- `OSM`、`POI`、`Nominatim`、`node`、`way` のような地図データ管理用語は画面に出さない。
- ユーザー向けには「元地図」「場所確認済み」「確認方法」「確認ページ」のような言葉に置き換える。
- 地図の著作権表示として必要な `OpenStreetMap` 表記だけは地図内の小さい表示に残す。

### 地図タブ

- The Tsubaki Tower中心の地図。
- 全18スポットを番号付きピンで表示。
- ピン色はカテゴリ別。
- ピンをタップするとポップアップ表示。
- 右/下の詳細パネルと連動。
- スポット一覧から選ぶと地図も該当地点へ移動。

### 予定タブ

旅行日程を日別カードで表示。

- 6/27(土)
- 6/28(日)
- 6/29(月)
- 6/30(火)

### 準備タブ

出発前チェックリスト。

- グアム税関申告
- Visit Guam EDF説明
- Visit Japan Web

### 座標タブ

採用座標の一覧。

- 緯度経度
- 取得元
- 取得元URL
- `座標確認済み` / `要確認`

---

## ピン色ルール

| カテゴリ | 色 | 対象 |
|---|---|---|
| 観光 | ピンク | 恋人岬、リティディアン |
| ビーチ | 青 | ガンビーチ |
| ショッピング | 緑 | Kマート、マイクロネシアモール、ドンキ、DFS、タモンサンズ |
| 飲食 | オレンジ | IHOP、Island Vintage、Beachin' Shrimp、Meskla Dos、Beachside Grill候補、The Beach、クレープ店候補 |
| アクティビティ | 紫 | パラセール、サンセットクルーズ |
| ホテル | 黒 | The Tsubaki Tower |

---

## 採用座標

| No | スポット | 緯度 | 経度 | 確度 | 主な取得元 |
|---|---|---:|---:|---|---|
| ❶ | 恋人岬 / Two Lovers Point | 13.5353160 | 144.8025120 | confirmed | OSM Nominatim: Two Lovers Leap node 2133106594 |
| ❷ | リティディアンビーチ | 13.6540361 | 144.8625255 | confirmed | OSM Nominatim: Ritidian Beach node 358671702 |
| ❸ | ガンビーチ | 13.5251951 | 144.8039974 | confirmed | OSM Nominatim: Gun Beach way 913340341 |
| ❹ | Kマート | 13.5001249 | 144.7999980 | confirmed | OSM Nominatim: Kmart way 32528271 |
| ❺ | マイクロネシアモール | 13.5204240 | 144.8175227 | confirmed | OSM Nominatim: Micronesia Mall way 32524264 |
| ❻ | ドン・ドン・ドンキ | 13.4934793 | 144.7986517 | needs-check | 公式住所 + OSM Chalan Pasaheru周辺 |
| ❼ | IHOP | 13.4913763 | 144.7820548 | confirmed | OSM Nominatim: IHOP way 556244677 |
| ❽ | Island Vintage Coffee | 13.5162691 | 144.8063845 | needs-check | 住所情報 + OSM Guam Reef Hotel |
| ❾ | Beachin' Shrimp | 13.5144495 | 144.8054805 | confirmed | Visit Guam住所 + OSM The Plaza |
| ❿ | Meskla Dos | 13.5166965 | 144.8076695 | confirmed | OSM Nominatim: Meskla Dos node 13657417332 |
| ⓫ | Beachside Grill候補 / Tasi Grill | 13.5144036 | 144.8043558 | needs-check | Dusit Place公式 + OSM Tasi Grill |
| ⓬ | The Beach Restaurant & Bar | 13.5256500 | 144.8035500 | needs-check | 公式位置説明 + OSM Gun Beach補助 |
| ⓭ | タモンサンズプラザ | 13.5096479 | 144.8055472 | confirmed | OSM Nominatim: Tumon Sands Plaza way 256252073 |
| ⓮ | Tギャラリア by DFS | 13.5134368 | 144.8062149 | confirmed | OSM Nominatim: T Galleria By DFS node 4791707822 |
| ⓯ | クレープ店候補 / Love Crepes Guam | 13.5144495 | 144.8054805 | needs-check | The Plaza内候補 + OSM The Plaza |
| ⓰ | パラセール候補 / Scuba Company Marine Sports | 13.4974900 | 144.7776400 | needs-check | 公式住所 + 外部地図座標 |
| ⓱ | サンセットクルーズ乗り場候補 | 13.4446000 | 144.6538000 | needs-check | BG Tours公式の注意書きにより暫定 |
| ⓲ | The Tsubaki Tower | 13.5214913 | 144.8059738 | confirmed | OSM Nominatim: The Tsubaki Tower node 9718459192 |

---

## 取得元メモ

### OpenStreetMap / Nominatim

多くの座標は OpenStreetMap の Nominatim検索結果を採用。

例:

- The Tsubaki Tower: OSM node `9718459192`
- T Galleria By DFS: OSM node `4791707822`
- Gun Beach: OSM way `913340341`
- Micronesia Mall: OSM way `32524264`

### 公式・観光サイト

OSMに店舗POIがなかったものは、公式住所や観光局ページで場所を補強。

- DON DON DONKI: `https://www.dondondonki.com/gu/`
- Beachin' Shrimp: `https://www.visitguam.com/listing/beachin-shrimp/152/`
- The Beach Restaurant & Bar: `https://www.guambeachbar.com/about-us/`
- SCUBA COMPANY: `https://www.scubaco.com/en/about-us`
- BIG Sunset Cruise: `https://bestguamtours.com/ja/big-sunset/`

---

## mobiエリアの扱い

### 実装内容

- `mobiArea` として半透明緑のポリゴンを表示。
- `outsideMobiArea` として恋人岬方面を赤の点線/半透明エリアで表示。
- 地図ポップアップと詳細パネルに「実際の利用可否は現地アプリで確認」と明記。

### 根拠

- 正式なmobi運行エリア境界データは未取得。
- Tumon/Tamuningのホテル・商業導線をもとにした推定表示。
- 恋人岬はホテル中心部から北側に離れ、スケジュール上も時間指定があるため、タクシー推奨としている。

### 今後の改善

- mobi公式エリアのGeoJSONまたはスクリーンショットを入手できたら、`mobiArea` を差し替える。
- 現地アプリで実際に使える範囲を確認し、要確認表示を減らす。

---

## 距離リング

The Tsubaki Tower座標を中心に同心円を表示。

| リング | 半径 | 色 | 意味 |
|---|---:|---|---|
| 徒歩5分圏 | 400m | 緑 | 暑さ・信号・坂で変動する徒歩目安 |
| mobi約5分圏 | 1400m | 青 | 低速EV/短距離移動の推定 |
| mobi約10分圏 | 2800m | オレンジ | TumonからTamuning北部までの推定 |
| mobi約15分圏 | 4200m | 赤 | Kマート・空港寄りまでの推定 |

---

## 交通手段ロジック

各スポット詳細に以下を表示。

- 徒歩
- mobi
- タクシー
- 赤シャトル
- 送迎（アクティビティ用）

判断基準:

- ツバキ周辺/Tumon中心部: 徒歩優先
- 買い物で荷物が増える場所: mobiまたはタクシー
- 恋人岬/リティディアン: タクシー
- パラセール/クルーズ: 送迎優先

恋人岬の扱い:

- 推奨: タクシー
- mobi: 対象外の可能性あり
- 徒歩: 非推奨
- 理由: ツバキから北側の岬で、旅行スケジュール上も短い候補枠に入っているため。

---

## 食事候補の表示

未確定候補として表示。

| タイミング | 候補 | 理由 |
|---|---|---|
| 土曜夜 | Beachside Grill候補 / Tasi Grill、The Beach Restaurant & Bar | 到着日なのでホテル周辺・ビーチ沿いで移動負荷が低い |
| 日曜昼 | Beachin' Shrimp、Meskla Dos | Tumon中心部でショッピング導線に寄せやすい |
| 火曜朝 | IHOP、Island Vintage Coffee | チェックアウト前の朝食候補。IHOPは車移動、Island Vintageは近場候補 |

---

## スケジュール

アプリ内 `予定` タブに表示。

### 6/27(土)

- 09:50 成田発
- 14:35 グアム着
- ツバキチェックイン
- 夜ご飯未決定
- 候補: Beachside Grill候補 / Tasi Grill、The Beach Restaurant & Bar

### 6/28(日)

- ガンビーチ
- ショッピング: Kマート / マイクロネシアモール / ドンキ
- タモンナイトマーケット

### 6/29(月)

- 09:00 パラセール
- 12:10-13:30 Honolulu Coffee受取
- 13:30-15:00 恋人岬候補
- 15:30 サンセットクルーズ送迎
- 16:45 クルーズ開始
- 19:00 クルーズ終了
- 20:00 ホテル帰着

### 6/30(火)

- 朝: IHOP候補 / Island Vintage候補
- 11:00 チェックアウト
- 13:30-14:00 ホテル出発
- 14:35 空港到着目標
- 16:35 グアム発
- 19:20 成田着

---

## 出発前チェックリスト

アプリ内 `準備` タブに表示。

- グアム税関申告: `https://traveller.guamedf.landing.cards/`
  - 到着72時間前から登録可能。
- Visit Guam EDF説明: `https://www.visitguam.jp/planning/edf-jp/`
- Visit Japan Web: `https://services.digital.go.jp/visit-japan-web`

---

## Vercelデプロイ

### 実行結果

- `vercel deploy --yes`
- Deployment ID: `dpl_H3ZW4MzSrE3stDaUP3yc3tpwStcw`
- Ready state: `READY`
- Alias: `https://web-ny1.vercel.app`
- 個別URL: `https://web-ombfyd9i5-ny1.vercel.app`

### 注意

`curl -I` では以下の状態。

- `https://web-ny1.vercel.app` → `401`
- `https://web-ombfyd9i5-ny1.vercel.app` → `401`

理由:

- Vercel側のDeployment Protection / SSO保護が有効。
- ログイン済みブラウザでは見られる可能性がある。
- 一般公開するにはVercel側でProtectionを解除する必要がある。

---

## 検証

### ローカル

- `npm run build` 成功。
- `http://localhost:3011` で `200 OK`。
- Chromium / Playwrightでモバイル幅 `390x844` を確認。
- 地図コンテナ表示: OK
- ピン表示: OK
- クラスタ表示: OK
- 予定タブ: OK
- 準備タブ: OK
- コンソールエラー: なし

スクリーンショット:

- `/tmp/guam-map-mobile.png`

### Vercel

- Vercel上のビルド成功。
- ただし公開確認は認証保護により `401`。

---

## 実装時に発生した問題と対応

### LeafletのSSR問題

問題:

- Leafletを直接importすると、Next.jsの静的生成時に `window is not defined` が発生。

対応:

- `GuamTripApp` 内の `useEffect` で `import("@/components/GuamMap")`。
- ブラウザ上でのみLeafletを読み込む。

### dynamic importのloadingで止まる問題

問題:

- `next/dynamic` の `ssr: false` で地図が「読み込み中」のままになる状態を確認。

対応:

- client shellを分離し、`useEffect` + stateで地図コンポーネントを明示的に読み込み。

### ピンの重なり

問題:

- Tumon中心部のDFS、The Plaza、Meskla Dos、Tasi Grillなどが近接。

対応:

- `leaflet.markercluster` を採用。
- ズーム時にクラスタ解除/スパイダー表示。

---

## 未確認・要改善

### 未確認

- クレープ店の固有名。
- Beachside Grillという指定が固有名か、Tasi Grill / Beach House Grill等の総称か。
- Donkiの正確な店舗入口座標。
- Island Vintage Coffeeの営業状況と正確な店舗位置。
- The Beach Restaurant & Barの正確な入口座標。
- サンセットクルーズの当日出港場所。
- mobi公式エリア境界。

### 改善案

- Google Maps経路リンクを各スポット詳細に追加。
- 現在地表示。
- PWAのオフラインキャッシュ。
- mobi公式エリアが取れたらGeoJSONで差し替え。
- 予約確定後にパラセール/クルーズ集合地点を確定座標へ更新。
- スポットごとの「Google Mapsで開く」「Apple Mapsで開く」ボタン追加。
- 食事候補に営業時間・予約リンクを追加。
- 日付を2026年の実カレンダーと照合して曜日ズレを検出するチェックを追加。

---

## 変更履歴

- 2026-06-08: 地図タイルを日本語版に変更。グアム側の地図登録名は英語が残るため、番号ピン横にスポット名ラベルを常時表示するようにした。IHOPは `❼A IHOP タモン` と `❼B IHOP GPO方面` の2地点に分割。
- 2026-06-08: 拡大しても近い番号が重ならないよう、近接スポットの番号ピンだけ少しずらして表示する処理を追加。本来の場所とは細い点線でつなぎ、一覧クリック時は吹き出しを自動表示せず番号が隠れないようにした。
- 2026-06-08: 画面上の `OSM`、`取得元`、`座標確認済み` などを、旅行中に分かりやすい `元地図`、`確認ページ`、`場所確認済み` へ変更。場所タブでも専門用語や生URLを見せないようにした。
- 2026-06-08: スポット一覧クリック時にクラスタ内マーカーへ確実に移動するよう `zoomToShowLayer` を追加。モバイルでは `地図 → 番号で移動 → 詳細` の順に変更し、番号と場所の対応を確認しやすくした。
- 2026-06-08: LeafletのCanvas描画でHeadless Chromium確認時に `clearRect` エラーが出たため、`preferCanvas` を外してデフォルトSVG描画へ戻した。

---

## 関連

- [[README]]
- [[途中プロジェクト一覧]]
- `/root/company/apps/map/web`
