---
type: chatgpt-approval-pack
title: candidate-007 ChatGPT 承認パック（自己完結）
issue: kaeru07/vault#61
scenario: candidate-007
date: 2026-05-25
status: chatgpt_pending（ChatGPT 方向性承認待ち・AI は承認しない）
---

# candidate-007 ChatGPT 承認パック

> Issue #61 / #80。**このファイル単体で ChatGPT が方向性承認を判断できる**よう、判断材料を集約。
> [[../05_monetization/ChatGPT承認ゲート標準]] に従い **approved 化・progress 投入はしない**（Claude は材料集約まで）。
> 関連の一次ソース: [[../05_monetization/scenarios/candidate-007]] / [[../05_monetization/scenarios/candidate-007_公開ブロッカー]] / [[../05_monetization/scenarios/candidate-007_7日実行プラン]] / [[../05_monetization/scenarios/candidate-007_progress投入設計]] / [[../90_prototypes/vault-search-cheatsheet/README]] / [[../00_inbox/Vaultの見方_どこを見れば何がわかるか]]

> [!note] 用語注（Issue #69）
> - candidate / 有力候補 = 収益化する価値があるかもしれない案
> - chatgpt_pending = ChatGPT 方向性レビュー待ちの状態
> - approved / 承認済み = ChatGPT + あなたの判断で「実装着手 OK」になった状態
> - Vault Search Cheatsheet = iPhone Obsidian + GitHub Vault の検索パターン早見表 HTML
> - 既存「Vault の見方ガイド」 = [[../00_inbox/Vaultの見方_どこを見れば何がわかるか]] / 重複整理対象
> - 統合方針 A/B/C = 既存ガイドと本候補をどう統合するかの 3 案（A 併存 / B 既存索引化 / C 既存に統合）
> - revenueImpact = 収益インパクト（high / medium / low / none の 4 段階）
> - AdSense = Google の Web 広告 SDK
> - note 販売 = note.com 等で有料記事として情報をまとめて販売すること
> - progress 投入 = progress（作業履歴アプリ・localhost:3010）への ToDo 追加
> - Vercel = 静的サイトを無料層で公開できる代表サービス

## 1. 対象（1 行）

iPhone Obsidian + GitHub Vault の**検索パターン早見表**を静的 HTML 1 枚にまとめた成果物（90_prototypes/vault-search-cheatsheet / 外部 CDN ゼロ / MVP モック動作確認済）。Vault / Obsidian ユーザー層への AdSense + テンプレ販売 + note で収益化。

## 2. 推奨理由（なぜ candidate-007 を進めるか）

- **MVP がすでに動く**（90_prototypes/vault-search-cheatsheet/index.html / 検索ボックス + 20 件キーワード対応表 + iPhone/GitHub Tips + トラブル対応フロー）
- **API なしで完全成立**（外部呼び出しゼロ / file:// で動く / CDN ゼロ）
- **既存資産「Vault の見方ガイド」の流用度最大**（重複整理は必要だが新規開発ほぼ不要）
- **競合確認できず**（個人 Obsidian Vault 向け検索チートシートの SaaS / Web 化は未発見 / **ニッチ市場**）
- 静的構成のため **Vercel 無料層で即時公開可能**

## 3. 比較候補との差分

| 候補 | impact | scoreTotal | candidate-007 との差 |
|---|---|---|---|
| candidate-001 麻雀何切るアプリ公開 | high | 28 | 既存資産 + ASO 整備で公開。本命・収益化直接性が最も高い |
| candidate-005 token-speed-tool | medium | 28 | MVP 動作確認済・静的構成・公開ブロッカー最小・AI 開発者層 |
| candidate-006 llm-chooser | low | 26 | 判定基準客観化制約・AI 開発者層 |
| **candidate-007 vault-search-cheatsheet** | **low** | **28** | **既存資産流用度最大・継続性 4/5・既存ガイドとの統合方針が論点** |

> candidate-007 は scoreTotal 28（candidate-001/005 と同点）。impact は low だが、**既存資産流用度最大 + 継続性 4/5** が強み。**統合方針 A/B/C（B2）が承認時の主要論点**。

## 4. メリット / デメリット

### メリット

- **MVP がすでに動く**（試作不要・公開ブロッカーが少ない）
- **静的構成で公開が早い**（Vercel `vercel --prod` 1 コマンド）
- **API なし**で外部依存ゼロ（運用コスト・課金リスクなし）
- **既存資産流用度最大**（[[../00_inbox/Vaultの見方_どこを見れば何がわかるか]] / 個人運用ノウハウが直接転用可能）
- **継続性 4/5**（個人開発で日々 Vault 運用するため自然に蓄積される）
- **note との相性◯**（「個人開発 Vault 運用ガイド」note への展開可能）
- candidate-001/005/006 と**完全並走可能**（別市場のため干渉なし）

### デメリット / リスク

- **市場規模が狭い**（個人 Obsidian Vault ユーザー層 + 検索チートシート需要は限定的）
- **広告 CPC は低めの想定**（Vault / Obsidian ユーザー層は読了率高だが絶対 PV 小）
- **既存ガイドとの重複整理が前提**（B2 統合方針 A/B/C のいずれかを決定する必要）
- **個人 Vault 運用ノウハウの公開範囲**（B9 規約整理）で機密の線引きが必要

## 5. 想定収益（推測・確定値ではない）

- モデル: 静的サイト AdSense + テンプレ販売（チートシート Markdown 拡張版）+ note 販売（個人開発 Vault 運用ガイド）
- 規模: **未確認**。Vault / Obsidian ユーザー層の市場規模は推測のみ。承認後 B4 で**実運用ログを蓄積してから**月次収益レビューで再評価
- candidate-005 / 006 と比べて**収益化直接性は低い**（広告 CPC 低・絶対 PV 小・読了率高の特性想定）
- 数値を「確認済み」とは書かない（CLAUDE.local.md 機密 / 未確認ガード準拠）

## 6. ブロッカー（[[../05_monetization/scenarios/candidate-007_公開ブロッカー]] 要約）

| # | 種別 | 内容 | 優先度 | 状態 |
|---|---|---|---|---|
| B1 | 技術 | iPhone Safari 実機確認していない | 高 | 未確認 |
| B2 | 重複整理 | 既存「Vault の見方ガイド」との重複（統合方針 A/B/C 決定必要）| 高 | 未整備 |
| B3 | 公開先 | デプロイ先未決定（Vercel 推奨） | 中 | 未決定 |
| B4 | コンテンツ | 検索パターン 20 件のみ・実運用で増やす運用 | 中 | 要整備 |
| B5 | UX | iPhone / GitHub Tips セクション薄い | 中 | 要拡張 |
| B6 | 広告 | AdSense 申請未着手 | 低 | 未着手 |
| B7 | テンプレ | 拡張版 Markdown 未作成 | 低 | 未着手 |
| B8 | note | 「個人開発 Vault 運用ガイド」note 素材なし | 低 | 未着手 |
| B9 | 規約 | 個人 Vault 運用ノウハウの公開可能範囲明文化 | 中 | 未確認 |

## 7. 7 日実行プラン（[[../05_monetization/scenarios/candidate-007_7日実行プラン]] 要約）

- Day1: B1 + B9 iPhone 実機確認 & 規約明文化
- Day2: B2 既存ガイド重複整理（決定された統合方針 A/B/C 実施）
- Day3: B3 Vercel デプロイ
- Day4: B5 UX 拡充（iPhone/GitHub Tips スクショ追加）
- Day5: B4 検索パターン蓄積運用ルール
- Day6: B7 拡張版テンプレドラフト
- Day7: B8 note 1 本目ドラフト + 公開可否レビュー

## 8. progress 投入設計（[[../05_monetization/scenarios/candidate-007_progress投入設計]] 要約）

- 最初の 3 ToDo: B1 + B9 / B2 / B3（各 1 セッション完結粒度）
- 投入は **approved + 統合方針決定 + decision-log 記入後**（AI は送らない / 人間が POST）
- revenueImpact: **low**（candidate-005 medium より優先度低）

## 9. 統合方針 A/B/C（主要論点・ChatGPT 方向性レビュー時に決定）

| 案 | 説明 | メリット | デメリット | デフォルト推奨 |
|---|---|---|---|---|
| A | 既存ガイド + 本候補を併存 | 既存導線維持 | 重複保守 2 重 | × |
| B | 既存ガイドを索引化・本候補が実体ページ | 役割明確化 | 既存利用者の習慣変更 | **○（推奨）** |
| C | 既存ガイドへ統合・本候補は HTML サンプルのみ | 重複ゼロ | HTML 独自価値が薄れる | △ |

> Claude のデフォルト推奨は **B**（役割明確化 + 既存ガイド利用者の影響を最小化）。最終決定は ChatGPT + 人間判断。

## 10. 判定材料（ChatGPT が判断するときの観点）

| 観点 | 確認材料 |
|---|---|
| MVP 動作 | 90_prototypes/vault-search-cheatsheet/index.html / 検索ボックス + 20 件対応表 + sticky header |
| 市場根拠 | 競合確認は低信頼度（再調査余地あり） / Vault / Obsidian ユーザー層は限定的 |
| 既存資産流用 | 「Vault の見方ガイド」と素材重複 → B2 統合方針 A/B/C 決定が前提 |
| 公開コスト | 静的構成・Vercel 無料層・1 日で公開可能 |
| 収益期待 | low（CPC 低・絶対 PV 小・読了率高の特性想定）|
| 並走可能性 | candidate-001/005/006 と完全並走可（別市場・別 repo / 別 prototype） |

## 11. 承認判断の選択肢

- **approved**: 7 日プランに従い実行開始（B1 → B2 統合方針実施 → B3 公開）
- **hold**: 統合方針の追加検討待ち（B2 を 2 週間かけて再検討してから再判断）
- **reject**: 市場規模が狭すぎるため別案件を優先（idea_pool に戻す）
- **追加質問**: 既存ガイドの実利用状況確認・統合方針の選択肢追加等

## 12. 想定される追加質問への回答

- Q: candidate-005 / 006 との優先度は？ → A: candidate-005（medium）→ 006（low）→ 007（low）の順を推奨
- Q: 統合方針 A/B/C のデフォルトは？ → A: B（既存ガイドを索引化・本候補が実体ページ）
- Q: 個人 Vault 運用ノウハウの公開範囲は？ → A: B9 で公開可能範囲を明文化（個人情報・API キー・本番接続文字列は除外）

## 13. 着手可否（実装現実性）

- 試作完成済（追加開発ほぼ不要）
- 静的構成のため Vercel 無料層で即時公開可能
- B2 統合方針実施は**方針 B の場合 1 日で完了可能**（既存ガイド索引化 + 本候補へのリンク張り替え）
- → 着手可否は **OK**（B2 統合方針決定が次の論点）

## 14. 関連リンク

- [[../05_monetization/scenarios/candidate-007]] — candidate 本体
- [[../05_monetization/scenarios/candidate-007_公開ブロッカー]]
- [[../05_monetization/scenarios/candidate-007_7日実行プラン]]
- [[../05_monetization/scenarios/candidate-007_progress投入設計]]
- [[../90_prototypes/vault-search-cheatsheet/README]] — MVP モック
- [[../00_inbox/Vaultの見方_どこを見れば何がわかるか]] — 既存資産（重複整理対象）
- [[../05_monetization/scenarios/candidate-005]] / [[../05_monetization/scenarios/candidate-006]]（並走候補・別市場）
- [[../05_monetization/ChatGPT承認ゲート標準]] / [[../05_monetization/収益化シナリオ承認フロー]]
- Issue: kaeru07/vault#61（前提 #55 / #58 / #63 / #80）

## 15. 承認後のフォロー

- approved + 統合方針決定後、人間が [[../05_monetization/scenarios/candidate-007_progress投入設計]] の最初の 3 ToDo を progress 投入
- ToDo1 done → ToDo2 → ToDo3 の順で Claude が /focus か /vloop で実行
- Day7 終了時に公開可否レビュー（最終判断は人間）
