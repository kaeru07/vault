# Claude Code 依頼テンプレ — {{title}}

> 1 件の作業を Claude Code に依頼する雛形。コピーして埋め、そのまま Claude Code に渡せる形にする。
> 運用方針の正本は [[../03_prompts/Claude-Code標準運用]] / `/root/company/CLAUDE.local.md`。

---

## メタ

- 日付: YYYY-MM-DD
- 規模: 軽量 / 標準 / フル
- 対象アプリ / パス:
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- 関連レビュー:
- Vault 編集の有無: あり（`obsidian-sync-vault` 配下を触る）/ なし

---

## 依頼本文（このまま Claude Code に渡す）

```
目的:
- <達成したいこと。1〜3 行で明確に>

対象:
- <アプリパス。例: /root/company/apps/ny01/progress>
- <触ってよいファイル / フォルダ>

やること:
1. <項目1>
2. <項目2>
3. <項目3>

禁止事項:
- <壊してはいけない既存仕様>
- <スコープ外のファイル / 機能>
- DB スキーマ・本番デプロイ・外部 API キー変更は原則禁止
- .env / 認証情報は触らない・出力しない
- Vault 外のファイルを不用意に変更しない

role:
- architect: 着手前提 5 項目（何を直す / どこ触る / どこ触らない / 完了条件 / 検証方法）を最初に出す
- coder: 上記方針に沿って指定範囲のみ実装
- tester: typecheck + build + 動作確認
- reviewer: 既存機能への影響 / 不要な抽象化・コメント有無 / 危険変更の有無

検証:
1. npm run typecheck
2. npm run build（または render など）
3. <該当する手動確認手順>
4. 機密パターン事前チェック（api_key / sk- / Bearer / password / secret / token / -----BEGIN 等）
5. エラーは握りつぶさない。原因不明なら「継続不能」として停止して報告

Vault 編集を伴う場合の追加手順（順序厳守）:
1. sync-vault を編集（/root/company/obsidian-sync-vault/）
2. 機密パターン事前チェック
3. `cd /root/company/obsidian-sync-vault && source ~/.nvm/nvm.sh && nvm use 22 >/dev/null && ob sync`
4. sync-vault → obsidian-vault へミラー（rsync）
5. obsidian-vault で再度機密スキャン
6. git status / diff 確認 → commit → push
7. progress へ POST
8. 20_reviews/YYYY-MM-DD_<slug>.md 生成
9. 20_reviews/_review_queue.md の未レビュー先頭に追記
10. 最終応答（完了マーカー 7 項目）

POST 連携（必須）:
- 完了時に http://localhost:3010/api/execution-runs へ POST
- targetApp / targetTodoTitle / runStatus / summary / rawReport / changedFiles[] / checks{} / nextActions[]
- 任意: monetizationImpact / theme / obsidianSummary / obsidianSaveTarget
- ファイル変更が 1 件でもあれば POST 必須（例外なし）
- 実装変更ゼロでも「設計判断 / 運用ポリシー / 複数案比較 / 収益化判断 / セキュリティ判断」を含む応答は changedFiles: [] で POST 必須

最終報告フォーマット（先頭に完了マーカー 7 項目を固定で出す）:
✓ 完了状態: <一行サマリー>
✓ ExecutionRun POST: runId XXXXXXXX-XXXXXX
✓ 作業レビュー: 20_reviews/YYYY-MM-DD_<slug>.md / n/a
✓ Obsidian追記: <ファイル名> / 行数: N / n/a
✓ ob sync: Fully synced / n/a
✓ GitHub反映: commit <短ハッシュ> / pushed / n/a
✓ iPhone確認ページ: <ページ名と確認観点>

その後で本文:
【実施内容】
【変更ファイル】
【検証結果】
【未対応】
【次にやるべきこと】
```

---

## 着手前提（5 項目）— architect が最初に埋める

- 何を直すか:
- どこを触るか:
- どこを触らないか:
- 完了条件:
- 検証方法:

---

## Vault 同期チェックリスト（Vault 編集ありの作業のみ）

- [ ] `obsidian-sync-vault` 配下の編集が完了している
- [ ] 機密パターン（API キー / `.env` 値 / SSH 鍵本文 / `-----BEGIN` / `Bearer ` / `sk-` 等）が含まれていない
- [ ] `ob sync` を実行し `Fully synced` を確認した
- [ ] sync-vault → obsidian-vault へ rsync ミラーを実行した
- [ ] obsidian-vault 側で機密スキャンを再実行した
- [ ] `git add → commit → push` まで完了した（push 未実施は完了扱いにしない）
- [ ] progress へ POST し runId を取得した
- [ ] `20_reviews/YYYY-MM-DD_<slug>.md` を生成した
- [ ] `20_reviews/_review_queue.md` の未レビュー先頭に 1 件追記した（`createdAt` JST 必須）
- [ ] 最終応答の冒頭に完了マーカー 7 項目を置いた

詳細ルール:
- 同期コマンド・失敗時対応 → [[../03_prompts/Vault同期ルール]]
- GitHub 管理 Vault のミラー・push 手順 → [[../03_prompts/GitHub反映ルール]]
- 20_reviews / レビューキュー → `/root/company/CLAUDE.local.md` § 作業レビューファイル自動生成ルール / レビューキュー連動ルール

---

## 関連

- [[../03_prompts/Claude-Code標準運用]]
- [[../03_prompts/Vault同期ルール]]
- [[../03_prompts/GitHub反映ルール]]
- [[../03_prompts/claude-code-light]]
- [[../03_prompts/claude-code-standard]]
- [[../03_prompts/claude-code-full]]
- [[claude-review-template]]
- [[session-review-template]]
