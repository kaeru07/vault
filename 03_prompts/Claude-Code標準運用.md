# Claude Code 標準運用

> Claude Code を**現場で迷わず使う**ための運用ページ。プロンプト雛形・role 分担・レビュー・進捗連携・収益化視点を 1 ページに集約。

---

## 規模分類と雛形

| 規模 | 目安 | 使う雛形 |
|---|---|---|
| **軽量** | 文言修正・UI 微修正・単発不具合・1〜3 ファイル | [[claude-code-light]] |
| **標準** | 機能追加・画面改修・データ追加 | [[claude-code-standard]] |
| **フル** | 新規アプリ・DB・認証・全体設計・複数アプリ横断 | [[claude-code-full]] |

迷ったら **light → standard → full** の順で必要分だけ拡張する。最初からフルを選ばない。

---

## 着手前提（必須 5 項目）

どの規模でも**着手前にこの 5 項目を埋める**:

1. **何を直すか**
2. **どこを触るか**
3. **どこを触らないか**
4. **完了条件**
5. **検証方法**

埋まらないまま着手しない。集中作業モードでは architect フェーズで自動的にこの 5 項目を出力する。

---

## role 分担

| role | 役割 |
|---|---|
| secretary | 朝会・記録・チェックポイント |
| pm | 優先順位判断・タスク選択 |
| architect | 変更範囲・リスク確認・5 項目出力 |
| coder | architect 方針に沿った実装（指定範囲のみ） |
| tester | build / lint / dev / API / UI 確認 |
| reviewer | 妥当性・影響・残課題の確認 |
| deployer | Vercel / Supabase / 本番反映（該当時のみ） |

詳細定義は `/root/company/engineering/prompts/roles.md` に正本あり。

---

## 作業レビュー

- 1 タスク終わったら 1 件レビューを残す（[[Claude作業レビュー運用]] へ詳細）
- レビュー雛形は `90_templates/claude-review-template.md`
- 結論は **採用 / 修正後採用 / 差し戻し / 棚上げ** のいずれかを明記

---

## 最終報告フォーマット

タスク完了時の報告は**この形式に統一**:

```
【実施内容】
【変更ファイル】
【検証結果】
【未対応】
【次にやるべきこと】
```

会話で済ませず、必要分は Obsidian へ追記する。

---

## build 確認（検証セット）

「コードが書けた」は完了ではない。「**検証が通った**」が完了。

| 種別 | 確認内容 |
|---|---|
| typecheck | `npm run typecheck` 等 |
| build | `npm run build` / `render` 等 |
| lint | プロジェクトに設定があれば |
| 動作確認 | dev / 該当画面・API の手動確認 |

省略可能性:

- `build` は省略不可
- エラーは握りつぶさない。原因不明なら「**継続不能**」として停止して報告

---

## progress 連携（必須）

**ファイル変更を伴う作業はすべて、終了時に `POST http://localhost:3010/api/execution-runs` する**。

必須フィールド: `targetApp` / `targetTodoTitle` / `runStatus` / `summary` / `rawReport`
任意: `targetTodoId` / `changedFiles[]` / `checks{}` / `errors[]` / `warnings[]` / `nextActions[]`

`runStatus`:
- 検証全 OK → `completed`
- 一部未検証 → `partial`
- 主要検証失敗 → `failed`

POST 後、応答末尾に「**## progress レビュー用**」ブロックを**プレーンテキスト**で出す（コードブロック禁止）。

POST 不要な場合:
- 質問への回答のみ / ファイル変更ゼロ / 調査結果のみ
- ユーザーが「POST するな」と明示

---

## Obsidian 追記ルール

作業終了時、**後から読み返す価値のあるものだけ** Obsidian に 1 〜 3 行で追記する。

| 内容 | 追記先 |
|---|---|
| アプリ仕様・運用変更 | `02_apps/<app>.md` |
| 1 件レビュー | `04_reviews/YYYY-MM-DD-<title>.md` |
| プロンプト改善 | `03_prompts/*.md` |
| 収益化判断 | `05_monetization/*.md` |
| 市場調査結果 | `06_research/*.md` |
| 当日進捗 1 行 | `01_daily/today.md` |
| 雑メモ | `00_inbox/inbox.md` or `未整理メモ.md` |

**追記しない**: 作業ログ全文（`engineering/reports/` が正本）/ 機密情報 / 一時状態。

**Vault を 1 ファイルでも編集したら**、応答前に `ob sync` を実行して iPhone Obsidian へ反映する。詳細 → [[Vault同期ルール]]

**Phase A（運用ルールのみ）運用中**: `monetizationImpact` が `high` / `medium` の作業、または `theme` に `monetization` / `market-research` / `app-strategy` / `prompt-template` / `obsidian` / `workflow` を含む作業は、Obsidian 側へ 1〜3 行で手動追記する。判定基準・保存先マッピング・機密フィルタの詳細は `/root/company/CLAUDE.local.md` の「Obsidian 要約追記ルール」セクションを参照（連携設計レビュー: progress runId 20260513-202846）。

**Phase A 運用テストの知見（追補）**:
- **ファイル変更が 1 件でもある作業は ExecutionRun POST 必須**（追補・1 行追加・誤字修正も例外なし）→ CLAUDE.local.md「progress アプリ自動連携」§ POST必須
- **最終応答の冒頭に完了マーカー 5 項目（完了状態 / POST / Obsidian追記 / ob sync / iPhone確認ページ）を必ず置く** → CLAUDE.local.md「最終応答の冒頭ルール（完了マーカー）」
- **Obsidian 追記フォーマットは「runId + 要約 + 次の確認事項」の統一形式**（1 行目: runId + 要約 / 2 行目: 次の確認事項） → CLAUDE.local.md「Obsidian 要約追記ルール § 追記の手順」
- **POST 範囲拡大**: 実装変更ゼロでも、設計判断 / 運用ポリシー / 複数案比較 / 収益化・セキュリティ判断 / 「今後こうする」系のルール変更議論は `changedFiles: []` で必ず POST する。POST 省略可は「単純な事実確認・1 行質問・了解応答」のみに狭める → CLAUDE.local.md「POST必須（範囲拡大ルール）」
- **GitHub 管理 Vault 反映**（2026-05-13 確定）: Vault 編集を伴う作業は `obsidian-sync-vault` → `obsidian-vault` ミラー → `git commit / push` まで実行。詳細 → CLAUDE.local.md「GitHub 管理 Vault 反映ルール」 / [[GitHub反映ルール]]
- **作業レビューファイル自動生成**（2026-05-14 追加）: 作業終了時に `20_reviews/YYYY-MM-DD_<slug>.md` を 1 件自動生成（8 セクション + ChatGPT 依頼文）。`90_templates/session-review-template.md` をコピーして埋める。完了マーカーは **7 項目**（作業レビューを追加）。詳細 → CLAUDE.local.md「作業レビューファイル自動生成ルール（20_reviews/）」 / [[../20_reviews/README]]
- **レビューキュー連動**（2026-05-14 追加）: `20_reviews/<slug>.md` を生成したら、同セッション内で必ず [[../20_reviews/_review_queue]] の「未レビュー」先頭に 1 件追加する（チェックボックス状態がレビュー状態の正本）。ChatGPT は GitHub から `_review_queue.md` を入口にレビュー対象を辿る。詳細 → CLAUDE.local.md「レビューキュー（_review_queue.md）連動ルール」

---

## 収益化優先ルール

判断に迷ったときの優先順位（[[../05_monetization/収益化ロードマップ]] と同じ）:

1. **公開済み / 公開直前**のものを優先
2. **既存ユーザー / 視聴者がいるチャネル**を優先
3. **撤退コストが低い**ものから
4. **収益化までの距離が短い**ものから
5. **自分の知識が深い領域**から（麻雀・将棋・AI ワークフロー）

「面白そうだが収益と離れている」作業は、**進めるなら明示的に**進める。default では収益化に近い方を選ぶ。

---

## 機密情報の扱い

- API キー / `.env` 中身 / 認証情報をプロンプトに**書かない**
- 環境変数名のみ参照する（例: `ANTHROPIC_API_KEY`）
- うっかり貼り付けたら commit 前に削除、push 後はローテーション

---

## 停止条件

以下に該当した時だけ停止して報告する。それ以外は止まらず進める。

- 超危険作業しか残っていない
- build 失敗の原因が不明で継続不能
- 既存機能に影響が出た
- progress と指示が重大に矛盾
- 必須ファイル・前提が欠けていて継続不能
- Claude Code 利用制限が近い（停止前に progress 更新を最優先）
- ユーザーが明示的に停止を指示

---

## 関連

- [[claude-code-light]]
- [[claude-code-standard]]
- [[claude-code-full]]
- [[Claude作業レビュー運用]]
- [[../02_apps/progress]]
- [[../05_monetization/収益化ロードマップ]]
- [[../90_templates/claude-task-template]]
- [[../90_templates/claude-review-template]]
