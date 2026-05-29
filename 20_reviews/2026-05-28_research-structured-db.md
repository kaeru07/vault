---
date: 2026-05-28
task: news-app Research構造化DB強化（schema/parser/UI/一覧/テンプレ）
runId: 20260528-225924
targetApp: news-app
monetizationImpact: medium
theme: [workflow, market-research, app-strategy]
relatedRunIds: [20260528-215335]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-28 news-app Research構造化DB強化

> 1 作業 = 1 ファイルの作業レビュー。ChatGPT レビューはセクション 8 をそのまま流す。

---

## 1. 作業目的

- なぜこの作業をするのか: news-app の Research を「AI ニュースメモ」ではなく「構造化 Research DB」として運用できる形へ強化する。
- 背景: 前作業（runId 20260528-215335）でトピックカード表示までは実装済み。今回は Topic schema 強化・Hermes/Codex 生成側接続準備・一覧 UX 改善が目的。
- 期待効果: 1 Topic = 1 判断単位として、確度・タグ・影響 PJ・ToDo 候補まで構造化し、一覧から概要を把握できる。生成側テンプレで蓄積を自動化する。

---

## 2. 実施内容

- ResearchTopic schema を拡張（topicId / summaryTlDr / confidence / tags / references.sourceType / affectsProjects / todoCandidate / updatedAt）。monetization → monetizationImpact に改名。
- parser を強化（日英見出し・ダッシュ有無・値のインライン/次行/ネスト・[label] URL・sourceType 推定・確度 高中低→high/medium/low・affectsProjects ネスト構造・yes/no）。slugify / generateTopicId helper を追加し topicId を保持（将来 /research/topic/[id] 用）。
- TopicCard に TL;DR / 確度バッジ / タグ wrap / sourceType バッジ（控えめ）/ ToDo 候補バッジ / 既存PJへの影響 / 更新日時を追加。line-clamp・truncate で iPhone 縦幅優先。
- 一覧（ResearchCard）と概要（/research）に Topic 数 / S・A 件数 / 上位タグ / TL;DR を表示。summarizeDocTopics helper を追加。
- Hermes/Codex 用テンプレを追加（content/research/_template.md、docs/research-format.md、Vault 03_prompts/research-topic-template.md）。
- 2026-05-27 の市場調査ログを新フィールドで再構成。
- 関連調査・判断: 旧フォーマットは全文 MarkdownView に fallback する設計を維持（Topic 0 件なら従来表示）。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/research/types.ts | ConfidenceLevel/SourceType/AffectsProjects 追加、ReferenceLink に sourceType、ResearchTopic 拡張、monetization→monetizationImpact |
| lib/research/parser.ts | 新フィールド解析・sourceType 推定・slugify/generateTopicId・fallback 維持 |
| lib/research/summary.ts | 新規。summarizeDocTopics（Topic 数/S・A 件数/上位タグ/TL;DR） |
| lib/research/vault.ts | CategorySummary に最新日次トピック統計を追加 |
| components/research/TopicCard.tsx | TL;DR/確度/タグ/sourceType/ToDo候補/affectsProjects/更新日時表示 |
| components/research/ResearchCard.tsx | 新フォーマット時に統計+タグ+TL;DR表示、旧は従来表示 |
| app/research/page.tsx | カテゴリ概要に統計+タグ+TL;DR表示 |
| content/research/_template.md | 新規。記入テンプレ |
| docs/research-format.md | 新規。フォーマット仕様+各基準 |
| content/research/daily-market-research/2026-05-27.md | 新フィールドで再構成（+ Vault 正本/ミラー） |
| (Vault) obsidian-sync-vault/03_prompts/research-topic-template.md | 新規。Hermes/Codex 生成プロンプト（ob sync 済） |

---

## 4. 検証結果

- typecheck: OK（next build に含まれる）
- build: OK（npm run build 成功 / 7 ページ）
- lint: n/a
- 手動確認: dev(3099) で全 research ルート 200。/research/market/2026-05-27 で TL;DR・確度(高中低)・タグ12種・sourceType(公式/ニュース/ランキング/SNS)・ToDo候補・affectsProjects(Progress/News App/Mahjong/Scrape Lab)・参考URL外部リンク11件・根拠折りたたみ・更新日時を確認。一覧/概要に Topics 7 / S1 A3 / 上位タグ / TL;DR を確認。旧 2026-05-26 と news/tools は fallback。
- 機密パターン事前チェック: OK（公開 URL のみ）
- ob sync: Fully synced
- git push: n/a（本タスクで git push 禁止。GitHub ミラーは未 push / pending）

---

## 5. 未対応

- /research/topic/[id] の routing は未実装（topicId 保持・helper のみ準備）。
- 過去日次（2026-05-26 等）は新フォーマット未変換（fallback 表示）。
- news / tools 用の構造化フォーマットは別途。
- GitHub ミラー（obsidian-vault）は push 禁止のため未 push。手動: `cd /root/company/obsidian-vault && git add -A && git commit && git push origin main`。
- iPhone 実機表示確認は未（curl で HTML 構造のみ）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。新フォーマット未対応は従来表示に fallback。型の monetization→monetizationImpact 改名は TopicCard 追従済み。
- DB / 認証 / 本番 / 機密: 触れていない。
- ロールバック手段: 追加コンポーネント/helper 削除 + types/parser を前 runId 状態へ戻す + 2026-05-27.md を戻す。
- 観察すべき項目: 生成側が旧フォーマットを出し続けると新規日次が fallback になる。

---

## 7. 次にやるべきこと

- Hermes/Codex 生成側を新テンプレで出力させる（最重要フォローアップ）。
- /research/topic/[id] routing 実装。
- news / tools 用の構造化フォーマット検討。
- 過去日次ファイルの一括移行の要否判断。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: news-app
作業: Research構造化DB強化（schema/parser/UI/一覧/テンプレ）
runId: 20260528-225924
日付: 2026-05-28
GitHub commit: (未push / pending)

## 作業目的
- news-appのResearchを「AIニュースメモ」から「構造化Research DB」へ。1 Topic=1判断単位として確度/タグ/影響PJ/ToDo候補まで構造化し、一覧から概要把握。生成側テンプレで蓄積自動化。

## 実施内容
- ResearchTopic schema拡張(topicId/summaryTlDr/confidence/tags/sourceType/affectsProjects/todoCandidate/updatedAt)
- parser強化(日英見出し/次行値/ネスト/[label]URL/sourceType推定/確度正規化/affectsProjects/yes-no)、slugify/generateTopicId
- TopicCardにTL;DR/確度/タグ/sourceTypeバッジ/ToDo候補/affectsProjects追加
- 一覧/概要にTopic数/S・A件数/上位タグ/TL;DR表示
- Hermes/Codex用テンプレ追加(content/research/_template.md, docs/research-format.md, Vault 03_prompts/research-topic-template.md)
- 旧フォーマットはfallback維持

## 変更ファイル
- lib/research/types.ts, parser.ts, summary.ts(新規), vault.ts
- components/research/TopicCard.tsx, ResearchCard.tsx
- app/research/page.tsx
- content/research/_template.md(新規), docs/research-format.md(新規)
- content/research/daily-market-research/2026-05-27.md

## 検証結果
- build: OK / typecheck: OK / lint: 未実行
- dev で全research routes 200、market/2026-05-27が全新フィールド表示、旧フォーマット/news/toolsはfallback
- 機密スキャン: OK(公開URLのみ)

## 未対応
- /research/topic/[id] routing未実装(topicId準備のみ) / 過去日次未変換 / news・tools構造化未 / GitHubミラー未push / iPhone実機未確認

## 危険ポイント
- 新フォーマット未対応はfallback。monetization→monetizationImpact改名はUI追従済み。DB/認証/本番なし。

## 確認したい観点
- schema設計は構造化Research DBとして妥当か（過不足）
- parserのfallback境界は安全か
- sourceType推定ルールは妥当か
- iPhone縦幅で崩れない設計になっているか
- 生成側テンプレ(docs/research-format.md)の基準は明確か
- 次にやるべき作業の優先順位は妥当か
- 収益化インパクト評価(medium)は妥当か
````

---

## 関連

- progress runId: 20260528-225924（正本）
- 関連 run: 20260528-215335（トピックカード化の前作業）
- 関連アプリ: [[../02_apps/news-app]]
- 生成テンプレ: [[../03_prompts/research-topic-template]]
- レビュー運用: [[_review_queue]]
