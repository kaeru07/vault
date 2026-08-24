---
date: 2026-08-24
task: 別環境（Windows想定）への移行準備 — 環境の棚卸しとバックアップ
runId: 20260824-234944
targetApp: company-mgmt
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260824-213636]
commitHashes: [eb12398]
reviewFileCommit:
---

## 作業目的

稼働環境（Ubuntu VPS / root 運用）を丸ごと別環境（Windows 想定）へ移すための準備。
**今回は棚卸しのみ**（実行形態は未決定）。VPS は最終的に完全移行して書き込み元を一本化する方針。

## 実施内容

### 棚卸し（すべて実測）

- **常駐**: pm2 6プロセス。**自動実行に必須なのは `progress`(3010) だけ**で、他はアプリのプレビュー。
  `pm2-root.service` で起動時復帰
- **定時実行**: `factory-schedule.timer`（11/14/16/23時）＋ boot service ＋ `hermes-market-research.timer`（毎朝7時）＋ `codex-remote.service`。
  **トリガーは bash だが中身は progress の API を叩くだけ**で、実処理・二重起動防止・記録は全部アプリ側にある。
  → 新環境で必要なのは「決まった時刻に API を叩く仕掛け」だけで、**ロジックの移植は不要**
- **秘密情報**: `/root/.secrets/appstore`（Codemagicトークン・配布証明書秘密鍵）、progress の `.env.local`、
  `/root/.ssh`、`/root/.config/gh`、`/root/.claude`、`/root/.codex`。**値は記録していない**
- **データ**: progress/data 204MB（git管理）、obsidian-vault 638MB（git管理）、
  **obsidian-sync-vault 608MB（git管理外）**、**company直下の管理ファイル 約1.3MB（remoteなし）**
- **リポジトリ**: `kaeru07/` 配下19本。`apps/mahjong` の作業ブランチは `ios-codemagic-test`
- **ランタイム**: Node 22.22.2 / npm 10.9.7 / git 2.43 / gh 2.65 / pm2 6.0.14 /
  **Claude Code 2.1.238** / **Codex CLI 0.149.1** / Python 3.12.3 / Playwright ブラウザ 1.3GB
- **環境依存**: progress 内に `/root/...` の絶対パス直書きが **34箇所**。
  WSL2 なら同じパスを作れば無改修、ネイティブ Windows なら改修対象
- executor は `spawn('claude' / 'codex')`。**`CLAUDE_BIN` / `CODEX_BIN` で差し替え可能**

### バックアップ（git にバックアップが無い2系統）

| 退避物 | サイズ | 状態 |
|---|---|---|
| `_backups/company-mgmt-20260824.tar.gz` | 174KB | 完了 |
| `_backups/sync-vault-20260824.tar.gz` | 約600MB | 作成 |

## 変更ファイル

| ファイル | 変更内容 |
|---|---|
| `progress/docs/operations/environment-migration.md` | 新規。棚卸し＋移行手順＋決めるべき4点 |
| `_backups/*.tar.gz` | git管理外の2系統を退避 |

## 検証結果

- 棚卸しの数値はすべて稼働中の VPS で実測（pm2 jlist / systemctl / ss / du / 各CLIの --version / grep）
- 秘密情報は**ファイル名と鍵名のみ**記載し、値は一切書いていない
- push: `kaeru07/ny01` `eb12398`

## 未対応

- **実行形態が未決定**（WSL2 / ネイティブ Windows / 両対応）。決まるまで絶対パスの env 化などの改修はしていない
- `/root/company` に git remote が無い。管理ファイル群は tar 退避しただけで、**GitHub 上のバックアップは無いまま**
- `netscope` / `hack-lab` は `/root/company` の外（`/root/map`・`/root/hack`）にあり、移すなら別途コピーが必要

## 危険ポイント

- **稼働 Vault（608MB）と company 管理ファイルは、これまで GitHub バックアップが無かった**。VPS が飛べば失われる状態だった。
  今回の tar は同じディスク上なので、**別媒体か private リポジトリへの退避が別途必要**
- 移行後に **VPS と新環境の両方で自動実行すると、progress のデータ（git管理）が競合**する。
  切り替え時は VPS のタイマー停止（`systemctl disable --now factory-schedule.timer …`）を必ず行う
- Windows は電源が切れている時間がある。**23時の定時実行が落ちる可能性**があり、VPS の24時間稼働とは前提が変わる
- 現在 progress は VPS のポート3010＋Basic認証で iPhone から見ている。移行後の**外部からの見え方は再設計が必要**

## 次にやるべきこと

1. **実行形態を決める**（WSL2 なら数時間で移行完了、ネイティブ Windows なら絶対パス34箇所などの改修が要る）
2. `/root/company` の private リポジトリを作って push（管理ファイルの冗長化）
3. tar 退避を別媒体へコピー
4. 形態決定後: 新環境で API を叩く仕掛けを作り、VPS 側タイマーを停止して一本化

## ChatGPT レビュー依頼文

```
対象: kaeru07/ny01（main, commit eb12398）/ progress/docs/operations/environment-migration.md
runId: 20260824-234944

Ubuntu VPS で動いている個人開発の自動実行環境（Next.jsアプリ + systemdタイマー +
Claude Code / Codex CLI を executor として spawn）を、Windows 機へ移す準備として
棚卸しをしました。以下の観点でレビューしてください。

1. 抜けている持ち物はないか
   - 常駐 / 定時実行 / 秘密情報 / データ / リポジトリ / ランタイム / 外部サービス /
     絶対パス依存、で分類しています。個人開発環境の移行で見落としがちな項目はありますか。

2. 実行形態の選択
   - WSL2（今の構成をほぼそのまま移せる）と ネイティブWindows（絶対パス34箇所の
     env化・bash→PowerShell・pm2の常駐化・タスクスケジューラ）で迷っています。
   - 「自動実行を24時間回す」用途で、Windows機（電源が落ちる時間がある）に移すこと自体の
     是非も含めて意見をください。

3. データ競合
   - progress の実行履歴・目標データは git 管理（public リポジトリ）です。
     移行期間中に両環境で動かすと競合します。安全な切り替え手順はどうあるべきですか。

4. バックアップ
   - 稼働Obsidian Vault（608MB）と管理ファイル群が、これまで GitHub にバックアップされて
     いませんでした。同一ディスク上の tar 以外に、個人環境で現実的な冗長化案はありますか。
```
