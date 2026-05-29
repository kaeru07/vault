---
date: 2026-05-28
task: news-app research詳細ページのトピックカード化 + 参考URL表示
runId: 20260528-215335
targetApp: news-app
monetizationImpact: low
theme: [workflow, market-research]
relatedRunIds: []
commitHashes: []
# reviewFileCommit:
---

# 2026-05-28 news-app research詳細ページのトピックカード化 + 参考URL表示

> 1 作業 = 1 ファイルの作業レビュー。ChatGPT レビューはセクション 8 をそのまま流す。

---

## 1. 作業目的

- なぜこの作業をするのか: Vault 由来の市場調査 Markdown を news-app に表示できたが、長文がそのまま並び iPhone で読みにくかった。
- 背景: 1 ページに競合 / ユーザー不満 / 注目ジャンル / 次の一手が長文混在し、どこが重要か分からない。Vault も news-app もカード化前提の構造になっていなかった。
- 期待効果: 1 トピック = 1 カードに分離し、iPhone 縦画面で重要度・種別・要約・参考URLが一目で分かるようにする。

---

## 2. 実施内容

- 主な変更:
  - Vault Markdown を新フォーマット（## 今日の結論 / ## トピック一覧 + ### Topic N: + 種別・重要度・要約・根拠・参考URL・収益化への示唆・次アクション / ## 今日のToDo候補 / ## 保留・未確認）へ整理。
  - news-app に新フォーマット解析（parser）と Topic カード UI（TopicCard / StructuredResearchView）を追加。
  - 詳細ページを「結論カード → トピックカード一覧 → ToDo → 保留」に分離。長文の塊表示を廃止。
  - 参考URL はドメイン名ラベルの外部リンク（新規タブ）。最大 3 件、超過は「他 n 件」折りたたみ。横はみ出しは truncate / break-words で防止。
  - 根拠は折りたたみ。長い箇条書きは最大 3 件表示 + 折りたたみ。
  - 解析不能な Markdown（旧フォーマット・news / tools 等）は従来 MarkdownView に fallback。
- 関連調査・判断:
  - 正本は obsidian-sync-vault。news-app は content/research の表示用コピーを読む構成を踏襲。
  - 重要度未指定は B、種別未指定（またはテンプレ列挙のまま）は general 扱い。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| lib/research/types.ts | ReferenceLink / ResearchTopic / StructuredResearch 型追加、ResearchDoc に structured? 追加 |
| lib/research/parser.ts | 新フォーマット構造化解析（トピック / 結論 / ToDo / 保留 / 参考URL）。トピック 0 件は undefined→fallback |
| components/research/TopicCard.tsx | 新規。1 トピック 1 カード（種別 / 重要度バッジ・要約・収益化示唆・次アクション・参考URL・根拠折りたたみ） |
| components/research/StructuredResearchView.tsx | 新規。結論 + トピックカード + ToDo + 保留の構成 |
| app/research/[category]/[date]/page.tsx | structured.topics>0 でカード表示、無ければ MarkdownView。原文 Markdown は折りたたみ |
| content/research/daily-market-research/2026-05-27.md | 新フォーマット（トピックカード + 参考URL）へ書き換え（表示用コピー） |
| (Vault) obsidian-sync-vault/06_research/daily-market-research/2026-05-27.md | 同内容（正本 / ob sync 済み） |

---

## 4. 検証結果

- typecheck: OK（next build に含まれる）
- build: OK（npm run build 成功）
- lint: n/a（個別 lint 未実行）
- 手動確認: dev(port3099) で /research /research/market /research/news /research/tools と各詳細が 200。/research/market/2026-05-27 はカード表示（結論・7 トピック・種別/重要度バッジ・参考URL外部リンク 11 件・根拠折りたたみ・ToDo・保留）。news/tools/market2026-05-26 は fallback 表示でクラッシュなし。
- 機密パターン事前チェック: OK（公開 URL のみ。API キー・トークン等なし）
- ob sync: Fully synced
- git push: n/a（本タスクで git push 禁止。GitHub ミラーは未 push / pending）

---

## 5. 未対応

- 2026-05-26 等の過去日次ファイルは新フォーマット未変換（fallback 表示のまま）。
- obsidian-vault（GitHub ミラー）は push 禁止のため未 push。手動なら `cd /root/company/obsidian-vault && git add 06_research/daily-market-research/2026-05-27.md && git commit && git push origin main`。
- iPhone 実機での表示確認は未（curl で HTML 構造のみ確認）。

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。詳細ページの表示分岐を追加しただけで、新フォーマット未対応は従来表示に fallback。
- DB / 認証 / 本番 / 機密: 触れていない。
- ロールバック手段: 追加コンポーネント削除 + page.tsx の分岐除去 + 2026-05-27.md を旧フォーマットへ戻す。
- 観察すべき項目: Vault 生成側（Hermes/Codex）が旧フォーマットで出し続けると新規日次は fallback になる。

---

## 7. 次にやるべきこと

- フォローアップ: Vault 生成側テンプレを新フォーマットで出力するよう更新。
- ユーザー判断待ち: GitHub ミラーの push 可否。過去ファイルを新フォーマットへ一括移行するか。
- 観察項目: 実機 iPhone での折り返し・タップ範囲・読みやすさ。
- 関連 ToDo: news / tools 用の構造化フォーマット検討。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: news-app
作業: research詳細ページのトピックカード化 + 参考URL表示
runId: 20260528-215335
日付: 2026-05-28
GitHub commit: (未push / pending)

## 作業目的
- Vault由来の市場調査Markdownが長文で並びiPhoneで読みにくかったため、1トピック=1カードに分離して読みやすくする。

## 実施内容
- Vault Markdownを新フォーマット(結論/トピック一覧/ToDo/保留)に整理。
- news-appに新フォーマット解析とTopicカードUIを追加。詳細ページを結論→トピックカード→ToDo→保留に分離。
- 参考URLはドメインラベルの外部リンク(最大3件+他n件)。根拠と超過箇条書きは折りたたみ。
- 解析不能Markdownは従来表示にfallback。

## 変更ファイル
- lib/research/types.ts, lib/research/parser.ts
- components/research/TopicCard.tsx (新規), StructuredResearchView.tsx (新規)
- app/research/[category]/[date]/page.tsx
- content/research/daily-market-research/2026-05-27.md (+ Vault正本)

## 検証結果
- build: OK / typecheck: OK / lint: 未実行
- 手動確認: dev で全research routes 200、market/2026-05-27 がカード表示、他は fallback でクラッシュなし
- 機密スキャン: OK(公開URLのみ)

## 未対応
- 過去日次ファイル未変換 / GitHubミラー未push / iPhone実機未確認

## 危険ポイント
- 表示分岐追加のみ。未対応フォーマットは従来表示にfallback。DB/認証/本番なし。

## 次にやるべきこと
- Vault生成側テンプレを新フォーマット化 / 過去ファイル移行判断 / news・tools用構造化検討

## 確認したい観点
- 抜け漏れがないか
- 既存機能を壊していないか
- 解析fallback設計は妥当か
- iPhone縦表示で崩れない設計になっているか
- 次にやるべき作業の優先順位は妥当か
````

---

## 関連

- progress runId: 20260528-215335（正本）
- 関連アプリ: [[../02_apps/news-app]]
- レビュー運用: [[_review_queue]]
