---
type: chatgpt-approval-pack
title: candidate-001 ChatGPT 承認パック（自己完結）
issue: kaeru07/vault#22
scenario: candidate-001
date: 2026-05-19
status: chatgpt_pending（ChatGPT 方向性承認待ち・AIは承認しない）
---

# candidate-001 ChatGPT 承認パック

> Issue #22。**このファイル単体で ChatGPT が方向性承認を判断できる**よう、判断材料を集約。
> [[../05_monetization/ChatGPT承認ゲート標準]] に従い **approved 化・progress 投入はしない**（Claude は材料集約まで）。
> 関連の一次ソース: [[2026-05-19_承認候補選定]] / [[../05_monetization/scenarios/candidate-001_公開ブロッカー]] / [[../05_monetization/scenarios/candidate-001_7日実行プラン]]

## 1. 対象（1行）
既存ほぼ完成の麻雀「何切る」アプリ（apps/mahjong）の公開ブロッカーを解消しストア公開→広告（AdMob/AdSense）で収益化。

## 2. 推奨理由（なぜ candidate-001 を最初に進めるか）
- **収益化インパクト high は3候補で唯一**（002/004 は medium、003 は low）
- 既存資産流用が最大（実装済み＋vercel.json あり）＝公開までの距離が最短
- 最初の1作業が「build 検証」と1分解明確・完了条件が検証可能
- 調査根拠が公式ストア一次（弱くない／spec scoreTotal 28/40 で1位）

## 3. 比較候補との差分

| 候補 | impact | score | candidate-001 との差 |
|---|---|---|---|
| **candidate-001** 麻雀何切るアプリ公開 | **high** | 28 | 基準（最速・唯一の high） |
| candidate-002 麻雀何切るShorts | medium | 27 | 収益化の壁高（90日1000万再生）。001への送客が主価値 |
| candidate-004 麻雀問題系3アプリ統合 | medium | 25 | 統合方針未確定が前提。001の後 |
| candidate-003 Obsidianテンプレ | low | 24 | 調査根拠2・要再調査。優先度外 |

## 4. メリット / デメリット
- **メリット**: 新規開発不要／何切る市場は専業アプリ複数で成立（市場あり）／動画化容易で candidate-002 と相乗／公開ブロッカー解消のみで収益導線に到達
- **デメリット/リスク**: 実 DL・収益は**ストア非公開＝推測**（人間が一次確認すべき）／build 成否・本番デプロイ状態は**未確認**（B1/B2）／競合はレッドオーシャン寄り（差別化軸は要設計）

## 5. 想定収益（推測・確定値ではない）
- モデル: 表示広告（AdMob/AdSense）＋ candidate-002 Shorts からの送客
- 規模: **未確認**。同カテゴリに専業アプリが複数継続＝市場成立は確認できるが、具体金額は非公開のため本パックでは**推測扱い**。承認後 B1/B2 を経て実数値で再評価する前提
- 数値を「確認済み」とは書かない（CLAUDE.local.md 機密/未確認ガード準拠）

## 5-補強. 市場根拠（2026-05-22 追加 / Issue #49 Epic B 追加調査）

iTunes Search JP（無料 API・一次データ）で **「何切る」直接検索**を実施した結果（[[../06_research/2026-05-22_上位5案追加調査]] §3-1）:

- 何切る専業アプリ **10 件以上存在**（ウザク式 354 reviews / 一択 227 / 牌効率 176 ほか）= 市場成立を実データで再確認
- レビュー数は最大 354 で **ニッチ市場・激戦区ではない**（雀魂 807969 のような巨人不在）
- AI 系は「麻雀何切るAI」1 件のみ（14 reviews）= **AI 解説の差別化軸はまだ空いている**
- Education カテゴリにも「みんなの何切る」存在 → 学習用途の需要あり

→ candidate-001 の差別化軸として **「Web 版 + AI 解説」** を追加（[[../05_monetization/scenarios/candidate-001]] §差別化軸の補強）。これにより chatgpt 承認時の判断材料が単純な「公開して広告」から「ニッチ × AI 解説差別化 × Web 版」へ強化される。

## 6. ブロッカー（[[../05_monetization/scenarios/candidate-001_公開ブロッカー]] 要約）
| # | 種別 | 内容 | 優先度 | 状態 |
|---|---|---|---|---|
| B1 | 技術 | `npm run build` 成否未検証 | 高 | 未確認 |
| B2 | 技術 | 本番 Vercel デプロイ状態未確認（vercel.json はあり） | 高 | 未確認 |
| B3 | ストア公開 | README が create-next-app 既定（ASO 文面なし） | 中 | 要整備 |
| B4 | 素材 | public/ デフォルト・アイコン/OGP/スクショ未整備 | 中 | 要整備 |
| B5 | 動画 | 紹介/何切る動画なし（candidate-002 連動） | 低 | 未着手 |

## 7. 最初の30日プラン（[[../05_monetization/scenarios/candidate-001_7日実行プラン]] を月次へ拡張）

| 期間 | フォーカス | 主要 ToDo | 完了条件 |
|---|---|---|---|
| Day1-2 | 公開可否確定 | B1 build検証 → B2 デプロイ状態確認 | build 成否＋本番URL有無が確定 |
| Day3-5 | 公開準備 | B3 ストア/紹介文ドラフト・B4 素材要件整理 | 文面ドラフト＋素材要件 |
| Day6-7 | 公開判断材料 | スクショ取得・公開Go/No-Go レビュー（判断は人間） | Go/No-Go 材料1枚 |
| Day8-14 | 公開実行（人間承認下） | ストア/Web 公開作業・広告枠設置（設定は人間） | 公開済 or 残ブロッカー明確化 |
| Day15-21 | 送客 | candidate-002 Shorts 構成3本→投稿運用（投稿は人間判断） | Shorts ストック＋導線 |
| Day22-30 | 計測・改善 | DL/広告収益の計測開始（[[../05_monetization/収益結果ダッシュボード設計]]）→ 月次レビュー | 実数値が月次収益レビューに記録 |

> Day8 以降の公開・広告設定・投稿は**人間判断/操作**（外部公開・課金・広告は AI 停止条件）。AI は Day1-7 の準備と各 Day のドラフトまで。

## 8. 人間判断欄（ChatGPT 方向性 → 人間が確定）
- 承認コマンド（[[../05_monetization/ChatGPT承認コマンド標準]]）:
  `candidate-001 approve` / `candidate-001 hold: <理由>` / `candidate-001 reject: <理由>`
- ChatGPT 方向性: （未記入）approve / reject / hold（理由: ____）
- 人間 status 確定: （未記入）approved / rejected / hold → [[../05_monetization/decision-log]] に1行＋[[../90_templates/承認判断テンプレート]]
- 確定日 / 判断者: （未記入）

## 承認後の最初の一手（承認されたら）
- B1 `npm run build` 検証を [[../05_monetization/progress追加ToDo案]] ToDo#1 として progress 投入（人間が投入・AI は送らない）

## 関連
- [[../05_monetization/scenarios/candidate-001]] / [[2026-05-19_承認候補選定]] / [[ChatGPT承認待ち]] / [[../05_monetization/ChatGPT承認ゲート標準]] / [[../05_monetization/decision-log]]
- Issue: kaeru07/vault#22（関連 #14 / #15 / #16 / #18 / #19）
