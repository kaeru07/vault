---
date: 2026-06-09
task: news-app 市場調査ビューがVault更新を反映しなくなった原因を特定し修正
runId: 20260609-204723
targetApp: news-app
monetizationImpact: low
theme: [workflow, obsidian, market-research]
relatedRunIds: []
commitHashes: [7328f42]
---

## 1. 作業目的

ニュースアプリ（news-app）の市場調査ビューが、Vault には 2026-06-09 までの新しい日次調査ファイルが存在するのに **2026-05-27 付近で停止して見える**原因を、Vault→集計→生成→表示フローのどこで止まっているか実際に特定し、修正まで完了する。前回セッションは出力なしで終了していたため、今回は確認結果・変更有無に関わらず必ず報告する。

## 2. 実施内容

- データフロー追跡（表示 app/research/* → 集計 lib/research/vault.ts → 参照先解決 resolveResearchRoot → Vault原本 06_research）
- Vault・同梱コピー・稼働形態（pm2/port/Vercel）の実測
- 停止箇所2点を特定
- `scripts/sync-research-content.mjs` を新規作成（Vault→同梱 content/research 同期・破壊削除なし・dry-run対応）
- 同期実行（55ファイル / 05-27→06-09）
- `package.json` に `sync:research` / `prebuild` を追加（再凍結防止）
- 未コミットだった研究ビュー機能一式を含めスコープしてcommit / push
- tsc / build / 実画面で検証

## 3. 変更ファイル

- `news-app/scripts/sync-research-content.mjs`（新規）
- `news-app/package.json`（sync:research / prebuild 追加）
- `news-app/content/research/**`（daily-ai-news / daily-ai-tools / daily-market-research / market-research-method-review を 05-27→06-09 へ 55ファイル同期）
- `news-app/lib/research/{vault,parser,types}.ts` + `summary.ts`、`app/research/**`、`components/research/**`、`app/api/research/rescan`、`docs/research-format.md`（前回未コミット分を反映）

## 4. 検証結果

- tsc --noEmit: 0エラー
- next build: 成功（/research は ƒ Dynamic）
- next start 実画面（VPS）: rootKind=Vault本体 / 最新日付=2026-06-09 / status=最新 を確認。/research/news も 06-09 表示
- 同梱コピー単独でも各 daily-* の最新が 06-09 になっていることを確認
- 機密スキャン: 高確度シークレット検出なし
- git push origin main 成功（c50faa9..7328f42）
- lint: ESLint未設定で対話プロンプト→ build 内の型チェック付きlintで代替（pass）

## 5. 未対応

- ESLint 未設定（`next lint` が初期化プロンプトを出す）。本作業では設定追加せず据え置き
- Vercel 側の再デプロイ確認は push 後の自動デプロイ前提（手動トリガは未実施）
- Vault→同梱同期の自動化（朝の定期実行 / デプロイ前フック組込）は未実施。当面は手動 `npm run sync:research` または build 時 prebuild に依存

## 6. 危険ポイント

- 同梱 content/research は「Vault からの派生コピー」。同期スクリプトは破壊削除をしないため孤立ファイルは残りうる（実害は古い日付が残るのみ）
- prebuild は SOURCE_ROOT 不在（Vercel）で exit 0 して同梱コピーを使う設計。Vercel 上では Vault を読めないため、本番鮮度は「commit された同梱コピー」に依存する点は変わらない（＝定期コミットが前提）
- 今回コミットは news-app の研究ビュー関連のみにスコープ。ai-trend-sns / progress / 未追跡アプリ（anglerlog, birdlog）等の未コミット変更には一切触れていない

## 7. 次にやるべきこと

- Vercel 再デプロイ結果の確認（最新日付が 06-09 になるか）
- Vault→同梱同期を朝の定期実行 or デプロイ前フックに組み込み、再凍結を構造的に防止
- ESLint 設定の追加検討
- 同梱コピーの古い孤立ファイル掃除を同期スクリプトに `--prune` オプションとして追加検討

## 8. ChatGPT レビュー依頼文

```
news-app（Next.js 14 App Router）の市場調査ビューが「Vaultは2026-06-09まで更新済みなのに画面は2026-05-27で停止」していた問題の修正レビューをお願いします。
対象アプリ: news-app（kaeru07/ny01 リポジトリ） / runId: 20260609-204723 / commit: 7328f42

停止箇所の結論:
1) 研究ビュー機能一式（force-dynamic, ステータスバー, RescanButton, vault.tsのライブルート参照, topic/tag/todoページ, structured parser, summary.ts）が前回セッションで実装されたまま未コミットで、Vercelに一度も反映されていなかった。
2) 同梱 content/research（Vault本体を持たないVercel向けのfallbackコピー）が 2026-05-27 で凍結していた。

対応:
- scripts/sync-research-content.mjs を追加し Vault(06_research)→同梱を 06-09 まで同期（55ファイル, 破壊削除なし, dry-run対応）
- package.json に sync:research / prebuild を追加
- 未コミット機能を含めcommit/push

確認してほしい観点:
- 参照先解決の優先順（env > Vault本体 > 同梱コピー）と、Vercel上で同梱コピーに依存する構造の妥当性
- prebuildがVercelでSOURCE_ROOT不在のためexit 0する設計（＝定期コミット前提）で十分か、より堅牢な同期トリガ（cron/Actions）にすべきか
- 同梱コピーに古い孤立ファイルが残る点（--prune未実装）のリスク
- 研究ビュー機能を1コミットにまとめてpushしたスコープ判断の妥当性
```
