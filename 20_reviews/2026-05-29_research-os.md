---
date: 2026-05-29
task: news-app Research OS化（TopicURL/タグ検索/Timeline/ToDo導線/freshness/metrics）
runId: 20260529-082910
targetApp: news-app
monetizationImpact: medium
theme: [workflow, market-research, app-strategy]
relatedRunIds: [20260528-215335, 20260528-225924]
commitHashes: []
# reviewFileCommit:
---

# 2026-05-29 news-app Research OS化

> 1 作業 = 1 ファイルの作業レビュー。ChatGPT レビューはセクション 8 をそのまま流す。

---

## 1. 作業目的

- なぜこの作業をするのか: Research DB を「AI ニュースまとめ」ではなく「AI 開発工場用の構造化 Research OS」として、蓄積・検索・行動化できる構造へ寄せる。
- 背景: schema / parser / TopicCard / fallback / 生成テンプレ（runId 20260528-225924）まで実装済み。今回は Topic 単位 URL・タグ検索・Timeline・ToDo 化導線・重複基盤・freshness・evidence metrics・confidence 強化を追加。
- 期待効果: 1 Topic を URL で参照でき、タグ/タイムライン/重複/ToDo 候補で横断でき、情報の鮮度と根拠量が一目で分かる。

---

## 2. 実施内容

- Topic 単位 URL `/research/topic/[id]`（単独表示 + 関連Timeline + 重複候補）。
- タグ検索 `/research/tag/[tag]`、ToDo 化導線 `/research/todo-candidates`（重要度順 + 生成 JSON shape）。
- ResearchTopic に timelineKey / duplicateKey / similarityHints / sourceDate / relatedTopics / duplicateCandidates を追加し parser・UI・集計 helper を追従。
- 横断集計を vault.ts に追加（collectAllTopics / getTopicById / getTimelineFor / getDuplicateCandidates / getTopicsByTag / collectAllTags / collectTodoCandidateTopics）。
- TopicCard: evidence/sources metrics・情報日付・stale バッジ・confidence 強化（high=緑solid / medium=amber / low=赤・推測）・duplicateKey/類似ヒント・単独ページ/タグへのリンク。
- 一覧（ResearchCard）と概要（/research）に Todo・Stale 件数、ToDo候補リンク、タグ雲を追加。
- Hermes/Codex schema 更新（docs/research-format.md・_template.md・Vault 03_prompts/research-topic-template.md・会社 CLAUDE.local.md）。
- 2026-05-26 を新フォーマット化し timelineKey を 2026-05-27 と共有（Timeline 2 件成立）。short-video topic に sourceDate 2026-04-20 を付与し stale を実演。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/research/types.ts | ResearchTopic 拡張（timelineKey/duplicateKey/similarityHints/sourceDate/relatedTopics/duplicateCandidates）、TopicRef/TodoCandidate 追加 |
| lib/research/parser.ts | 新フィールドのラベル・解析・parseSourceDate |
| lib/research/summary.ts | isStale/effectiveSourceDate/toTodoCandidate/sortByImportanceThenDate、DocTopicSummary に todo/stale |
| lib/research/vault.ts | Topic 横断集計群、CategorySummary に todo/stale |
| components/research/TopicCard.tsx | metrics/freshness/confidence/重複/リンク |
| components/research/TopicListItem.tsx | 新規。コンパクト 1 行 |
| components/research/StructuredResearchView.tsx | docDate 伝播 |
| components/research/ResearchCard.tsx | Todo/Stale 件数 |
| app/research/topic/[id]/page.tsx | 新規。単独 + Timeline + 重複候補 |
| app/research/tag/[tag]/page.tsx | 新規。タグ検索 |
| app/research/todo-candidates/page.tsx | 新規。ToDo 候補 + JSON |
| app/research/[category]/[date]/page.tsx | docDate 渡し |
| app/research/page.tsx | ToDo候補リンク + タグ雲 + todo/stale |
| docs/research-format.md | 新フィールド項目表 + 解説 |
| content/research/_template.md | 新項目 |
| content/research/daily-market-research/2026-05-27.md | timelineKey/duplicateKey/sourceDate 付与 |
| content/research/daily-market-research/2026-05-26.md | 新フォーマット化（timeline 共有 / stale demo） |
| (Vault) obsidian-sync-vault/03_prompts/research-topic-template.md | 新項目（ob sync 済） |
| (company) CLAUDE.local.md | Research Topic フォーマット節を追加 |

---

## 4. 検証結果

- typecheck: OK / build: OK（npm run build 成功 / 11 routes）
- lint: n/a
- 手動確認: dev(3099) 全 routes 200。/research/topic/google-play-discovery-shift で関連Timeline（2026-05-26 entry）、/research/topic/ai-learning-video-funnel で重複候補（ai-learning-commoditized）、/research/tag/aso で 4 件、/research/todo-candidates で JSON shape、/research/market/2026-05-26 がカード化 + 「情報が古い」バッジ。存在しない topicId=404 / 未知 tag=空表示 200。旧 news/tools・Topic0件は fallback。
- 機密パターン事前チェック: OK（公開 URL のみ）
- ob sync: Fully synced
- git push: n/a（git push 禁止。GitHub ミラー未 push / pending）

---

## 5. 未対応

- relatedTopics / duplicateCandidates は型・parser・カード小表示のみ（専用導線 UI は未）。
- Progress 連携（ToDo 投入）は未（JSON shape 準備のみ）。
- tag 一覧専用ページ（/research/tags）は未（概要のタグ雲で代替）。
- GitHub ミラー未 push（手動: `cd /root/company/obsidian-vault && git add -A && git commit && git push origin main`）。
- iPhone 実機確認は未（curl で HTML 構造のみ）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。新 route は追加。Topic0件・旧フォーマットは従来表示に fallback。
- DB / 認証 / 本番 / 機密: 触れていない。
- 注意: static segment（topic/tag/todo-candidates）と dynamic [category] が同居。Next.js は static 優先で解決。"topic"/"tag" は有効カテゴリではないため /research/topic 単体は notFound。
- ロールバック手段: 新 route/コンポーネント削除 + types/parser/vault を前 runId 状態へ + markdown を戻す。

---

## 7. 次にやるべきこと

- ToDo 候補 → Progress 投入の実連携（JSON shape は準備済み）。
- trend detection / growing topic（timeline 差分検出）。
- duplicate の AI 判定（duplicateKey/similarityHints を入力に）。
- news / tools の構造化フォーマット。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: news-app
作業: Research OS化（TopicURL/タグ検索/Timeline/ToDo導線/freshness/metrics）
runId: 20260529-082910
日付: 2026-05-29
GitHub commit: (未push / pending)

## 作業目的
- Research DBを「AIニュースまとめ」から「AI開発工場用の構造化Research OS」へ。Topic単位URL/タグ検索/Timeline/ToDo導線/重複基盤/freshness/evidence metrics/confidence強化で、蓄積・検索・行動化できる構造に。

## 実施内容
- /research/topic/[id](単独+関連Timeline+重複候補), /research/tag/[tag], /research/todo-candidates(JSON shape)を新設
- ResearchTopicにtimelineKey/duplicateKey/similarityHints/sourceDate/relatedTopics/duplicateCandidates追加。parser/UI/集計helper追従
- TopicCardにevidence/sources metrics・情報日付・staleバッジ・confidence強化・重複キー・permalink/tagリンク
- 一覧/概要にtodo/stale件数、ToDo候補リンク、タグ雲
- Hermes/Codex schema更新(docs/research-format.md, テンプレ, Vault prompt, 会社CLAUDE.local.md)
- 2026-05-26を新フォーマット化しtimelineKey共有(Timeline 2件)・stale demo

## 変更ファイル
- lib/research/{types,parser,summary,vault}.ts
- components/research/{TopicCard,TopicListItem(新),StructuredResearchView,ResearchCard}.tsx
- app/research/{topic/[id],tag/[tag],todo-candidates}(新), [category]/[date], page.tsx
- docs/research-format.md, content/research/_template.md, daily-market-research/2026-05-26.md・2026-05-27.md

## 検証結果
- build: OK / typecheck: OK / lint: 未実行
- dev全routes 200、topic/tag/todo/timeline/duplicate/stale 動作、404/空表示も確認、旧形式fallback

## 未対応
- Progress連携(ToDo投入)未 / relatedTopics専用UI未 / GitHubミラー未push / iPhone実機未確認

## 危険ポイント
- 追加中心。static(topic/tag/todo) と dynamic[category] 同居だがstatic優先で解決。Topic0件/旧形式はfallback。

## 確認したい観点
- Research OSとしての構造設計(timeline/duplicate/freshness)は妥当か
- routeの静的/動的セグメント同居に落とし穴がないか
- stale/confidence/metricsの見せ方は判断に役立つか
- 次の一手(Progress連携/trend detection)の優先順位
- 収益化インパクト評価(medium)は妥当か
````

---

## 関連

- progress runId: 20260529-082910（正本）
- 関連 run: 20260528-225924（構造化DB強化）/ 20260528-215335（トピックカード化）
- 関連アプリ: [[../02_apps/news-app]]
- 生成テンプレ: [[../03_prompts/research-topic-template]]
- レビュー運用: [[_review_queue]]
