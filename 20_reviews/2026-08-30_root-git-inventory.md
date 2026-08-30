---
date: 2026-08-30
task: /root 直下まで含めた git 棚卸し — 未管理4本の登録と未コミット作業の保全
runId: 20260830-174413
targetApp: company-mgmt
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260829-142435]
commitHashes: []
reviewFileCommit:
---

## 作業目的

「疑わしいものは全部リポジトリ作成して登録」の指示を受け、前回 `company` 配下で終えた棚卸しを
**`/root` 直下まで広げて**やり直す。VPS が飛んだら失われるものを無くす。

## 棚卸しの結果

`/root` 直下11ディレクトリのうち **9つが git 管理外**、さらに管理下の2つに**未コミット48件**があった。

| 分類 | 件数 | 対応 |
|---|---|---|
| 実体があり未管理 | 4 | **private リポジトリ新設** |
| 既存リポジトリの clone のみ | 3 | 登録不要（実体は GitHub にある） |
| 完全に空 | 3 | 登録不要 |
| 未コミット作業 | 48件 | **salvage ブランチで保全** |

## 実施内容

### 1. 新規登録した4本（すべて private）

| ローカル | GitHub | 発見内容 |
|---|---|---|
| `/root/company-helper` | `kaeru07/company-helper` | Claude Code の作業ループ管理アプリ。**app-index に「GitHubなし・active」と書かれたまま放置**（49ファイル） |
| `/root/hack` | `kaeru07/hack-lab` | 疑似攻撃・防御ラボ。**pm2 で port 3002 に常駐しているのに管理外だった**（54ファイル） |
| `/root/docker` | `kaeru07/docker-envs` | Docker環境の定義（36ファイル） |
| `/root/docker/mahjong-quiz` | `kaeru07/mahjong-quiz-docker` | 麻雀クイズ。**remote が無く、ここが唯一の実体**だった（30ファイル） |

全て機密スキャンで実値なしを確認し、`node_modules` / `.env` / `.venv` を除外してから登録。

### 2. 埋め込みリポジトリの整理

親リポジトリの中に別リポジトリがあり、**clone しても中身が付いてこない**状態だった。

- `hack/memo` → 既に `kaeru07/memo` として管理済みのため `.gitignore` で除外
- `docker/mahjong-quiz` → 独立リポジトリ化してから除外

### 3. 未コミット作業の保全（main には入れない）

`/root` 側の clone に、`company/apps` の正本とは**異なる実装**が残っていた。破棄せず
`salvage/root-clone-20260830` ブランチとして push した。

| リポジトリ | 保全した内容 |
|---|---|
| `kaeru07/mahjong` | `BoardView.tsx`（+57 -34・正本とは別実装）、`AGENTS.md`、`CLAUDE.md` |
| `kaeru07/map` | 分析API新規、`prisma/schema.prisma` +64行、`migrate_v3/v4.sql`、取込スクリプト（13ファイル +2,046行） |
| `kaeru07/dashboard` | `GanttWidget` 新規、Xフィード周りの改修（16ファイル +580行） |

コミット前に `map` の `data/`（パケット実データ 1.9MB）と、`my-dashboard` の `ny01/`
（**誤って置かれた ny01 の clone・2.9GB**）を `.gitignore` で除外した。

### 4. 管理外のまま残したもの（理由つきで台帳に記録）

- `card` / `invest` / `shogi` — **完全に空**（ファイル0件）
- `/root/mahjong` 直下の素材（demo.zip・スクショ 10MB）— 本体 `mahjong/mahjong` は clone
- `/root/scrape` 直下 — 中身が自分と同名の空ファイルだけ
- `/root/street6` — 中身は `ai-dev-clone` の clone のみ

## 検証結果

- 新規4本すべて機密スキャン（`sk-` / `gho_` / 秘密鍵 / `SMTP_PASS` / `API_KEY`）で**実値なし**
- 登録後の状態: `/root` 直下の git 管理下ディレクトリ**すべて未コミット0件**
- `nanikiru-shorts` は前回登録済みであることを再確認（94ファイル・push済・追跡設定あり）

## 未対応

- **salvage ブランチ3本は main に入れていない**。正本へ取り込むか破棄するかは中身を見て判断が必要
- `/root/my-dashboard/ny01`（誤配置の clone・**2.9GB**）は `.gitignore` で除外しただけで実体は残っている
- `/root/mahjong` のデモ素材10MB は GitHub に無い（再取得可能と判断）

## 危険ポイント

- **pm2 で常駐しているのに git 管理外**のものが2本目（前回 `kusoge-close-button`、今回 `hack`）。
  稼働と管理のズレが繰り返し起きている。pm2 のプロセス一覧と git 管理を突き合わせる仕組みが要る
- **埋め込みリポジトリは「push したつもりで中身が無い」事故になる**。今回2件見つかった
- `/root` と `/root/company/apps` に同じリポジトリの clone が二重に存在し、
  **どちらでも作業できてしまう**状態。移行後は片方に寄せるべき

## 次にやるべきこと

1. salvage ブランチ3本の内容を確認し、正本へ取り込むか破棄するか決める
2. `/root/my-dashboard/ny01`（2.9GB）を削除するか判断する
3. `/root` 側の重複 clone を整理する（移行のタイミングが好機）

## ChatGPT レビュー依頼文

```
対象: kaeru07 配下の全リポジトリ（/root 直下まで含む）
runId: 20260830-174413

個人開発環境の git 棚卸しを /root 直下まで広げ、未管理4本を private 登録、
正本と異なる未コミット実装3件を salvage ブランチで保全しました。
以下の観点でレビューしてください。

1. 稼働と管理のズレ
   - pm2 で常駐しているのに git 管理外のアプリが2本見つかりました（別々の棚卸しで1本ずつ）。
     プロセス一覧と git 管理を突き合わせて検出する、個人開発で現実的な方法はありますか。

2. 未コミット作業の扱い
   - 正本と異なる実装が別ディレクトリの clone に残っていました。破棄せず
     salvage ブランチへ push しましたが、この扱いは妥当ですか。
     取り込む/捨てるの判断基準はどう置くべきですか。

3. 埋め込みリポジトリ
   - 親リポジトリ内に別リポジトリがあり「push したつもりで中身が無い」状態が2件ありました。
     submodule 化と .gitignore 除外、どちらを既定にすべきですか。

4. 重複 clone
   - 同じリポジトリが /root と /root/company/apps の2箇所に clone されており、
     どちらでも作業できてしまいます。環境移行を機に片方へ寄せる方針で良いですか。
```
