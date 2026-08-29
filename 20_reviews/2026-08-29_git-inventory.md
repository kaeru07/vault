---
date: 2026-08-29
task: git 管理の全体棚卸し — 未管理アプリの登録と未コミット57件の解消
runId: 20260829-142435
targetApp: company-mgmt
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260825-005750]
commitHashes: [fa66443, 69db1ee]
reviewFileCommit:
---

## 作業目的

環境移行の準備として、**git に登録すべきなのにされていないもの**を全部洗い出して片付ける。
VPS が飛んだら失われる状態を無くす。

## 棚卸しの結果

### 判明した穴

| 分類 | 件数 | 内容 |
|---|---|---|
| **git管理外のアプリ** | **5** | anglerlog / kusoge-close-button / nanikiru-shorts / neon-slash / generated |
| **未コミット** | **57** | map 12 / ny01 33 / shogi-kakoi-trainer 4 / note 3 / ny-wk 3 / memo 1 / ny-ai 1 |

company 直下は前回（2026-08-25）の `kaeru07/company` 新設で網羅済みだった。

## 実施内容

### 1. 未管理アプリ4本を private リポジトリへ登録

| アプリ | 新リポジトリ | 内容 |
|---|---|---|
| kusoge-close-button | `kaeru07/kusoge-close-button` | 閉ボタン選手権。**pm2 で port 3001 に常駐稼働中なのに未管理だった** |
| neon-slash | `kaeru07/neon-slash` | アクションゲーム（約2,040行） |
| nanikiru-shorts | `kaeru07/nanikiru-shorts` | 何切るショート動画の生成テンプレ（Remotion・1080x1920 MP4） |
| anglerlog（素HTML版） | `kaeru07/anglerlog-proto` | 釣果ログの旧試作 |

### 2. `generated/` は意図的に管理外にした

AI工場の中間生成物（Expo試作2本）。**方針は既に不採用が確定済み**で正本と重複するため、
git には入れず `README.md` で来歴だけ残した（company の `.gitignore` で README のみ追跡）。

### 3. 未コミット57件の解消

- **ny01**: 自動実行が実装したまま放置されていた**「撮影シナリオ」機能**（`app-review-screenshots.ts` +146行）が
  含まれていた。tsc / 209テスト / build で検証してから取り込み。
  他に mahjong-trainer の卓UI、`validate-research-format.mjs`、anglerlog の Next.js 版
- **note**: リモート（Phase2実装・`@sparticuz/chromium` 追加）とローカル（next のセキュリティ更新 14.2.3→14.2.35）が競合。
  **どちらも捨てずに両方の依存を残す形で rebase 解決**
- **shogi-kakoi-trainer**: Android(Capacitor) プロジェクト53ファイル。ビルド生成物は除外、**署名鍵の混入なしを確認**
- **ny-wk**: ファイルが `shogi/` へ移動されていた状態（内容は同一）をコミット
- **map / memo / ny-ai**: UI更新 / `.venv` 除外 / package-lock 追加

## 検証結果

- 全アプリで機密スキャン（`sk-` / `gho_` / `BEGIN PRIVATE KEY` / password 実値）を実施し、**混入なし**
- ny01 の取り込み分は tsc OK / **209テスト全pass** / build exit 0
- **最終状態: apps 配下21ディレクトリすべて git 管理下**（generated のみ意図的除外）、**未コミット0件**
- company / obsidian-vault / vault-sync-backup も0件
- `backup-to-github.sh` を実行して最新化（Vault 2,983ファイル）

## 未対応

- **anglerlog が2本ある**（`apps/anglerlog`=素HTML版 2026-06-05 / `apps/ny01/anglerlog`=Next.js版 2026-06-06）。
  どちらを正本にするか未判断。両方 git には入れた
- `apps/generated` は管理外のまま（正本と重複するため意図的）

## 危険ポイント

- **nanikiru-shorts の初回コミットで `.gitignore` が無く、node_modules 564MB を巻き込んだ**。
  untrack → `reflog expire` → `gc` で 7.9MB へ復旧してから push した。
  **新規リポジトリを作るときは `.gitignore` を先に置くこと**
- `kusoge-close-button` は**常駐稼働しているのに git 管理外**だった。pm2 で動いているものと
  git 管理の対象がずれていないか、定期的に突き合わせる必要がある
- note の rebase で依存を手で統合した。`npm ci` が通るかは未検証（`package-lock.json` は
  リモート側のものを採用しているため、次回の依存更新時にズレが出る可能性）

## 次にやるべきこと

1. **anglerlog の正本をどちらにするか決める**（素HTML版 / Next.js版）
2. note で `npm ci && npm run build` を通して依存の統合が正しいか確認する
3. 環境移行の実行形態（WSL2 / ネイティブ Windows）を決めてフェーズ1へ進む

## ChatGPT レビュー依頼文

```
対象: kaeru07 配下の全リポジトリ（21アプリ + company + vault）
runId: 20260829-142435

個人開発環境の git 管理を全体棚卸しし、管理外だったアプリ4本を private リポジトリへ
登録、未コミット57件を解消しました。以下の観点でレビューしてください。

1. 管理する / しないの線引き
   - AI工場の中間生成物（apps/generated）は正本と重複するため git 管理せず、
     README で来歴だけ残しました。この判断は妥当ですか。
   - 常駐稼働しているのに管理外だったアプリ（pm2 の kusoge-close-button）がありました。
     「動いているもの」と「git 管理」のズレを継続的に検出する仕組みは何が現実的ですか。

2. 依存の競合解決
   - note でリモート（新機能の依存追加）とローカル（セキュリティ更新）が競合し、
     両方の依存を残す形で package.json を手で統合しました（package-lock はリモート側）。
     この解決方法のリスクと、正しい手順を教えてください。

3. 二重管理
   - anglerlog が素HTML版とNext.js版で2本あります。どちらも git に入れましたが、
     正本を決めるまで放置するリスクをどう見ますか。

4. 再発防止
   - 新規リポジトリ作成時に .gitignore を置き忘れ、node_modules 564MB を
     巻き込みました（復旧済み）。個人開発で現実的な予防策はありますか。
```
