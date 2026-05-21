# Claude Code 標準運用

> Claude Code を**現場で迷わず使う**ための運用ページ。プロンプト雛形・role 分担・レビュー・進捗連携・収益化視点を 1 ページに集約。

---

## 最重要方針: 小ToDo消化ではなくEpic完了を優先する

Issue #50 により、今後は「小さいIssueを1件ずつ処理して止まる運用」をやめ、親Epicの完了条件までまとめて進めることを標準運用とする。

### やめること

- 小Phase完了だけで止まる
- 「次のIssueを作りました」で止まる
- 設計確認だけで止まる
- candidate判断前で止まる
- レビュー用まとめ前で止まる
- レビューのたびに小Issueを増やす

### 今後の標準

- 同じ目的の作業は既存Epic内でまとめて進める
- 5〜10件相当の関連作業を1回で進める
- 実行できる範囲は最後まで進める
- candidate判断まで続行する
- レビュー用まとめまで作る
- 小Issue追加ではなく、Epic完了を優先する
- ユーザー確認は原則しない

### 止まってよい場合

- 課金判断
- 外部公開
- 規約リスク
- 破壊的変更
- 認証情報操作
- git push失敗
- Epic完了
- Claude Code利用制限

### 止まってはいけない場合

- 小Phaseが終わっただけ
- 追加調査が残っている
- candidate判断が残っている
- レビューまとめが未作成
- 「次の小Issueを作ればよい」と判断しただけ

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

埋まらないまま着手しない。

ただし、完了条件を満たせる範囲が残っている場合は、小Phaseだけで止まらず続行する。

---

## role 分担（Issue #51 復元）

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

- 1 タスク終わったら 1 件レビューを残す
- ただしレビュー単位は「小Task」ではなく「Epic単位」を優先する
- レビュー時は「できるようになったこと」を先に整理する
- 1枚図サマリーを含める
- 固有名詞には日本語注釈を付ける

例:

- research-run = 情報収集を自動で行う処理
- idea-run = 情報から案を作る処理
- cron = 定期自動実行の仕組み
- candidate = 承認前の有力候補

---

## 最終報告フォーマット

タスク完了時の報告はこの形式に統一:

【実施内容】
【変更ファイル】
【検証結果】
【未対応】
【次にやるべきこと】
【成果物紹介】
- 何ができたか
- どこで見れるか
- 何に使うか
- どう使うか
- 次に見るファイル
- 注意点

追加:

【1枚図サマリー】
- できるようになったこと
- 現在地
- 次の一手
- ゴール

固有名詞には日本語注釈を付ける。

---

## build 確認（検証セット）

「コードが書けた」は完了ではない。「検証が通った」が完了。

| 種別 | 確認内容 |
|---|---|
| typecheck | `npm run typecheck` 等 |
| build | `npm run build` / `render` 等 |
| lint | プロジェクトに設定があれば |
| 動作確認 | dev / 該当画面・API の手動確認 |

- build は省略不可
- エラーは握りつぶさない
- 原因不明なら「継続不能」として停止

---

## progress 連携（必須）

ファイル変更を伴う作業はすべて、終了時に `POST http://localhost:3010/api/execution-runs` する。

必須フィールド（Issue #51 復元）: `targetApp` / `targetTodoTitle` / `runStatus` / `summary` / `rawReport`
任意: `targetTodoId` / `changedFiles[]` / `checks{}` / `errors[]` / `warnings[]` / `nextActions[]`

`runStatus`: 検証全 OK → `completed` / 一部未検証 → `partial` / 主要検証失敗 → `failed`

POST範囲:

- 実装
- 設計判断
- 運用変更
- Epic運用変更
- レビュー運用変更
- 「今後こうする」系ルール変更

POST不要:

- 単純な事実確認
- 1行質問
- 了解応答

---

## Obsidian 追記ルール

作業終了時、後から読み返す価値のあるものだけObsidianへ追記する。

Vault編集時:

- `obsidian-sync-vault` → `obsidian-vault` ミラー
- `git commit / push`
- `ob sync`

まで実行する。

### 追記先マッピング表（Issue #51 復元）

| 内容 | 追記先 |
|---|---|
| アプリ仕様・運用変更 | `02_apps/<app>.md` |
| 1 件レビュー | `04_reviews/YYYY-MM-DD-<title>.md` |
| プロンプト改善 | `03_prompts/*.md` |
| 収益化判断 | `05_monetization/*.md` |
| 市場調査結果 | `06_research/*.md` |
| 雑メモ | `00_inbox/inbox.md` or `未整理メモ.md` |

**追記しない**: 作業ログ全文（`engineering/reports/` が正本）/ 機密情報 / 一時状態。

### Phase A 運用テストの知見 / GitHub・レビューキュー連動（Issue #51 復元）

- **ファイル変更が 1 件でもある作業は ExecutionRun POST 必須**（追補・1 行追加・誤字修正も例外なし）
- **最終応答の冒頭に完了マーカー 7 項目**を必ず置く（完了状態 / POST / 作業レビュー / Obsidian追記 / ob sync / GitHub反映 / iPhone確認ページ）
- **POST 範囲拡大**: 実装変更ゼロでも、設計判断 / 運用ポリシー / 複数案比較 / 収益化・セキュリティ判断は `changedFiles: []` で必ず POST
- **GitHub 管理 Vault 反映**: Vault 編集を伴う作業は `obsidian-sync-vault` → `obsidian-vault` ミラー → `git commit / push` まで実行（詳細 → CLAUDE.local.md「GitHub 管理 Vault 反映ルール」 / [[GitHub反映ルール]]）
- **作業レビューファイル自動生成**: 作業終了時に `20_reviews/YYYY-MM-DD_<slug>.md` を 1 件生成（8 セクション + ChatGPT 依頼文）
- **レビューキュー連動**: `20_reviews/<slug>.md` を生成したら、同セッション内で必ず [[../20_reviews/_review_queue]] の「未レビュー」先頭に 1 件追加する（チェックボックス状態がレビュー状態の正本）
- 詳細はすべて `/root/company/CLAUDE.local.md` に正本あり

---

## 収益化優先ルール

判断に迷った場合の優先順位:

1. 公開済み / 公開直前
2. 既存ユーザー・視聴者あり
3. 撤退コストが低い
4. 収益化までが短い
5. 自分の知識が深い

追加:

- 「面白い」だけでなく「今の運用で勝ちやすい」を優先する
- Shorts化しやすさ
- MVP速度
- 継続性
- 広告相性
- note化しやすさ

も評価対象にする。

---

## 機密情報の扱い（Issue #51 復元）

- API キー / `.env` 中身 / 認証情報をプロンプトに**書かない**
- 環境変数名のみ参照する（例: `ANTHROPIC_API_KEY`）
- うっかり貼り付けたら commit 前に削除、push 後はローテーション
- Vault commit / push 前に機密パターン（`api[_-]?key` / `sk-` / `Bearer ` / `password` / `secret` / `token` / `-----BEGIN`）をスキャン

---

## vloop.md との役割分担（Issue #51）

- **本ファイル（標準運用）**: Claude Code 全般の運用ルール（role 分担・検証・progress 連携・Obsidian 追記・機密・収益化）
- **[[claude-commands/vloop]]**: vloop コマンド固有のルール（Epic 完了優先・連続実行フロー・停止条件・最終報告フォーマット）
- Epic 完了優先の原則は両方に置くが、vloop 固有の実行フロー詳細は vloop.md のみに置き、本ファイルには混ぜない

---

## 停止条件

以下だけ停止してよい:

- 超危険作業
- build失敗で継続不能
- 外部公開
- 課金設定
- 規約リスク
- 破壊的変更
- 必須ファイル不足
- Claude Code利用制限
- ユーザーが明示停止
- Epic完了

それ以外は止まらず進める。

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
- Issue #50: vloopを小ToDo消化ではなくEpic完了まで進める運用に変更する
