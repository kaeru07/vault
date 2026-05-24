---
type: chatgpt-approval-pack
title: candidate-005 ChatGPT 承認パック（自己完結）
issue: kaeru07/vault#60
scenario: candidate-005
date: 2026-05-24
status: chatgpt_pending（ChatGPT 方向性承認待ち・AI は承認しない）
---

# candidate-005 ChatGPT 承認パック

> Issue #60。**このファイル単体で ChatGPT が方向性承認を判断できる**よう、判断材料を集約。
> [[../05_monetization/ChatGPT承認ゲート標準]] に従い **approved 化・progress 投入はしない**（Claude は材料集約まで）。
> 関連の一次ソース: [[../05_monetization/scenarios/candidate-005]] / [[../05_monetization/scenarios/candidate-005_公開ブロッカー]] / [[../05_monetization/scenarios/candidate-005_7日実行プラン]] / [[../05_monetization/token-speed-tool]] / [[../90_prototypes/token-speed-tool/README]]

## 1. 対象（1 行）

外部 LLM API を呼ばずに **tokens/sec / TTFT / 体感スコア**を比較できるブラウザツール（90_prototypes/token-speed-tool / 静的 HTML + JSON / MVP モック動作確認済）。AI 開発者層への広告 + note 販売 + Shorts 送客で収益化。

## 2. 推奨理由（なぜ candidate-005 を進めるか）

- **MVP がすでに動く**（90_prototypes/token-speed-tool/index.html / サンプル 5 件で比較表示）
- **APIなしで完全成立**（外部 LLM 呼び出しゼロ・file:// で動く・CDN ゼロ）
- 既存ベンチサイトは**数値羅列で UX 悪い** → 「体感スコア」という独自軸で差別化可能
- ベンチ専業 iOS / Web アプリは**事実上不在**（iTunes Search で確認・サンプル少なく信頼度 low だが市場空白の傍証）
- 静的構成のため **Vercel 無料層で即時公開可能**（candidate-001 のような build 検証ブロッカーなし）
- candidate-001（麻雀本命・high）と**並走可能**（別 repo / 別ドメイン / 干渉なし）

## 3. 比較候補との差分

| 候補 | impact | scoreTotal | candidate-005 との差 |
|---|---|---|---|
| candidate-001 麻雀何切るアプリ公開 | **high** | 28 | 既存資産 + ASO 整備で公開。本命・収益化直接性が最も高い |
| candidate-002 麻雀何切る Shorts | medium | 27 | candidate-001 への送客が主価値 |
| candidate-003 Obsidian テンプレ | low | 24 | 調査根拠 2・要再調査 |
| candidate-004 麻雀問題系 3 アプリ統合 | medium | 25 | 統合方針未確定 |
| **candidate-005 token-speed-tool** | **medium** | **28** | **MVP 動作確認済・静的構成・公開ブロッカー最小・並走可** |

> candidate-005 は scoreTotal 28 で candidate-001 と同点。impact は medium だが、**MVP 完成度では 5 件中最高**（他は試作未着手 or 試作不要案）。

## 4. メリット / デメリット

### メリット

- **MVP がすでに動く**（試作不要・公開ブロッカーが少ない）
- **静的構成で公開が早い**（Vercel `vercel --prod` 1 コマンド）
- **APIなし**で外部依存ゼロ（運用コスト・課金リスクなし）
- AI 開発者層は**広告クリック率が高め**（IT 系メディア平均より上）
- **Shorts と note の両方に展開可能**（速度比較動画 / 月次ベンチまとめ note）
- **既存資産 nanikiru-shorts** の動画パイプラインを流用可能
- candidate-001 と**完全並走可能**

### デメリット / リスク

- **市場規模は推測**（AI 開発者層の絶対数は日本市場では限定的）
- **モデル更新が早い**（Claude 4.6→4.7、Gemini 2.5→3 等で再ベンチ需要が出るが、それ自体が継続性に効くとも言える）
- **競合参入リスク**（OpenRouter や Artificial Analysis が UX 改善版を出す可能性）
- **体感スコア計算式は暫定**（重み 0.4/0.4/0.2 は個人観察ベース・要実測検証）
- **公開ベンチデータの取り込み規約**は未整理（B9 で対応）

## 5. 想定収益（推測・確定値ではない）

- モデル: アプリ広告（AdMob/AdSense）+ note 販売（月次ベンチまとめ）+ Shorts 送客（速度比較動画）
- 規模: **未確認**。AI 開発者層の市場規模は推測のみ。承認後 B4 で**実測データを蓄積してから**月次収益レビューで再評価
- candidate-001（麻雀）と比べて市場が**狭く深い**（読了率高 / CPC やや高 / 絶対 PV は小）の特性想定
- 数値を「確認済み」とは書かない（CLAUDE.local.md 機密 / 未確認ガード準拠）

## 6. ブロッカー（[[../05_monetization/scenarios/candidate-005_公開ブロッカー]] 要約）

| # | 種別 | 内容 | 優先度 | 状態 |
|---|---|---|---|---|
| B1 | 技術 | iPhone Safari 実機確認していない | 高 | 未確認 |
| B2 | 公開先 | デプロイ先未決定（Vercel 推奨） | 高 | 未決定 |
| B3 | ドメイン | 独自ドメインの要否 | 中 | 未決定 |
| B4 | コンテンツ | サンプルデータ 5 件のみ・実測蓄積必要 | 中 | 要整備 |
| B5 | UX | ログ解析が最小（Claude Code / Codex のみ） | 中 | 要拡張 |
| B6 | 広告 | AdSense 申請未着手 | 低 | 未着手 |
| B7 | 動画 | Shorts 送客動画なし | 低 | 未着手 |
| B8 | note | 月次まとめ note の素材なし | 低 | 未着手 |
| B9 | 規約 | 公開ベンチデータの取り込みルール未整理 | 中 | 未確認 |

> candidate-001 と比べてブロッカーは多いが、**B1+B2+B9 が解ければ公開可能状態**に到達（B4-B8 は公開後に磨く）。

## 7. 最初の 30 日プラン（[[../05_monetization/scenarios/candidate-005_7日実行プラン]] を月次へ拡張）

| 期間 | フォーカス | 主要 ToDo | 完了条件 |
|---|---|---|---|
| Day1-2 | 公開可否確定 | B1 iPhone 実機 + B9 規約 / B2 Vercel デプロイ | 本番 URL 確定 + iPhone 動作確認 |
| Day3-6 | 実測蓄積 + UX 拡張 | B4 実測 10-15 件追加 / B5 Ollama ログ対応 / B7 動画構成 1 本 | sample-data.json 15 件 / Ollama 形式解析 / Shorts 1 本ドラフト |
| Day7 | 公開判断材料 | 公開 Go/No-Go レビュー（判断は人間） | Go/No-Go 材料 1 枚 |
| Day8-14 | 公開実行（人間承認下） | Vercel 本番化 / アクセス計測導入（Google Analytics / Plausible 等） | 公開済 + 計測導入 |
| Day15-21 | コンテンツ + 送客 | B4 実測 30 件以上 / B7 Shorts 投稿 1 本 / B8 note 1 本目 構成 | 実測 30 件 + Shorts 1 本 + note 構成 |
| Day22-30 | 計測・改善 | アクセス / 広告収益の計測開始 → 月次レビュー | 実数値が月次収益レビューに記録 |

> Day8 以降の公開・広告設定・投稿は**人間判断 / 操作**（外部公開・課金・広告は AI 停止条件）。AI は Day1-7 の準備 + 各 Day のドラフトまで + B4 実測の自動記録のみ。

## 8. candidate-001 との並走判断

| 軸 | candidate-001 | candidate-005 |
|---|---|---|
| 主市場 | 麻雀学習者（日本） | AI 開発者（日本 + 英語圏）|
| 既存資産 | mahjong-trainer（実装済み）| 新規 MVP（試作済み）|
| MVP 完成度 | 既存実装あり（build 未検証）| 静的 HTML 動作確認済 |
| 公開ブロッカー | B1-B5（中規模） | B1-B9（やや多いが解除しやすい）|
| 収益化導線 | アプリ広告 + Shorts 送客 | 広告 + note + Shorts 送客 |
| 並走可能性 | — | ✅ 完全並走可（別 repo / 別 URL / 別市場）|

→ **両方を承認**して並走させるのが効率最大。candidate-001 が本命（high）、candidate-005 が並走（medium）。Day7 時点で両方が「公開可能状態」に到達する想定。

## 9. 実装現実性

| 観点 | 評価 | 内容 |
|---|---|---|
| MVP 最小構成 | ◎ | すでに静的 HTML + JSON で動作確認済 |
| Vercel / Netlify 相性 | ◎ | 静的構成のため無料層で即時 deploy 可 |
| iPhone 運用相性 | ○ | viewport 設定済 / 実機未確認（B1）|
| 拡張容易性 | ◎ | 機能追加（Chart.js / ログ解析拡張）は CDN 追加 + JS 修正のみ |
| 既存資産接続 | ○ | nanikiru-shorts 動画パイプライン / Claude Code 実測ログ |

## 10. 収益導線の確かさ

| 導線 | 確度 | 根拠 |
|---|---|---|
| アプリ広告（AdSense）| 中 | AI 開発者層は IT 系広告との相性が高い・PV 規模は推測 |
| note 販売 | 中-高 | 「2026 Q2 LLM ベンチまとめ」等は実測データが揃えば執筆可能 |
| Shorts 送客 | 中 | 速度比較は 30 秒で完結する動画素材 |
| プレミアム機能（将来）| 低 | 履歴無制限・Bring your own key 等は実証データ蓄積後 |

## 11. 着手可否（人間 + ChatGPT 判断材料）

- **公開できる**: B1 + B2 + B9 解除で公開可能（Day1-2 で完了予定）
- **収益が出るか**: 不明（実測必要 / 月次レビューで判定）
- **継続できるか**: モデル更新ごとに**自然と再ベンチ需要が出る**ため継続性はある（陳腐化リスクとも言えるが）
- **candidate-001 と並走できるか**: ✅ 完全並走可

## 12. 人間判断欄（ChatGPT 方向性判断 → 人間が確定）

- 承認コマンド（[[../05_monetization/ChatGPT承認コマンド標準]]）:
  `candidate-005 approve` / `candidate-005 hold: <理由>` / `candidate-005 reject: <理由>`
- ChatGPT 方向性: （未記入）approve / reject / hold（理由: ____）
- 人間 status 確定: （未記入）approved / rejected / hold → [[../05_monetization/decision-log]]
- 確定日 / 判断者: （未記入）

## 13. Claude（AI）からの推奨

- 推奨: **candidate-005 を candidate-001 と並走で進める**
- 理由: ① MVP がすでに動く ② 静的構成で公開ブロッカー少 ③ 別市場で candidate-001 と干渉なし ④ 実測データが集まれば note 化 / Shorts 化に拡張可能 ⑤ 失敗時の損失が極小（Vercel 無料層 + 既存試作）

> ただし Claude は **承認しない**。ChatGPT 方向性判断 → 人間 status 確定の順序を厳守（[[../05_monetization/ChatGPT承認ゲート標準]]）。

## 14. ChatGPT への質問観点（レビュー時に答えて欲しい）

1. candidate-005 を candidate-001 と並走させる判断は妥当か（リソース分散にならないか）
2. scoreTotal 28 / 6 軸 25/30 のスコアリングは妥当か（過大評価・過小評価ないか）
3. 体感スコア計算式（tokensPerSec × 0.4 + (10/TTFT) × 4 + outputChars/100 × 0.2）の重みは妥当か
4. AI 開発者層を主市場に据えた判断は妥当か（市場規模見込みは現実的か）
5. B9 公開ベンチデータの取り込みルール（「手動取り込み + 出典必須」）は十分か
6. ChatGPT 承認後、人間が approved 化判断する流れ（[[../05_monetization/ChatGPT承認ゲート標準]]）は本ケースでも適用可か

## 関連

- [[../05_monetization/scenarios/candidate-005]]（candidate 本体）
- [[../05_monetization/scenarios/candidate-005_公開ブロッカー]]
- [[../05_monetization/scenarios/candidate-005_7日実行プラン]]
- [[../05_monetization/scenarios/candidate-005_progress投入設計]]
- [[../05_monetization/token-speed-tool]]（仕様 + trace）
- [[../90_prototypes/token-speed-tool/README]]（MVP モック）
- [[candidate-001_ChatGPT承認パック]]（並走候補 / 形式比較）
- [[ChatGPT承認待ち]]（承認入口・候補-005 はまだ追加していない）
- Issue: kaeru07/vault#60（前提 #61 / #62 / #63）
