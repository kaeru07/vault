---
date: 2026-05-15
task: scrape-lab-v2 新規作成 (v1)
runId: 20260515-123610
targetApp: scrape-lab-v2
monetizationImpact: medium
theme: [workflow, market-research, app-strategy]
relatedRunIds: [20260516-postproc]
commitHashes: [2e3e761]
repo: https://github.com/kaeru07/scrape-lab-v2
repoVisibility: private
managementState: github-managed
---

# 2026-05-15 scrape-lab-v2 新規作成 (v1)

> **2026-05-16 後処理追記**: 本作業時点では git 未初期化のため commitHashes が空だった。
> 2026-05-16 に git init → 初回 commit `2e3e761` → `kaeru07/scrape-lab-v2` (private) 作成 → push 完了。
> commitHashes は遡及で初回 commit 1 件を記録（v1 全体が 1 commit）。
> 管理状態: **github-managed**（local-only → git-only → github-managed）。
> repo: https://github.com/kaeru07/scrape-lab-v2 / branch: main / visibility: private

---

## 1. 作業目的

- なぜこの作業をするのか: 既存 Scrape Lab (note アプリ) を継ぎ足さず、調査・収集・差分検知・AI レビュー連携・ファイル蓄積に強い v2 を新規に作る
- 背景: 既存は note 名義の単発取得ツールで、差分検知・履歴・レビュー連携が薄い。市場調査 Hub への流用も視野
- 期待効果: URL 単位の調査 → 差分検出 → JSON / Markdown 化 → ChatGPT / Claude / Codex への即レビューが 1 アプリで完結

---

## 2. 実施内容

- 主な変更:
  - `apps/scrape-lab-v2/` 配下を新規作成 (Next.js 14 + TS + Tailwind, port 3030)
  - 取得: Cheerio による static fetch (タイムアウト 15s / 5MB 上限 / 本文 20,000 字上限)
  - browser モード: 設計枠のみ (playwright 未導入)
  - URL バリデーション: localhost / 127.0.0.1 / 10. / 192.168. / 169.254. / 172.16-31. / .local / .internal / file:// / 非 http(s) をブロック
  - 差分: title / description / body (文字数差) / links 追加・削除
  - 保存: `data/runs/YYYY-MM-DD/<runId>.json` + `data/pages/<urlHash>/{history.ndjson, meta.json}`
  - エクスポート: JSON, Markdown
  - レビュー用コピー欄 (Claude/Codex 用 1500 字クリップ)
  - iPhone 対応 (44px タップ領域 / max-w-3xl / 縦長レイアウト)
  - `apps/app-index.md` に scrape-lab-v2 エントリ追加
- 関連調査・判断:
  - 既存 note アプリ (package 名 `scrape-lab`) は触らず独立アプリとして新設
  - DB は使わず JSON / NDJSON のみ (Claude/Codex が後から読みやすい構造を優先)

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/scrape-lab-v2/package.json | 新規 (Next.js 14 / cheerio / TS) |
| apps/scrape-lab-v2/tsconfig.json, next.config.mjs, tailwind.config.ts, postcss.config.js, .eslintrc.json, .gitignore | 新規 (土台) |
| apps/scrape-lab-v2/src/lib/{types,paths,url-validator,static-scraper,browser-scraper,storage,diff,markdown,id}.ts | 新規 (バックエンドコア) |
| apps/scrape-lab-v2/src/app/api/scrape/route.ts | 新規 (POST 取得) |
| apps/scrape-lab-v2/src/app/api/runs/route.ts | 新規 (一覧) |
| apps/scrape-lab-v2/src/app/api/runs/[runId]/route.ts | 新規 (詳細) |
| apps/scrape-lab-v2/src/app/api/runs/[runId]/export/route.ts | 新規 (JSON/Markdown DL) |
| apps/scrape-lab-v2/src/app/{layout.tsx,page.tsx,globals.css,runs/page.tsx,runs/[runId]/page.tsx} | 新規 (画面) |
| apps/scrape-lab-v2/src/components/{ScrapeForm,RunList,RunDetail,CopyBox}.tsx | 新規 (UI 部品) |
| apps/scrape-lab-v2/README.md | 新規 |
| apps/app-index.md | scrape-lab-v2 エントリ追加 / 最終更新日 2026-05-15 |

---

## 4. 検証結果

- typecheck: OK (build 中の型検証通過)
- build: OK (next build, 5 ルート / dynamic + static)
- lint: OK (next lint - No ESLint warnings or errors)
- 手動確認:
  - POST /api/scrape (example.com) → 200, runId 発行, JSON 保存
  - 2回目POST → diff.hasPrevious=true, comparedRunId 一致
  - SSRF: localhost / file:// / 192.168.0.1 → 400 ブロック
  - /api/runs → 2件返却
  - /api/runs/<id>/export?format=md → Markdown 返却
  - /api/runs/<id>/export?format=json → JSON 返却
  - / /runs /runs/<id> HTML 200
  - 詳細ページに「Claude/Codex」コピーボックスが描画
- 機密パターン事前チェック: OK (ルール例示のみ。.env / API キー / 個人情報なし)
- ob sync: 後述 (この応答内で実行)
- git push: 【2026-05-16 後処理で完了】git init → commit `2e3e761` → `kaeru07/scrape-lab-v2` (private) 作成 → push 済み。HEAD=origin/main 一致・clean
- build/lint 再確認: 【2026-05-16】lint pass / build pass（v1 から退行なし）

---

## 5. 未対応

- スキップした項目:
  - browser モードの実装 (設計枠のみ)
  - 本文の行レベル diff (文字数差のみ)
  - テーマ別調査 (キーワード→URLリスト→一括取得)
- 環境制約で実行できなかったこと:
  - 【解消済 2026-05-16】git init / GitHub リポ作成 / push を後処理で完了
- ユーザー判断待ち:
  - 【解消済 2026-05-16】git init → private リポ作成 → push 完了。残るは browser モード等の v2 判断
  - 既存 note アプリの位置づけ (継続 / アーカイブ)

---

## 6. 危険ポイント

- 既存機能への影響リスク: なし (新規ディレクトリのみ。既存 note アプリ / 他アプリは触っていない)
- DB / 認証 / 本番 / 機密: いずれも触れていない
- ロールバック手段: `apps/scrape-lab-v2/` ディレクトリを削除 + `apps/app-index.md` の追加行を revert
- 観察すべき項目:
  - 取得時のタイムアウト挙動 (15s 上限がきつい場合は調整)
  - data/ 配下のファイル数が増えた時の listRunSummaries の読み込みコスト

---

## 7. 次にやるべきこと

- フォローアップ作業:
  - browser モード (playwright-core + @sparticuz/chromium) 実装
  - 本文の行レベル diff (差分の解釈精度向上)
  - 取得結果の Obsidian 自動連携 (`06_research/` への書き出し)
  - テーマ別調査 (キーワード → URL リスト → 一括取得 → サマリ)
- ユーザー判断待ち事項:
  - GitHub リポ作成 → push まで進めるか
  - 既存 scrape-lab (note) との統廃合方針
- 観察項目: data/runs/ の容量が想定より早く膨らまないか

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: scrape-lab-v2
作業: scrape-lab-v2 新規作成 (v1)
runId: 20260515-123610
日付: 2026-05-15
GitHub commit: (未 push / scrape-lab-v2 は git init 未実施。obsidian-vault 側 commit は別途)

## 作業目的
- URL 単位の手動スクレイピング・差分検知・調査結果の JSON / Markdown 化・Claude/Codex レビュー連携を 1 アプリに集約する v2 を新設
- 既存 note (scrape-lab) は触らず独立アプリとして作成

## 実施内容
- Next.js 14 + TS + Tailwind + Cheerio で apps/scrape-lab-v2/ を新規作成
- API: POST /api/scrape, GET /api/runs, GET /api/runs/[id], GET /api/runs/[id]/export
- 画面: トップ (フォーム + 最近のrun), 一覧, 詳細 (本文 / meta / 見出し / リンク / 差分 / コピー欄)
- 保存: data/runs/YYYY-MM-DD/<runId>.json + data/pages/<urlHash>/{history.ndjson, meta.json}
- セキュリティ: http/https 限定, SSRF (localhost, private IP, .local, .internal) ブロック, タイムアウト 15s, レスポンス 5MB, 本文 20,000 字上限
- 差分: title / description / body 文字数差 / links 追加・削除
- iPhone 対応 (44px タップ領域, max-w-3xl)

## 変更ファイル
- apps/scrape-lab-v2/ (新規ディレクトリ一式)
- apps/app-index.md (1 行追加)

## 検証結果
- npm run lint: pass
- npm run build: pass (5 routes)
- POST /api/scrape (example.com): 200 / 2回目 diff.hasPrevious=true
- SSRF: localhost, file://, 192.168.0.1 → 400
- /api/runs, /api/runs/[id], /export (json,md): 全て OK
- /, /runs, /runs/[id]: 200

## 未対応
- browser モード実装 (playwright)
- 本文の行レベル diff
- テーマ別調査
- git init / GitHub remote (ユーザー判断待ち)

## 危険ポイント
- 新規ディレクトリのみ。既存 note は触っていない
- DB / 認証 / 本番 / 機密 いずれも触れていない

## 次にやるべきこと
- browser モード実装
- 行レベル diff
- Obsidian 自動連携
- GitHub リポ作成

## 確認したい観点
- SSRF ブロックリストの抜け漏れ (IPv6 / DNS rebinding など)
- 取得タイムアウトと上限値が実用的か
- 差分検知のアルゴリズム (文字数差のみで足りるか)
- ファイル保存先構造 (data/runs と data/pages の責務分離は妥当か)
- Claude/Codex レビュー用コピー文面の粒度
- 収益化インパクトの評価 (medium) は妥当か
````

---

## 関連

- progress runId: 20260515-123610 (正本)
- 関連 run: なし
- 関連アプリ: [[../02_apps/scrape-lab-v2]] (未作成)
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- レビュー運用: [[../04_reviews/Claude作業レビュー運用]]
- フォルダ運用: [[../20_reviews/README]]
