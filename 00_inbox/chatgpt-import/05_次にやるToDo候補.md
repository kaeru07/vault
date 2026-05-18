---
title: ChatGPT会話から抽出した次にやるToDo候補
type: todo-candidates
created: 2026-05-17
runId: 20260517-185136
---

# 次にやるToDo候補

> 重要会話（high）から抽出した未消化・継続候補。実着手は progress / _review_queue で管理。
> ここは「候補の置き場」。承認・着手判断はユーザー / pm フェーズ。

## 高優先（収益化・運用基盤に直結）

- [ ] **APIなし「アプリ案工場」Phase1 を progress に追加**（出典: 2026-05-16 AI運用構成提案）
  - 手入力でアプリ案 → 収益化評価軸でスコア → モック作成ToDo自動登録。外部API/DB不使用
  - → [[important/2026-05-16_アプリ案工場-Phase1構想]]
- [ ] **Codex を読み取り専用レビューに常用する運用を確立**（出典: 2026-05-16 ClaudeとCodexのAgent活用）
  - Claude Code主導 → Codexで対立レビュー。execution-runs.json を渡し標準出力でレビュー要約
  - → 提案ドラフト作成済: [[../../03_prompts/Codexレビュー常用運用]]（status: proposal・採用判断はユーザー/pm）
- [ ] **scrape-lab-v2 本体を GitHub管理化 + market-research-hub 設計**（出典: 2026-05-12〜13 リソース活用法/市場調査）
  - JSON/NDJSON蓄積。Claude/Codex共通runner。収益化インパクト順に整理

## 中優先（運用改善）

- [ ] **obsidian-sync-vault のバックアップ方針を確定**（出典: Obsidian活用法提案・複数回指摘）
  - git管理外リスク。ob sync忘れ対策・追記専用ルールはCLAUDE.local.mdに一部反映済 → 残りを点検
- [ ] **prompt_inbox と work_queue の分離運用を検討**（出典: Obsidian活用法提案）
  - 「思いつきプロンプト貯め場」と「承認済み実行キュー」を分ける。現 _review_queue 運用との整合確認
- [ ] **Claude有料プラン Agent SDK 月次クレジット（6/15〜）の活用方針を決める**（出典: 2026-05-14 Claude有料プランの変更）
  - claude -p / Agent SDK / GitHub Actions のクレジット枠。自動化の費用設計

## 低優先（候補ストック）

- [ ] 麻雀タイプ診断アプリ案の精査（出典: 2026-05-15 麻雀タイプ診断アプリ）
- [ ] nanikiru-shorts テンプレの追加改善ネタ（出典: 2026-05-10 麻雀何切る動画制作。多くは実装済の可能性 → 重複確認）
- [ ] 未分類226件のうち収益化/法務系のサルベージ（必要時のみ）

---

## 注意

- 上記は会話時点の構想。**実装済みのもの（Goal Planner / progress停滞検知 / vault-inbox API 等）は重複なので着手前に現状確認**
- 法務/著作権カテゴリ8件は公開前チェックに使えるため、アプリ公開タスク発生時に参照

## 関連

- [[02_重要会話候補]] / [[03_カテゴリ別まとめ]]
- 着手管理: progress `/queue` / `20_reviews/_review_queue.md`
