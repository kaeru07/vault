---
date: 2026-06-07
task: 当たり牌読み /yomi の盤面を縦並び廃止→麻雀卓レイアウト(雀魂風)に変更
runId: 20260607-111753
targetApp: mahjong
monetizationImpact: low
theme: [app-strategy, workflow]
relatedRunIds: [20260606-220319, 20260606-232717]
commitHashes: []   # 未commit（本タスクでは commit/push 未指示）
# reviewFileCommit:
---

# 2026-06-07 当たり牌読み /yomi 盤面を麻雀卓レイアウト(雀魂風)へ刷新

## 1. 作業目的

- なぜ: 実際の麻雀卓を見ている感覚で捨て牌読み練習を行えるようにする。
- 背景: 旧YomiBoardViewは4プレイヤーを縦4段にスタックしており、誰の河か・卓全体の位置関係が直感的でなかった。
- 期待効果: 実戦に近い視認性。誰の河か瞬時に分かる。

## 2. 実施内容

- YomiBoardView を縦並びスタックから麻雀卓配置へ全面刷新（対面=上 / 上家=左 / 下家=右 / 自分=下）。
- 牌を席ごとに回転: self=0 / toimen=180 / kamicha=270 / shimocha=90。
- 河は6枚で折返し。上家・下家は縦積み（下家=90度は最新chunkが中央寄りになるよう逆順）。
- リーチ宣言牌は (席回転+90) で横向き表示。
- ツモ切りは半透明＋青点（維持）。副露はプレイヤー脇（ゾーン内）に表示。
- 当たり牌のみ「？」枠表示を維持し、回答後に実牌＋amber ringでreveal。
- 中央パネルに 局/本場/供託/巡目/ドラ、各プレイヤーに 自風/座席/点数/リーチ/和了/放銃 バッジ。
- TileDisplay に非破壊の `placeholder` プロップを追加（？枠が牌のfootprintを回転込みで保つ）。
- quiz/result の解説部分は無変更（YomiBoardView の props IF は不変）。

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| components/YomiBoardView.tsx | 縦並び廃止→麻雀卓レイアウトに全面刷新（席別回転/6枚折返し/リーチ横向き/副露脇/中央パネル） |
| components/TileDisplay.tsx | additive: placeholderプロップ追加（？枠を牌footprint・回転対応で描画） |

## 4. 検証結果

- typecheck: OK (`npx tsc --noEmit` 0 error)
- build: OK (`npm run build` 成功・全9ルート ○Static / API非依存維持)
- 手動確認(iPhone幅390px・headless Chromium):
  - 横スクロール: 0px（卓全体が画面内に収まる。卓box 358×450px）
  - 4プレイヤーが卓配置（対面上/上家左/下家右/自分下）で表示・誰の河か(自風+座席+点数ラベル)が分かる
  - 河は6枚折返し / 上家下家は縦積み
  - 当たり牌のみ「？」枠 → 回答後に実牌へreveal（amber ring）
  - 回答後に 読み筋/解説 表示（解説部分は従来どおり）
- 機密パターン事前チェック: OK
- ob sync: Fully synced（本レビュー保存時）
- git push: n/a（本タスクでは commit/push 未指示のため未実施）

## 5. 未対応

- 実機iPhone(Safari)での卓視認性・牌サイズの最終目視。
- commit / push（本タスクでは未指示。実施する場合は最終報告のコマンド参照）。
- リーチ横向き牌まわりの余白微調整（必要なら）。

## 6. 危険ポイント

- 既存「何切る」BoardViewは無変更。TileDisplay変更は optional prop 追加のみ（既存呼び出し非影響）。
- DB/認証/本番/機密: 非接触。
- ロールバック: YomiBoardView.tsx / TileDisplay.tsx の2ファイルを git checkout で戻すのみ。

## 7. 次にやるべきこと

- 実機Safari目視 → 必要なら牌サイズ/余白の微調整。
- commit/push（指示があれば）→ Vercel反映。
- 問題数拡充（20問以上）。

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: mahjong
作業: 当たり牌読み /yomi の盤面を麻雀卓レイアウト(雀魂風)へ刷新
runId: 20260607-111753（関連: 20260606-220319 実装 / 20260606-232717 仕上げ）
日付: 2026-06-07
GitHub commit: 未commit（ローカルのみ）

## 実施内容
- 4プレイヤーを縦並びから麻雀卓配置(対面上/上家左/下家右/自分下)へ変更。
- 席別に牌を回転、河は6枚折返し、リーチ宣言牌は横向き、ツモ切り半透明、副露はプレイヤー脇。
- 当たり牌のみ「？」維持→回答後reveal。中央に局/巡目/ドラ、各家に自風/点数/リーチ/和了/放銃。
- 解説部分は無変更。TileDisplayにplaceholderプロップを非破壊で追加。

## 検証
- tsc 0err / build 成功（全ルート静的・API非依存）。
- iPhone幅390pxで卓全体が収まる（横スクロール0）、？のみ伏せ→reveal、読み筋表示。

## 確認したい観点
- iPhoneでの卓の視認性（牌が小さすぎないか・誰の河か瞬時に分かるか・見切れないか）
- リーチ宣言牌の横向き表現が自然か
- 既存「何切る」を壊していないか
- さらに実戦寄りにするUI改善案
````

---

## 関連

- progress runId: 20260607-111753（正本）
- 実装: [[2026-06-06_mahjong-atari-yomi-mode]] / 仕上げ: [[2026-06-06_mahjong-yomi-verify-deploy]]
- 関連アプリ: [[../02_apps/mahjong]]
