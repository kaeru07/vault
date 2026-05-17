---
date: 2026-05-17
task: Vault横断の複数ToDoソースを読み取り優先順位付け（再実行）
runId: 20260517-125144
targetApp: company-meta
monetizationImpact: medium
theme: [workflow, app-strategy, monetization]
relatedRunIds: [20260516-193605, 20260516-190343, 20260516-182442, 20260516-171158, 20260515-000616]
commitHashes: []   # レビュー対象の作業 commit / 関連 commit のみ。本ファイル自身の push commit は記録不要
# reviewFileCommit:   # 任意。本ファイル自身の push commit を残したい場合に手動で埋める
---

# 2026-05-17 Vault横断の複数ToDoソースを読み取り優先順位付け（再実行）

> 1 作業 = 1 ファイル の自動生成レビュー。
> 詳細ルールは `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール。

---

## 1. 作業目的

- なぜこの作業をするのか: Vault配下に散在する複数のToDoソースを横断的に読み取り、横断優先順位を1本に統合して「次に何をやるか」を即断できる状態にする
- 背景: 前回（2026-05-17 想定）の同種作業の成果物が progress ExecutionRun / obsidian-vault / obsidian-sync-vault / git のいずれにも残っておらず痕跡ゼロだった。GitHub未反映ではなく、そもそも永続化されていなかった。推測再構成は共有Vaultへの捏造になるため、ユーザー判断で「今ここで再実行」を選択
- 期待効果: ToDoの正本（Vault）と操作盤（progress）の役割分担に沿って、着手すべき作業の優先順位を構造化し、次サイクルの着手判断を高速化する

---

## 2. 実施内容

- 主な変更（箇条書きで具体的に）:
  - `obsidian-sync-vault/20_reviews/2026-05-17_vault-multiple-todo-review.md` を新規作成（本ファイル）
  - `obsidian-sync-vault/20_reviews/_review_queue.md` の「未レビュー」先頭に1件追記（createdAt 2026-05-17 12:43）
- 関連調査・判断（読み取ったToDoソース）:
  - **A.** `00_inbox/ChatGPT ToDo投函箱の最小テスト.md` — ToDo候補4件（progressにInbox画面追加 / chatgpt-todo読込 / 読込済み管理 / ToDo化ボタンでpending_approval追加）
  - **B.** `00_inbox/Vault正本のChatGPT投函APIをprogressに追加する.md` — POST /api/vault-inbox 追加要望。**実装完了済**（runId 20260516-182442 / 190343 / 193605）= 消化済み
  - **C.** `02_apps/途中プロジェクト一覧.md` — 停止中7件（mahjong-analyzer / 麻雀ポータル統合 / ポーカー学習 / AIニュース系 / 市場調査ツールSaaS化 / Obsidian×note記事 / progressテンプレ販売）。各々に優先度・次アクション既記載
  - **D.** `02_apps/progress.md` 改善候補5件（Obsidian連携CLI / work-queue→Daily転記 / アプリ別週次時間集計 / 未完了・興味抽出クエリ / 公開テンプレ範囲文書化）
  - **E.** `20_reviews/2026-05-15_idle-work-backlog.md` — 30案+TOP5の既整理素材（上位サマリのみ横断対象として参照）

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `obsidian-sync-vault/20_reviews/2026-05-17_vault-multiple-todo-review.md` | 新規。Vault横断ToDo優先順位付けレビュー（8セクション+frontmatter） |
| `obsidian-sync-vault/20_reviews/_review_queue.md` | 「未レビュー」先頭に本レビュー1件を追記 |

---

## 4. 検証結果

- typecheck: n/a（実装変更なし）
- build: n/a（実装変更なし）
- lint: n/a
- 手動確認: Vault内5系統のToDoソースを `cat` / `grep` で実読し、消化済み（B）を除外して優先順位を再算出
- 機密パターン事前チェック: OK（API値・トークン・.env 等の機密は本ファイルに一切含まない）
- ob sync: Fully synced（最終応答前に実行）
- git push: 末尾「次にやるべきこと」参照（obsidian-vault ミラー後に commit/push）

---

## 横断優先順位（本作業の中核成果）

### 最優先（high）

| # | ToDo | 出所 | 理由 |
|---|---|---|---|
| H1 | progress に Vault `00_inbox` を読む **Inbox画面**を追加 | A / D | 投函API（B）は実装完了済だが「投函しても progress で見えない」非対称が運用最大のボトルネック。投函→可視化の欠落側を埋める |
| H2 | chatgpt-todo **読み込み済み管理** + **ToDo化ボタン**（project-tasks.json へ pending_approval 追加） | A | H1とセットで「Vault正本 → progress操作盤」の中核フローが完成。冪等取り込みは 20260516-171158 で基盤あり |

### 中（medium）

| # | ToDo | 出所 | 理由 |
|---|---|---|---|
| M1 | work-queue → Obsidian Daily 転記 / focus-work 自動追記CLI | D | 手動コピー・POST抜けの構造的削減。Phase B（progress×Obsidian双方向連携）の地盤 |
| M2 | 麻雀ポータル統合 LP モック1枚スケッチ | C（優先度中・収益化高） | 既存麻雀アプリ群（trainer/quiz/nanikiru-shorts）の送客集約。着手は軽量 |
| M3 | 市場調査ツール × activity-mining 統合可能性メモ | C（優先度中・収益化高） | 資産化インパクト大。まず1メモから |

### 低（low / 後回し）

- アプリ別・週次の時間集計ビュー（D）
- mahjong-analyzer ポータル統合可否の1行判断（C）
- ポーカー学習 / AIニュース系 / note記事 / progressテンプレ販売（C・いずれも市場調査前段階で優先度低）
- 未完了・興味の波 抽出クエリビュー / 公開テンプレ範囲文書化（D）

### クローズ提案（優先順位対象外）

- `00_inbox/Vault正本のChatGPT投函APIをprogressに追加する.md`（B）→ 実装完了済（runId 20260516-182442 / 190343 / 193605）。00_inbox からの除去は破壊操作のため**実行せず提案に留める**（ユーザー判断）

---

## 5. 未対応

- スキップした項目: 前回作業の推測再構成（痕跡ゼロのため捏造回避目的で意図的に非実施。ユーザー合意済）
- 環境制約で実行できなかったこと: なし
- ユーザー判断待ち: B（投函API ToDo）の 00_inbox からのクローズ可否

---

## 6. 危険ポイント

- 既存機能への影響リスク: なし（実装変更ゼロ・レビューmdとqueue追記のみ）
- DB / 認証 / 本番 / 機密に触れたか: 触れていない
- ロールバック手段: 本mdとqueue追記行の削除のみで原状復帰可能
- 観察すべき項目: 前回成果物が永続化されなかった事象。POST/review/queue を機械的に同時実行する運用が守られているかを継続観察

---

## 7. 次にやるべきこと

- フォローアップ作業: H1（progress Inbox画面）を次の集中作業サイクルの最優先着手候補に置く
- ユーザー判断待ち事項: B のクローズ可否 / 横断優先順位の妥当性確認
- 観察項目（数日〜数週間）: progress操作盤化（H1/H2）完了後に「投函→可視化→ToDo化」の往復が1経路で回るか
- 関連 ToDo の追加候補: H1/H2 完了時に「読み込み済みのアーカイブ運用」をToDo化

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: company-meta
作業: Vault横断の複数ToDoソースを読み取り優先順位付け（再実行）
runId: 20260517-125144
日付: 2026-05-17
GitHub commit: （本レビューmdのpush commitは git log --follow で参照）

## 作業目的
- Vault配下に散在する複数ToDoソースを横断読み取りし、横断優先順位を1本化する
- 前回同種作業の成果物が痕跡ゼロだったため、推測再構成ではなく現存ソースから再算出した

## 実施内容
- A: 00_inbox 投函箱2件 / B: 投函API要望（実装完了済=消化済み）
- C: 02_apps 途中プロジェクト7件 / D: progress.md 改善候補5件 / E: idle-work-backlog 30案
- high2件・medium3件・low多数・クローズ提案1件に整理

## 変更ファイル
- 20_reviews/2026-05-17_vault-multiple-todo-review.md（新規）
- 20_reviews/_review_queue.md（未レビュー先頭に追記）

## 検証結果
- typecheck / build / lint: n/a（実装変更なし）
- 手動確認: Vault 5系統ToDoソースを実読し消化済み除外で再算出
- 機密スキャン: OK（機密値なし）

## 未対応
- B（投函API ToDo）の 00_inbox クローズはユーザー判断待ち

## 危険ポイント
- 実装変更ゼロ。レビューmdとqueue追記のみ

## 次にやるべきこと
- H1: progress に Vault 00_inbox を読む Inbox画面を追加
- H2: 読み込み済み管理 + ToDo化ボタン（pending_approval）

## 確認したい観点
- 横断優先順位（H1/H2 を最優先とした判断）は妥当か
- 消化済み（B）を優先対象外とした判断は妥当か
- 収益化インパクト medium の評価は妥当か
- 前回成果物が永続化されなかった運用穴への対策（POST/review/queue 機械的同時実行）で十分か
````

---

## 関連

- progress runId: 20260517-125144（正本）
- 関連 run: 20260516-193605 / 20260516-190343 / 20260516-182442 / 20260516-171158 / 20260515-000616
- 関連アプリ: [[../02_apps/progress]]
- 途中プロジェクト: [[../02_apps/途中プロジェクト一覧]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
