---
type: chatgpt-approval-pack
title: candidate-006 ChatGPT 承認パック（自己完結）
issue: kaeru07/vault#61
scenario: candidate-006
date: 2026-05-25
status: chatgpt_pending（ChatGPT 方向性承認待ち・AI は承認しない）
---

# candidate-006 ChatGPT 承認パック

> Issue #61 / #80。**このファイル単体で ChatGPT が方向性承認を判断できる**よう、判断材料を集約。
> [[../05_monetization/ChatGPT承認ゲート標準]] に従い **approved 化・progress 投入はしない**（Claude は材料集約まで）。
> 関連の一次ソース: [[../05_monetization/scenarios/candidate-006]] / [[../05_monetization/scenarios/candidate-006_公開ブロッカー]] / [[../05_monetization/scenarios/candidate-006_7日実行プラン]] / [[../05_monetization/scenarios/candidate-006_progress投入設計]] / [[../90_prototypes/llm-chooser/README]]

> [!note] 用語注（Issue #69）
> - candidate / 有力候補 = 収益化する価値があるかもしれない案
> - chatgpt_pending = ChatGPT 方向性レビュー待ちの状態
> - approved / 承認済み = ChatGPT + あなたの判断で「実装着手 OK」になった状態
> - LLM Chooser = 質問 4 種に答えて Claude / Codex / Gemini / ローカル LLM を提案するクイズ形式の静的 HTML
> - 判定基準客観化 = 「どの LLM が合うか」の判定根拠を観察可能な指標で裏付けすること（N-03 既知制約）
> - revenueImpact = 収益インパクト（high / medium / low / none の 4 段階）
> - AdSense = Google の Web 広告 SDK
> - Shorts 送客 = 短尺動画からアプリ / サイトへ誘導すること
> - progress 投入 = progress（作業履歴アプリ・localhost:3010）への ToDo 追加
> - Vercel = 静的サイトを無料層で公開できる代表サービス

## 1. 対象（1 行）

質問 4 種に答えると **Claude / Codex / Gemini / ローカル LLM のどれが合うか**を提案する静的 HTML 1 枚物のチャート（90_prototypes/llm-chooser / 外部 CDN ゼロ / MVP モック動作確認済）。SNS 拡散 + AdSense + テンプレ販売で収益化。

## 2. 推奨理由（なぜ candidate-006 を進めるか）

- **MVP がすでに動く**（90_prototypes/llm-chooser/index.html / クイズ 4 種 + 全 LLM カード + 機能比較表 9×4）
- **API なしで完全成立**（外部 LLM 呼び出しゼロ / file:// で動く / CDN ゼロ）
- **競合確認できず**（LLM 使い分けの判定フローを 1 ページ HTML 化した競合は未発見 / 低信頼度だがニッチ空白の傍証）
- candidate-005（token-speed-tool）との**相互送客可能**（同じ AI 開発者層がメイン市場）
- 静的構成のため **Vercel 無料層で即時公開可能**

## 3. 比較候補との差分

| 候補 | impact | scoreTotal | candidate-006 との差 |
|---|---|---|---|
| candidate-001 麻雀何切るアプリ公開 | high | 28 | 既存資産 + ASO 整備で公開。本命・収益化直接性が最も高い |
| candidate-005 token-speed-tool | medium | 28 | MVP 動作確認済・静的構成・公開ブロッカー最小・候補-006 と相互送客可 |
| **candidate-006 llm-chooser** | **low** | **26** | **MVP 動作確認済・静的構成・判定基準客観化が制約（N-03）** |
| candidate-007 vault-search-cheatsheet | low | 28 | 既存「Vault の見方ガイド」と統合方針が制約・別市場 |

> candidate-006 は scoreTotal 26（candidate-005/007 より低い）。impact は low。**判定基準客観化（B2 / N-03 既知制約）が承認時の主要論点**。

## 4. メリット / デメリット

### メリット

- **MVP がすでに動く**（試作不要・公開ブロッカーが少ない）
- **静的構成で公開が早い**（Vercel `vercel --prod` 1 コマンド）
- **API なし**で外部依存ゼロ（運用コスト・課金リスクなし）
- candidate-005 との**相互送客**で AI 開発者層を厚くカバー可能
- **Shorts と note の両方に展開可能**（「あなたに合う LLM は？」1 分動画 / LLM 使い分けチートシート Markdown）
- **既存資産 nanikiru-shorts** の動画パイプラインを流用可能

### デメリット / リスク

- **判定基準が個人観察ベース**（N-03 既知制約・客観化方針が公開前提）
- **市場規模は推測**（AI 利用者層は広いが、LLM 使い分けクイズへの興味は限定的の可能性）
- **競合確認が低信頼度**（網羅的な競合調査はしていない）
- **広告 CPC 推測**（AI 開発者層は CPC 高めだが、絶対 PV は小の特性想定）
- **テンプレ販売の単価は低い**（数百円〜千円台想定）

## 5. 想定収益（推測・確定値ではない）

- モデル: 静的サイト広告（AdSense）+ テンプレ販売（LLM 使い分けチートシート Markdown）+ Shorts 送客 + candidate-005 への相互送客
- 規模: **未確認**。AI 開発者層の市場規模 + クイズ系コンテンツの興味度は推測のみ。承認後 B4 で**実利用ログを蓄積してから**月次収益レビューで再評価
- candidate-005 と比べて**収益化直接性は低い**（クイズは入口・実利用は candidate-005 等の本体ツールに誘導する設計）
- 数値を「確認済み」とは書かない（CLAUDE.local.md 機密 / 未確認ガード準拠）

## 6. ブロッカー（[[../05_monetization/scenarios/candidate-006_公開ブロッカー]] 要約）

| # | 種別 | 内容 | 優先度 | 状態 |
|---|---|---|---|---|
| B1 | 技術 | iPhone Safari 実機確認していない | 高 | 未確認 |
| B2 | 判定基準 | 個人観察ベース・客観化方針未確定（N-03 既知制約）| 高 | 未整備 |
| B3 | 公開先 | デプロイ先未決定（Vercel 推奨） | 中 | 未決定 |
| B4 | コンテンツ | 実利用ログによる根拠補強が必要 | 中 | 要整備 |
| B5 | UX | 質問重み調整 UI なし | 中 | 要拡張 |
| B6 | 広告 | AdSense 申請未着手 | 低 | 未着手 |
| B7 | 動画 | Shorts 送客動画なし | 低 | 未着手 |
| B8 | テンプレ | チートシート Markdown 未作成 | 低 | 未着手 |

## 7. 7 日実行プラン（[[../05_monetization/scenarios/candidate-006_7日実行プラン]] 要約）

- Day1: B1 + B2 iPhone 実機確認 & 判定基準客観化方針 1 ページ
- Day2: B3 Vercel デプロイ
- Day3-4: B4 実利用ログ蓄積（Claude Code / Codex / Gemini / ローカル LLM 各 2〜5 件）
- Day5: B5 質問重み調整 UI
- Day6: B8 チートシート Markdown ドラフト
- Day7: B7 Shorts 動画 1 本構成 + 公開可否レビュー

## 8. progress 投入設計（[[../05_monetization/scenarios/candidate-006_progress投入設計]] 要約）

- 最初の 3 ToDo: B1 + B2 / B3 / B4-1（各 1 セッション完結粒度）
- 投入は **approved + decision-log 記入後**（AI は送らない / 人間が POST）
- revenueImpact: **low**（candidate-005 medium より優先度低）

## 9. 判定材料（ChatGPT が判断するときの観点）

| 観点 | 確認材料 |
|---|---|
| MVP 動作 | 90_prototypes/llm-chooser/index.html / クイズ 4 種 + LLM カード 4 種 + 比較表 9×4 |
| 市場根拠 | 競合確認は低信頼度（再調査余地あり） / AI 利用者層は広いが興味度推測 |
| 判定の客観性 | **N-03 既知制約**。B2 で「観察可能な指標で裏付ける方針」を作る計画 |
| 公開コスト | 静的構成・Vercel 無料層・1 日で公開可能 |
| 収益期待 | low（candidate-005 への相互送客が主価値） |
| 並走可能性 | candidate-001/005/007 と完全並走可（別 repo / 別 prototype / 別市場） |

## 10. 承認判断の選択肢

- **approved**: 7 日プランに従い実行開始（B1→B2→B3 → 公開）
- **hold**: 客観化方針の追加調査を待つ（B2 を 1 ヶ月かけて整備してから再判断）
- **reject**: 判断基準の客観化が困難なため別案件を優先（idea_pool に戻す）
- **追加質問**: 競合確認の信頼度向上のため網羅調査をやり直す等

## 11. 想定される追加質問への回答

- Q: candidate-005 との優先度は？ → A: candidate-005（medium）を先行・candidate-006（low）は並走着手
- Q: 判定基準客観化の具体策は？ → A: B2 で「観察可能な指標で裏付ける方針」を作る（個人観察を完全に消すのは難しい）
- Q: AdSense 申請の前提条件は？ → A: 公開 + コンテンツ蓄積（B4 完了後）が前提・申請まで時間がかかる

## 12. 着手可否（実装現実性）

- 試作完成済（追加開発ほぼ不要）
- 静的構成のため Vercel 無料層で即時公開可能
- 判定基準客観化（B2）は**ペーパープランで 1 ページ作るだけで Day1 完了可能**
- → 着手可否は **OK**（B2 の客観化が次の論点）

## 13. 関連リンク

- [[../05_monetization/scenarios/candidate-006]] — candidate 本体
- [[../05_monetization/scenarios/candidate-006_公開ブロッカー]]
- [[../05_monetization/scenarios/candidate-006_7日実行プラン]]
- [[../05_monetization/scenarios/candidate-006_progress投入設計]]
- [[../90_prototypes/llm-chooser/README]] — MVP モック
- [[../05_monetization/scenarios/candidate-005]]（並走候補・相互送客）
- [[../05_monetization/scenarios/candidate-007]]（並走候補・別市場）
- [[../05_monetization/ChatGPT承認ゲート標準]] / [[../05_monetization/収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#61（前提 #63 / #80）

## 14. 承認後のフォロー

- approved 後、人間が [[../05_monetization/scenarios/candidate-006_progress投入設計]] の最初の 3 ToDo を progress 投入
- ToDo1 done → ToDo2 → ToDo3 の順で Claude が /focus か /vloop で実行
- Day7 終了時に公開可否レビュー（最終判断は人間）
