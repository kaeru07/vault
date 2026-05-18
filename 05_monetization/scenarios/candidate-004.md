---
title: 麻雀問題系3アプリ統合で主力1本→広告収益
type: monetization-scenario
status: candidate
revenueImpact: medium
手段: アプリリリース (台帳A1) + 広告収入 (台帳A2)
対象アプリ: ny01 配下 mahjong-quiz / mahjong-trainer / mahjong-analyzer（統合）
市場: 日本
created: 2026-05-18
updated: 2026-05-18
approvedBy:
progressLinkedAt:
issue: kaeru07/vault#13
relatedIssues: [kaeru07/vault#6, kaeru07/vault#5]
---

## 収益化シナリオ（1文）
ny01 配下に分散する麻雀問題系3アプリ（quiz / trainer / analyzer）を主力1本へ統合し、candidate-001（mahjong）と相互送客しながら広告で収益化する。

## 対象アプリまたは資産
- ny01/mahjong-quiz・mahjong-trainer・mahjong-analyzer（[[../既存資産台帳]] 優先順位3位・流用難易度「中」）
- 関連: [[candidate-001]]（mahjong 本体）と相互送客前提

## 想定収益源
- 統合アプリの表示広告（AdMob/AdSense）
- candidate-001 / candidate-002（Shorts）との相互送客による回遊増

## 既存資産流用ポイント
- 3アプリとも実装済み（新規開発不要・統合と取捨選択のみ）
- 麻雀ドメインで candidate-001/002 と素材・導線を共用できる

## 最初の1作業（progress送り用・1つに分解）
- 作業: 3アプリの機能差分を比較し「主力1本＋理由」を1案提示（統合実装はしない・調査整理のみ）
- 完了条件: 3アプリ機能差分表と「主力候補1本＋根拠」が文書化されている

## 完了条件
- 主力1本の選定案と根拠が文書化
- 残2アプリの扱い（統合/廃止/温存）方針案が併記
- 実装・公開はしない（人間承認後）

## 調査根拠（妥当性評価メタ）
- どこを調べたか: `apps/ny01/` 実体（mahjong-quiz/trainer/analyzer 存在確認）+ [[../既存資産台帳]]（Issue #5・一次調査）/ 2026-05-18 / n=3アプリ
- 情報源種別: 内部一次（リポジトリ実体）。**市場収益数値は非公開＝推測**（人間が一次確認）
- 競合根拠: 麻雀何切る系は複数専業アプリが成立（[[../../06_research/2026-05-18_収益化定期調査_初回]] 参照）
- 鮮度: 2026-05-18 / 再現性: 高（同ディレクトリで再現）
- スコア: 収益3 速度3 流用4 広告4 動画3 継続3 競合回避2 根拠3 = 25/40

## 妥当性評価スコア / status
- scoreTotal: 25/40
- status: **candidate**（収益期待medium・統合方針未確定で pending_approval に上げない。**approved にはしない**）

## AI/人間の分担
- AI: 機能差分調査・統合案ドラフト・実装ドラフト
- 人間承認が必要: approved 昇格 / 統合方針確定 / どのアプリを残すか / 公開・広告設定

## ステータス履歴
- 2026-05-18 candidate（AI起票・Issue #13＝#6優先・既存資産から起票）

## 関連
- [[../既存資産_収益転用候補]]（優先順位3位の具体化）/ [[../ranking-rule]] / [[../収益化シナリオ承認フロー]]
- 連動: [[candidate-001]] / [[candidate-002]]
- Issue: kaeru07/vault#13（連動 #6 / #5）
