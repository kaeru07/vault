---
date: 2026-05-24
task: Hermes Agent × Codex 組み込み検討 材料整備（Issue #67）
runId: 20260524-vloop7-hermes-codex-research
targetApp: company-meta / obsidian-vault
monetizationImpact: medium
theme: [monetization, ai-workflow, market-research, research-automation]
relatedRunIds: [20260524-231358, 20260524-223229]
commitHashes: []
---

# Hermes Agent × Codex 組み込み検討 材料整備

## 1. 作業目的

- vloop5 末 / vloop6 末で「次サイクル: #67 Hermes Agent × Codex 検討着手」と確定
- Issue #67 の完了条件 6 件への回答を 1 ファイルで整理
- 採用判断は人間（API 課金関連）。本サイクルは**検討材料整備のみ**
- 既存運用との接続点（重複範囲 + Hermes が独自に価値を出せる範囲）を明確化

## 2. 実施内容

- `06_research/hermes-agent-codex-組み込み検討.md` 新規作成（10 セクション）
  - §1 想定する 5 段階運用ループ（Mermaid 図 + 状態色分け）
  - §2 既存運用との接続点分析（既存資産マップ + 重複範囲 + Hermes 独自価値）
  - §3 Progress 側機能追加判断（当面不要 / Phase B 検討項目）
  - §4 Vault 側テンプレ・運用ルール（新規不要 / 採用判断後）
  - §5 試用対象 1 つ選定（candidate-001 麻雀何切るアプリ）
  - §6 最小運用フロー 1 本（Mermaid + Hermes 無し迂回ルート）
  - §7 収益化インパクト・実装コスト・API 課金有無比較（採用閾値仮説）
  - §8 結論（完了条件 6 件への回答 + Claude 推奨）
  - §9 未対応点 / 仮説
  - §10 関連
- 案件別ToDo一覧 §5 収益化案セクションに #67 追加（検討材料あり / candidate-001 approved 後に最小運用フロー実行）
- 次に実体化するToDo 優先 1 を「完了 → artifact_exists」化
- 00_START_HERE 「さらに掘る」に Hermes 検討材料リンク追加

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `06_research/hermes-agent-codex-組み込み検討.md` | 新規（10 セクション・Mermaid 2 図・完了条件 6 件への回答）|
| `20_reviews/案件別ToDo一覧.md` | §5 収益化案セクションに #67 追加 |
| `20_reviews/次に実体化するToDo.md` | 優先 1 完了化 |
| `00_START_HERE.md` | Hermes 検討材料リンク追加 |
| `20_reviews/2026-05-24_hermes-codex-integration-research.md` | 本ファイル新規 |
| `20_reviews/_review_queue.md` | 先頭追加 |

## 4. 検証結果

| 観点 | 結果 |
|---|---|
| Issue #67 完了条件 6 件 | ✅ すべて回答（§8 結論で集約）|
| 既存運用との接続点 | ✅ 7 既存資産との関係を明示 + 重複範囲 + 独自価値範囲 |
| 試用対象 1 つ選定 | ✅ candidate-001（理由 5 件 + 採用しない 4 候補の理由）|
| 最小運用フロー | ✅ 6 ステップ + Hermes 無し迂回ルートあり |
| API 課金リスク評価 | ✅ Hermes 料金未確認を明記 + 採用閾値仮説 + 月次予算上限案 |
| 未確認情報の扱い | ✅ Issue #67 の「17歳が電卓アプリで月 300 万」を未確認情報として明記 + Hermes API 仕様未検証を明記 |
| approved 化禁止ルール | ✅ Hermes 出力で candidate を approved 化しないルール明記 |
| 機密スキャン | ✅ 実値なし |
| build / typecheck / lint | n/a（Markdown のみ）|
| ob sync / GitHub push | 次フェーズで実行 |

## 5. 未対応

- Hermes Agent の具体的 API 仕様 / 料金 / 実装能力の確認（ユーザー操作・公式仕様確認後）
- candidate-001 が approved 化されてから §6 最小運用フローを実行
- 1-2 サイクル手動運用後の Hermes 採用 ROI 再評価
- `90_templates/hermes-cycle-template.md` 作成（採用判断後）
- progress に `socialPostUrl` / `socialReactions` フィールド追加検討（Phase B）

## 6. 危険ポイント

- ❌ なし（破壊的変更なし / 既存ファイル削除なし / API キー設定なし / 課金関連操作なし）
- Hermes Agent の実 API 呼び出しは**一切していない**（検討材料整備のみ）
- 未確認情報は**未確認**として明示し、収益化判断には人間 + ChatGPT セカンドオピニオン必須を明記

## 7. 次にやるべきこと

1. ChatGPT が _review_queue.md 先頭の本レビューをレビュー
2. ユーザーが Hermes Agent の公式仕様確認（必要時のみ）
3. candidate-001 が approved 化されたら §6 最小運用フローを実行（次サイクル以降）
4. 1-2 サイクル手動運用後に Hermes 採用 ROI 再評価
5. 次サイクルで #59 Vault 全体棚卸し Phase 計画
6. 次サイクルで候補-006/007 の判断するための資料一式（補助 4×2=8 ファイル）

## 8. ChatGPT レビュー依頼文

```text
以下は Claude Code の vloop 連続実行報告です（7 サイクル目）。レビューしてください。

対象アプリ: company-meta / obsidian-vault
作業: vloop7 Hermes Agent × Codex 組み込み検討 材料整備（Issue #67）

## できるようになったこと
- Issue #67 完了条件 6 件への回答を 06_research/hermes-agent-codex-組み込み検討.md に集約
- 5 段階運用ループ + 既存資産接続点 + 試用対象選定 + 最小運用フロー + 採用閾値仮説
- 既存 research-run（HN/Reddit/iTunes）で 7 割カバー / Hermes 独自価値は SNS 反応分析・レビュー不満抽出に限定
- candidate-001 を試用対象に選定（理由 5 件）
- Hermes 無しでも 1 サイクル回せる迂回ルートを明示

## 確認したい観点
1. Hermes 独自価値範囲（SNS 反応分析 + レビュー不満抽出）の判断は妥当か
2. 試用対象 candidate-001 の選定は妥当か（他 4 候補の不採用理由も含む）
3. 最小運用フロー（§6 6 ステップ）と Hermes 無し迂回ルートの両立は妥当か
4. Hermes 採用閾値（週 5 本投稿 / 月 8 時間以上の手動分析）は妥当か
5. 「candidate-001 approved 後に判断」推奨は妥当か（早すぎる採用を避ける判断）
6. progress に新フィールド（socialPostUrl / socialReactions）を Phase B 検討にする判断は妥当か
7. Hermes 出力で candidate を approved 化しないルール明記は妥当か

参考リンク:
- 06_research/hermes-agent-codex-組み込み検討.md（10 セクション・本サイクルの主成果物）
- 20_reviews/案件別ToDo一覧.md §5 収益化案
- 00_START_HERE.md（Hermes 検討材料リンク追加）
```

## 関連

- 前サイクル: [[2026-05-24_candidate-006-007-and-project-todos]]（vloop6）
- [[../06_research/hermes-agent-codex-組み込み検討]]
- [[../03_prompts/Codexレビュー常用運用]]
- [[../03_prompts/AgentSDKクレジット活用方針]]
- [[../05_monetization/scenarios/candidate-001]]
- [[案件別ToDo一覧]]
- [[次に実体化するToDo]]
- Issue: kaeru07/vault#67
