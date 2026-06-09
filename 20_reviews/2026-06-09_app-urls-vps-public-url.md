---
date: 2026-06-09
task: URL一覧をiPhone確認用のVPS公開URL形式に修正（Epic epic-url-iphone-vps-url 完了）
runId: 20260609-013044
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260608-235115]
commitHashes: [c50faa9]
# reviewFileCommit:
---

# 2026-06-09 URL一覧をiPhone確認用のVPS公開URL形式に修正（Epic完了）

> /vloop で Epic epic-url-iphone-vps-url を完了まで消化した作業レビュー。

---

## 1. 作業目的

- なぜこの作業をするのか: 直前のapp-urls作業(runId 20260608-235115)はlocalhostを一律「iPhone直接不可」にしただけで、Epic `epic-url-iphone-vps-url` の核心である「VPS稼働アプリを公開IP `http://160.251.143.146:<port>/<path>` 形式で登録してiPhone確認URLにする」が未達だった。
- 背景: progressの「アプリURL」はiPhoneからタップして動作確認するための一覧。VPS上で0.0.0.0にバインドして稼働しているアプリは公開IP経由でiPhoneから到達できるのに、localhostしか登録されておらず確認に使えなかった。
- 期待効果: VPS稼働アプリの公開IP URLを主URL(iPhone確認OK)として出し、localhostは内部URLへ格下げ。Epic完了。

---

## 2. 実施内容

- 実測調査:
  - 公開IP = 160.251.143.146
  - `ss -ltnp` で 0.0.0.0/* LISTEN を確認: 3000(NetScope web /root/map/web)・3002(hack-lab vite=registry外)・3010(progress)・8080/8888(docker-proxy=NetScope分析/訓練)。3030(Scrape Lab)はLISTENなし=未起動。
  - `curl` 公開IP直: 3010/app-urls・3000/(307→/packets)・3000/api/stats・8080/・8888/ すべて200（VPS内から公開IPバインド応答を確認）。
- 実装:
  - `lib/app-urls.ts`: `AppUrlKind` に `vps_internal` 追加（VPS内部=localhost）。PUBLIC_KIND_PRIORITYに反映。公開IP(160.x)は非プライベートのため既存 `classifyRecordAccess` が ok 判定。
  - `components/app-urls/AppUrlsBoard.tsx`: `vps_internal`=「VPS内部URL」ラベル追加、`vps`ラベルを「VPS公開URL」に変更。
  - `data/real/app-urls.json`: progress/NetScopeに公開IP `vps` 主URL(confirmed)を追加、localhostを `vps_internal` 内部URL(iPhone直接不可)へ格下げ。web UIは `/packets`、Stats APIは `/api/stats`、8080/8888も公開IP化。Scrape Labは未起動を明記。operationMemoを公開IP方針へ更新。
  - `data/real/epics.json`: `epic-url-iphone-vps-url` を status=done / progress=100、remainingWork=iPhone実機での外部FW開放確認。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/lib/app-urls.ts | AppUrlKindにvps_internal追加・優先度反映（commit c50faa9） |
| progress/components/app-urls/AppUrlsBoard.tsx | VPS内部URLラベル追加・vps=VPS公開URLにリネーム（commit c50faa9） |
| progress/data/real/app-urls.json | 公開IP URL登録・localhost内部格下げ・memo更新（運用データ・コミット対象外・ライブ反映済） |
| progress/data/real/epics.json | epic-url-iphone-vps-urlをdone/100（運用データ・コミット対象外・ライブ反映済） |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit exit0）
- build: OK（next build Compiled successfully / /app-urls 2.8kB）
- lint: OK（No ESLint warnings or errors）
- 手動確認: 分類再実測12件=iPhone確認OK 2(progress/NetScope公開IP)/直接不可 1(Scrape Lab localhost未起動)/未確認 9。pm2 restart progress(online)後、live /app-urls に公開IP URL 7件・iPhone確認URL・VPS内部URL・iPhone直接不可バッジ表示を確認。
- 機密パターン事前チェック: OK（差分に機密なし）
- ob sync: Fully synced（本レビュー保存時）
- git push: c50faa9（d5a8977..c50faa9 origin main）

---

## 5. 未対応

- iPhone実機での外部到達確認（クラウドFW/SG開放）はVPS内からは検証不能のためEpicのremainingWorkに記録。VPS内からは公開IPバインド応答済み。
- news-app / mahjong 等の未起動アプリは公開URLが無く unknown のまま（推測URLは作らない）。Vercel化/起動+公開は別Epic。
- hack-lab(3002)はcompany apps registry外のため未登録。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。app-urls画面とその台帳データのみ。スキーマは後方互換（vps_internal追加のみ）。
- DB / 認証 / 本番 / 機密: なし。公開IPはグローバルIPだが機密ではない。
- pm2 restart progress を実施（内部ツール・可逆）。本番Vercel/Supabaseデプロイではない。
- ロールバック手段: コードは git revert c50faa9。データはapp-urls.json/epics.jsonの該当エントリを戻す。
- 観察項目: iPhone実機で公開IP URLが実際に開けるか（FW依存）。

---

## 7. 次にやるべきこと

- iPhone実機で http://160.251.143.146:3010/app-urls・:3000/packets・:8080・:8888 を開き、外部FW/SG開放を確認。開かない場合はクラウド側ポート開放 or リバースプロキシ(HTTPS)化を検討。
- 公開URLを増やす: 未起動/未公開アプリの起動+0.0.0.0バインド or Vercel化。
- 関連Epic: 「progressアプリに動作確認Todoページを追加する」(epic, active, progress 0)。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress
作業: URL一覧をiPhone確認用のVPS公開URL形式に修正（Epic epic-url-iphone-vps-url 完了）
runId: 20260609-013044
日付: 2026-06-09
GitHub commit: c50faa9

## 作業目的
- VPS上で0.0.0.0稼働するアプリを公開IP http://160.251.143.146:<port>/<path> 形式で登録しiPhone確認URL化、localhostは内部URLへ格下げ。Epic完了。

## 実施内容
- ss/curl/pm2で公開IPバインドと応答を実測(3010 progress, 3000/8080/8888 NetScope)
- lib/app-urls.tsにvps_internal種別追加(公開IPはok判定/localhostはvps_internalでblocked)
- AppUrlsBoardにVPS内部URLラベル追加・vps=VPS公開URLにリネーム
- data/real/app-urls.jsonに公開IP主URL登録+localhost内部格下げ、epics.jsonでepic-url-iphone-vps-urlをdone/100
- pm2 restart progressでライブ反映確認

## 変更ファイル
- progress/lib/app-urls.ts, progress/components/app-urls/AppUrlsBoard.tsx (commit c50faa9)
- progress/data/real/app-urls.json, epics.json (運用データ・ライブ反映済・コミット対象外)

## 検証結果
- typecheck/build/lint: 全OK
- live /app-urls で公開IP URL・iPhone確認URL/VPS内部URL/iPhone直接不可バッジ表示確認
- 分類: iPhone確認OK 2 / 直接不可 1 / 未確認 9

## 未対応
- iPhone実機での外部FW/SG開放確認(VPS内からは応答済)
- 未起動アプリの公開URL整備は別Epic

## 確認したい観点
- 公開IPを直接URLに載せる方針の妥当性(HTTPS化/リバースプロキシの要否)
- 公開IPバインド確認をconfirmedとし外部FW未確認をremainingWorkに分けた粒度は妥当か
- vps/vps_internalの種別分離設計は妥当か
- data/realをコミット対象外にする運用は妥当か
````

---

## 関連

- progress runId: 20260609-013044（正本）
- 関連 run: 20260608-235115（前段のapp-urls iPhone分類）
- 関連アプリ: [[../02_apps/progress]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
