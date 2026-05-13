# Claude Code 依頼テンプレ — {{title}}

> 1 件の作業を Claude Code に依頼する雛形。コピーして埋め、そのまま Claude Code に渡せる形にする。
> 運用方針の正本は [[../03_prompts/Claude-Code標準運用]]。

---

## メタ

- 日付: YYYY-MM-DD
- 規模: 軽量 / 標準 / フル
- 対象アプリ / パス: 
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- 関連レビュー: 

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

role:
- architect: 着手前提 5 項目（何を直す / どこ触る / どこ触らない / 完了条件 / 検証方法）を最初に出す
- coder: 上記方針に沿って指定範囲のみ実装
- tester: typecheck + build + 動作確認
- reviewer: 既存機能への影響 / 不要な抽象化・コメント有無 / 危険変更の有無

検証:
1. npm run typecheck
2. npm run build（または render など）
3. <該当する手動確認手順>
4. エラーは握りつぶさない。原因不明なら「継続不能」として停止して報告

最終報告フォーマット:
【実施内容】
【変更ファイル】
【検証結果】
【未対応】
【次にやるべきこと】

POST 連携:
- 完了時に http://localhost:3010/api/execution-runs へ POST
- targetApp / targetTodoTitle / runStatus / summary / rawReport / changedFiles[] / checks{} / nextActions[]
- 応答末尾に「## progress レビュー用」をプレーンテキストで出す（コードブロック禁止）
```

---

## 着手前提（5 項目）— architect が最初に埋める

- 何を直すか: 
- どこを触るか: 
- どこを触らないか: 
- 完了条件: 
- 検証方法: 

---

## 関連

- [[../03_prompts/Claude-Code標準運用]]
- [[../03_prompts/claude-code-light]]
- [[../03_prompts/claude-code-standard]]
- [[../03_prompts/claude-code-full]]
- [[claude-review-template]]
