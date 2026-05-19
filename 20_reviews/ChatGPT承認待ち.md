---
type: chatgpt-approval-queue
title: ChatGPT 承認待ち（chatgpt_pending キュー）
issue: kaeru07/vault#19
created: 2026-05-19
updated: 2026-05-19
status: active
---

# ChatGPT 承認待ち（chatgpt_pending キュー）

> [[../05_monetization/ChatGPT承認ゲート標準]] の chatgpt_pending を1か所に集約。
> ChatGPT はここを入口に方向性を判断。**Claude は材料追記まで・承認しない**。テンプレ: [[../90_templates/chatgpt-approval-template]]。

## candidate-001 麻雀何切るアプリ公開（chatgpt_pending）

- 対象: [[../05_monetization/scenarios/candidate-001]]
- 材料作成: Claude（runId 20260519-082352 ほか / commit 2d27afc・53038eb・bf3f397）
- 状態: chatgpt_pending（判断材料完備・ChatGPT 承認待ち）

### 推奨案（Claude の推奨・承認ではない）
- 推奨: **candidate-001 を最初に進める**（high 唯一・既存資産流用で実装速度最大・最初の1作業が1分解明確）

### 比較候補
| 候補 | revenueImpact | scoreTotal | 一言 |
|---|---|---|---|
| candidate-001 麻雀何切るアプリ公開 | high | 28 | 公開ブロッカー解消だけで収益導線（推奨1位） |
| candidate-002 麻雀何切るShorts | medium | 27 | 001 への送客で相乗 |
| candidate-004 麻雀問題系3アプリ統合 | medium | 25 | 統合方針確定が前提 |
| candidate-003 Obsidianテンプレ | low | 24 | 調査根拠2・要再調査 |

### メリット / デメリット
- メリット: 既存 mahjong ほぼ完成・vercel.json あり／何切る市場は専業アプリ複数で成立／動画化容易（candidate-002 連動）
- デメリット/リスク: 実DL・収益は非公開＝**推測**／build 成否・本番デプロイ状態は**未確認**（[[../05_monetization/scenarios/candidate-001_公開ブロッカー]] B1/B2）

### 根拠
- 調査根拠: Google Play 公式ストア一次 + まとめ記事 / n=10 / 2026-05-18（[[../06_research/2026-05-18_収益化定期調査_初回]]）
- 関連: [[../05_monetization/既存資産_収益転用候補]] 優先1位

### 次の一手（承認された場合）
- 最初の1作業: B1 `npm run build` 検証（[[../05_monetization/progress追加ToDo案]] ToDo#1）→ 7日プラン [[../05_monetization/scenarios/candidate-001_7日実行プラン]]

### 人間判断欄（ChatGPT 方向性判断 → 人間が確定）
- 承認コマンド（[[../05_monetization/ChatGPT承認コマンド標準]]）:
  `candidate-001 approve` / `candidate-001 hold: <理由>` / `candidate-001 reject: <理由>`
- ChatGPT 方向性: （未記入）approve / reject / hold（理由: ____）
- 人間 status 確定: （未記入）approved / rejected / hold → [[../05_monetization/decision-log]]
- 確定日 / 判断者: （未記入）

---

> 以降、新しい chatgpt_pending はこの下に [[../90_templates/chatgpt-approval-template]] で追記（最新が上が望ましい）。

## 未対応点 / 仮説
- candidate-002/003/004 は判断材料一部のみ → 揃い次第ここへ追記（現状は candidate-001 のみ chatgpt_pending）
- 判断欄は ChatGPT/人間が記入。Claude は空のまま（承認確認しない）

## 関連
- [[../05_monetization/ChatGPT承認ゲート標準]] / [[../90_templates/chatgpt-approval-template]] / [[2026-05-19_承認候補選定]] / [[../05_monetization/decision-log]]
- Issue: kaeru07/vault#19
