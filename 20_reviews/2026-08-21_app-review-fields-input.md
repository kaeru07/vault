---
date: 2026-08-21
task: progress の「審査提出準備」タブを、App Store 提出値を人が入力・保存・コピーできる画面に作り替え
runId: 20260821-090235
targetApp: ny01-progress
monetizationImpact: medium
theme: [app-strategy, workflow]
relatedRunIds: [20260821-051731-575, 20260821-120702, 20260822-011730]
commitHashes: [8009a69, cf8b505, 2214107, 072595c, f9a24e2]
reviewFileCommit:
---

## 作業目的

App Store 審査の提出作業で、App Store Connect に入力する値（価格・著作権・カテゴリ・プライバシーポリシー/サポートURL・説明文など）を毎回探し回らずに済むようにする。iOS署名準備タブと同じ位置づけで、**人がその場で入力して保存し、コピペで貼り付けられる**画面を progress に用意する。

前セッションはこの作業の build 検証中にクラッシュし、未コミットの実装だけが残っていた。加えて、当初の実装は fastlane メタデータを読んで表示するだけの**読み取り専用**で、本来の要求（自分で入力できる）と食い違っていた。

## 実施内容

- クラッシュ地点の特定: transcript `aac2f75c` の末尾が `npm run build` 実行中。Codex 生成の5ファイルが未コミットで残存していることを確認
- 要件の修正: 「入力して保存 → コピー」できる画面へ作り替え（読み取り専用から変更）
- 入力項目を固定ホワイトリスト17項目で定義（Name / Subtitle / カテゴリ2種 / 著作権 / 価格 / 配信地域 / 各URL3種 / プロモーション用テキスト / キーワード / 概要 / リリースノート / App Privacy / 年齢レーティング / App Review 備考）
- 値の解決優先度を **保存値 > 自動既定値（fastlane/metadata・apps.json）> 空** に統一。空文字保存で自動既定値へ復帰
- 保存 API（`GET/PUT /api/app-review-fields`）と永続化（`app-review-fields.json`・bundleId 単位）を追加
- クライアント側に 入力欄 / 項目別コピー / 全文コピー（現在の入力値から生成）/ 保存 / 未保存・自動・入力値バッジ / 自動値に戻す を実装
- 年齢レーティング注記を修正: 元実装は全アプリに「麻雀のため…」と出す誤りだったため、麻雀アプリ判定を入れて分岐
- 機密ガード: `ny01` が公開リポジトリで `data/real` も追跡対象のため、審査用デモアカウントID/PW・連絡先電話番号は入力対象から除外し、画面に警告を表示
- progress の運用ドキュメント4点セット（/guide・TERMS・図の要否確認・current-operating-model.md）を更新
- 追補（runId 20260821-120702 / commit cf8b505）: 手動選択が必要だった「手動コピー用全文」を「全文プレビュー」に改称し、**ワンタップの「全文をコピー」ボタン**を追加
- 追補（commit 2214107）: ユーザー指摘「これが順番だから」を受け、入力欄の並びを **App Store Connect のバージョンページ順**に固定（プレビューとスクリーンショット → バージョン情報 → App Reviewに関する情報 → App Storeバージョンのリリース → App情報／価格／プライバシー）。項目追加（バージョン=MARKETING_VERSION初期値 / リリース方法 / スクショ準備メモ）と **ASC文字数上限カウンタ**（超過で赤）
- 追補（runId 20260822-011730 / commit 072595c）: ユーザー指摘「生成じゃなくてスクショ取れないの？撮ったものをダウンロードできるようにしたい」を受け、**実画面のスクリーンショット撮影＋ダウンロード**を実装。playwright-core でアプリを実際に開き iPhone 6.5インチ（1284×2778）/ iPad 13インチ（2064×2752）で撮影。撮影URL未指定時はアプリの `out/` を 127.0.0.1 のランダムポートで一時配信して撮る。一覧は `fastlane/screenshots/` 直下と `ja/` の両方を拾い、PNGヘッダから実サイズを読んで入れられるディスプレイ枠を表示。1枚ずつ ダウンロード / 画像コピー / 削除

## 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `progress/lib/app-review-fields.ts` | 17項目の定義、保存値マージ、`getAppReviewFields`（async化）、`saveAppReviewFields` |
| `progress/lib/app-review-copy.ts` | 新規。サーバー/クライアント共用のコピーテキスト生成（node依存なし） |
| `progress/lib/app-review-fields.test.ts` | 5テスト。`PROGRESS_DATA_PATH` を一時ディレクトリへ向け data/real を汚さない |
| `progress/app/app-review-fields/page.tsx` | server component 化。使い方・機密警告 |
| `progress/app/app-review-fields/AppReviewFieldsClient.tsx` | 新規。入力・保存・コピー UI |
| `progress/app/api/app-review-fields/route.ts` | 新規。GET / PUT（400+日本語エラー） |
| `progress/lib/nav-groups.ts` / `nav-menu.ts` | サブタブ・グループ導線 |
| `progress/lib/command-center.ts` | TERMS に `appReviewFields` |
| `progress/app/guide/page.tsx` | アプリ開発スライドに説明追記 |
| `progress/docs/operations/current-operating-model.md` | 新セクション・画面一覧・用語表・変更履歴・frontmatter |
| `apps/kaeru07.github.io/support/mahjong-analyzer.html` / `apps.json` | 前セッションで作成・push 済（f9a24e2） |

## 検証結果

- `npx tsc --noEmit`: OK
- `npm test`: 191件 全 pass（新規5件含む）
- `npm run build`: exit 0。`/app-review-fields`（3.31 kB）・`/api/app-review-fields` がルート登録
- `pm2 restart progress` 後 HTTP 200。SSR で入力欄68個（input 44 / textarea 24 = 4アプリ×17項目）描画
- API round-trip: 保存 → GET で永続化確認 → 空文字で自動値復帰 → 未知 bundleId は 400
- Playwright headless（390px）: 入力 → 「未保存」バッジ → 保存 → 「保存しました」 → リロード後も保存値保持 → 自動値へ復帰。横スクロールなし、pageerror なし、スクリーンショット目視OK
- 機密スキャン: 新規ファイルに実値ヒットなし
- commit `8009a69` → `git push origin main` 成功
- 追補分: tsc OK / build exit 0 / Playwright headless で「全文をコピー」クリック → ラベルが「コピー済み」に変化 → `navigator.clipboard.readText()` で全文39行を取得できることを確認。commit `cf8b505` push 済
- ASC並び替え分: tsc OK / 193テスト全pass / build exit 0 / ブラウザでグループ順・ラベル順が ASC と一致することを確認、171文字入力でカウンタが赤（rgb(254,226,226)）になることを確認。commit `2214107` push 済
- スクショ分: tsc OK / 196テスト全pass / build exit 0 / 撮影API で実際に 1284×2778 の PNG を生成（`baseUrlUsed` はアプリ out/ の一時サーバー）/ ブラウザでサムネイル7枚表示・ダウンロードリンクの実クリックで 237KB の PNG を取得・横スクロールなし・pageerror なし / DELETE API で削除確認 / `../../../../etc/passwd` は 400 で拒否。commit `072595c` push 済

## 未対応

- 各アプリの `fastlane/metadata` 未整備分（暇潰し！/ 麻雀読みトレーニング / 麻雀実戦読みトレーナー）は自動既定値がほぼ空。画面から手入力して埋める作業は未実施
- モノレポに残る他アプリの未コミット変更（mahjong-analyzer / mahjong-trainer / data/real）は本作業の対象外として触っていない

## 危険ポイント

- **`mahjong-analyzer` が2箇所に存在する**。ストア用の正本は `/root/company/apps/mahjong-analyzer`（独立リポジトリ `kaeru07/mahjong-analyzer`・codemagic.yaml / fastlane / ios あり・審査提出準備タブが参照しているのはこちら）。一方 **AI工場が毎晩作業しているのは `/root/company/apps/ny01/mahjong-analyzer`**（expo あり・codemagic.yaml なし・2026-08-21 02:32 更新）。審査に出す成果物がどちらなのか要確認。取り違えると「直したはずの画面が審査に出ない」事故になる
- スクショ撮影はサーバー側でヘッドレスブラウザを起動する。撮影URLは画面から任意の http(s) を指定できるため、外部URLを入れれば外部サイトも撮れる（単一ユーザー・Basic認証前提の運用として許容）

- **Codex が使用上限（2026-08-27 まで）で実行不可**。本作業の実装も Codex へ委譲できず Claude が直接実装した。朝 5:17 の Factory 失敗（`Codexが終了コード1で終了しました（出力なし）`）も同一原因の可能性が高く、**このままだと自動実行が空振りし続ける**
- `ny01` は public リポジトリで `data/real` も git 追跡対象。この画面の保存値は GitHub に公開される。デモアカウント・電話番号を入力対象から外したのはこのため（画面にも警告あり）
- 保存値が fastlane メタデータより優先されるため、fastlane 側を更新しても画面の保存値が残っていると古い値を貼ってしまう。緩和として「自動値と同じ入力は保存値として持たない」実装＋「自動 / 入力値」バッジを入れている

## 次にやるべきこと

1. Factory の executor を codex から claude へ切り替えるか、8/27 まで自動実行を止めるかを判断する（判断が要るのでユーザー確認事項）
2. 審査提出準備タブで、メタデータ未整備の3アプリの値を入力して保存する
3. 実際の App Store Connect 提出時に、コピーした値がそのまま貼れるか（文字数制限・改行）を1アプリで通しで確認する

## ChatGPT レビュー依頼文

```
対象: progress（/root/company/apps/ny01/progress, Next.js 14 App Router）
runId: 20260821-090235 / commit: 8009a69（kaeru07/ny01, main）

App Store 審査の提出値を人が入力・保存し、項目ごと／全文でコピーできる画面
`/app-review-fields` を追加しました。以下の観点でレビューしてください。

1. 値の解決モデルの妥当性
   - 「保存値 > fastlane/metadata・apps.json の自動既定値 > 空」で、空文字保存＝自動値へ復帰。
   - クライアントは「入力値が自動既定値と一致する項目は空文字で送る」= 保存値として固定しない。
   - fastlane 側を更新したときに古い保存値が残って誤った値を貼るリスクを、これで十分に抑えられているか。

2. 公開リポジトリでの機密の扱い
   - ny01 は public、保存先 data/real も git 追跡対象。
   - 審査用デモアカウントID/PW・連絡先電話番号は入力項目に含めず、画面に警告のみ。
   - 「入力させない」で足りるか、それとも保存前バリデーション（機密パターン検出で拒否）まで要るか。

3. API 設計 (GET/PUT /api/app-review-fields)
   - PUT はアプリ単位の部分更新（未知キー無視・型違い/4000文字超はエラー）。
   - 楽観ロック無しで、同時編集時に後勝ちになる。この規模で許容か。

4. 運用面
   - Codex が 8/27 まで使用上限で、AI工場の executor=codex が失敗し続けている。
     executor を claude に切り替える／自動実行を一時停止する、どちらを勧めますか。
```
