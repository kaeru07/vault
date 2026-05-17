---
title: Git管理Vault（obsidian-vault）反映候補
type: candidates
created: 2026-05-17
runId: 20260517-185136
---

# Git管理Vault反映候補

> `/root/company/obsidian-vault`（GitHub mirror / ChatGPT参照用）へ反映すべき候補一覧。
> **今回は自動で大量コピーしない。** 下表は提案のみ。実反映はユーザー判断。

| # | 反映元（sync-vault） | 反映先候補パス | 反映理由 | 優先度 | 反映すべき要約内容 |
|---|---|---|---|---|---|
| 1 | `00_inbox/chatgpt-import/00_インポート概要.md` | `00_inbox/chatgpt-import/00_インポート概要.md` | インポート実施記録。ChatGPT側からも経緯参照可能にする | 中 | 661会話/期間/カテゴリ件数/機密チェック結果のサマリーのみ |
| 2 | `00_inbox/chatgpt-import/02_重要会話候補.md` | 同左 | high 63件の所在をChatGPTレビューの入口にできる | 中 | high一覧（メタのみ・本文なし） |
| 3 | `important/2026-05-13_意思決定ログ-POST範囲拡大ルール.md` | `03_prompts/` または `04_reviews/` | 既に CLAUDE.local.md に反映済の運用ルールの**出典記録**。判断履歴として価値 | 高 | POST範囲拡大の決定事項・背景のみ |
| 4 | `important/2026-05-13_Opus47プロンプト方針.md` | `03_prompts/` | 「管理は厳密・実装は自由」プロンプト方針の出典。テンプレ改善の根拠 | 高 | 方針の結論と適用ルール |
| 5 | `important/2026-05-12_Obsidian活用法-progress分業設計.md` | `07_architecture/` または `06_research/` | progress=実行 / Obsidian=判断 の役割分担設計の出典。アーキ判断の根拠 | 中 | 役割分担の結論・フォルダ設計案 |
| 6 | `important/2026-05-16_アプリ案工場-Phase1構想.md` | `05_monetization/` | APIなし収益化「アプリ案工場」構想。収益化ロードマップに接続 | 中 | Phase1スコープ・評価軸 |

---

## 反映しないもの（明示）

- `01_会話インデックス.md`（36KB・low多数）→ sync側のみで十分。mirror不要
- `important/` の麻雀/Shorts/GoalPlanner系 → 既に実装済み案件の会話。記録価値は低、mirror不要
- 機密フラグ会話の本文 → そもそもMarkdown化していない。mirror対象外
- 添付（画像/PDF/確定申告書類）→ 絶対にmirrorしない

---

## 反映手順（実施時）

1. ユーザーが反映可否を判断（本ファイルの優先度=高 から）
2. 該当ファイルのみ sync-vault → obsidian-vault へ個別コピー
3. 機密スキャン → commit → push（既存 § GitHub管理Vault反映ルール準拠）
4. 大量一括コピーは禁止（1テーマ1commit）

## 関連

- [[00_インポート概要]] / [[02_重要会話候補]]
- 反映ルール: `/root/company/CLAUDE.local.md` § GitHub 管理 Vault 反映ルール
