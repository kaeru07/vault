---
title: progress連携基準
type: monetization-design
issue: kaeru07/vault#2
created: 2026-05-18
status: active
---

# progress連携基準

> **Vault = 調査・候補・承認の正本 / progress = 承認済み作業の実行管理。**
> approved でないものは progress へ送らない。

## progress へ送ってよい条件（全て満たすこと）

- [ ] `status: approved`（[[収益化シナリオ承認フロー]] で人間が承認済み）
- [ ] 収益化シナリオが明確（何で収益を得るか1文で言える）
- [ ] 対象アプリ/手段が明確（[[収益化手段台帳]] の行に紐付く）
- [ ] **最初の作業が1つに分解されている**（progressの1 task = 1作業）
- [ ] 完了条件がある（検証可能な形）
- [ ] 調査根拠が残っている（[[調査手段の妥当性評価]] のメタ + n件 + 情報源種別）

1つでも欠ける場合は送らず、`pending_approval` / `hold` に戻す。

## 送り方

1. 承認済みシナリオから「最初の1作業」を抽出
2. progress の queue/ToDo に1件として登録（収益化インパクトを明記）
3. シナリオ側に `progressLinkedAt` / 対象runId を追記（双方向トレース）
4. 実行は既存の集中作業モード/Claude Code運用に従う
5. 結果（ExecutionRun / 20_reviews）を Vault シナリオへフィードバック

## 送ってはいけない例
- スコアは高いが `pending_approval` のまま（承認なし）
- 「アプリを収益化する」など作業が1つに割れていない
- 調査根拠が「推測」「件数不明」
- 公開/課金/外部公開を伴うのに人間承認が無い

## 正本の責務分離（再掲）
- Vault: なぜ/根拠/承認状態（判断・長期記憶）
- progress: 承認済み作業の実行・可視化（事実・実行履歴）
- 矛盾時は **承認状態は Vault、実行履歴は progress** を正とする

## 関連
- [[収益化自動化_全体像]] / [[収益化シナリオ承認フロー]] / [[調査手段の妥当性評価]]
- Issue: kaeru07/vault#2
