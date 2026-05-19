---
title: ChatGPT 承認待ちテンプレート
type: template
issue: kaeru07/vault#19
created: 2026-05-19
---

# ChatGPT 承認待ちテンプレート

> ChatGPT で方向性を承認するときの**情報を統一**するテンプレ。[[../05_monetization/ChatGPT承認ゲート標準]] とセット。
> Claude が判断材料を埋める。**Claude は承認しない・推奨まで**。判断は ChatGPT＋人間。

```markdown
## <対象シナリオ/方向性名>（chatgpt_pending）

- 対象: candidate-XXX / 方向性
- 起票/材料作成: Claude（runId / commit）
- 状態: chatgpt_pending（ChatGPT 承認待ち）

### 推奨案（Claude の推奨・承認ではない）
- 推奨: <1件 + 一言理由>

### 比較候補
| 候補 | revenueImpact | scoreTotal | 一言 |
|---|---|---|---|
|  |  |  |  |

### メリット / デメリット
- メリット: <収益化インパクト・既存資産流用・速度 等>
- デメリット/リスク: <ブロッカー・不確実性・推測箇所>

### 根拠
- 調査根拠（情報源種別 / n / 鮮度）: 
- 既存資産・関連: [[..]]

### 次の一手（承認された場合）
- 最初の1作業（progress 投入候補・承認後のみ）:

### 人間判断欄（ChatGPT 方向性判断 → 人間が確定）
- コマンド書式（[[../05_monetization/ChatGPT承認コマンド標準]]）:
  `<scenarioId> approve` / `<scenarioId> hold: <理由>` / `<scenarioId> reject: <理由>`
- ChatGPT 方向性: approve / reject / hold（理由: ____）
- 人間 status 確定: approved / rejected / hold（[[../05_monetization/decision-log]] へ）
- 確定日 / 判断者:
- コマンド原文（受領した1行）: 
```

## 使い方（vloop 連携）

1. vloop が判断材料（比較/推奨/ブロッカー/計画）を作成 → 本テンプレで [[../20_reviews/ChatGPT承認待ち]] に1ブロック追記
2. ChatGPT が GitHub 経由で参照し方向性を判断
3. 人間が `status:` を確定し [[../05_monetization/decision-log]] へ記録
4. approved のみ progress 投入（[[../05_monetization/progress連携基準]]）

## 関連
- [[../05_monetization/ChatGPT承認ゲート標準]] / [[../05_monetization/収益化シナリオ承認フロー]] / [[../05_monetization/decision-log]]
- Issue: kaeru07/vault#19（関連 #18）
