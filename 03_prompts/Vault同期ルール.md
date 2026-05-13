# Vault 同期ルール（ob sync 自動反映）

> Claude Code が Obsidian Vault を編集した後、**iPhone Obsidian へ反映するための同期手順**を 1 ページに集約したもの。
> 同じルールは `/root/company/CLAUDE.local.md` の「Obsidian Vault更新時の同期ルール」セクションにも記録している（こちらが Claude Code 側の正本）。

---

## 対象 Vault

- `/root/company/obsidian-sync-vault`

このパス配下の Markdown・テンプレート・添付ファイルを **作成・更新・削除した場合**、作業完了前に必ず同期コマンドを実行する。

---

## 同期コマンド

```bash
cd /root/company/obsidian-sync-vault
source ~/.nvm/nvm.sh && nvm use 22 >/dev/null && ob sync
```

`ob` は Obsidian の CLI ツール。Node v22 で動く前提なので、必ず `nvm use 22` を通してから呼ぶ。

---

## 実行タイミング

Vault 配下のファイルを 1 件でも編集した応答では、以下の順序で必ず通す。

1. Vault 配下の編集をすべて完了させる
2. **`ob sync` を実行**（上記コマンドそのまま）
3. 戻り値・標準出力を確認し、エラーが無いことを確認
4. progress アプリへ POST（`http://localhost:3010/api/execution-runs`）
5. ユーザーへの最終応答を返す

長時間作業中は区切りごとに `ob sync` を呼ぶのが望ましい。最低限、**最終応答の直前 1 回**は必須。

---

## 適用対象

- Vault 配下の `.md` / テンプレート / README / 運用ページ / 添付の**作成・更新・削除**
- ファイル名のリネーム
- フォルダ移動

すべて含む。

---

## スキップしてよい場合

- 質問への回答だけで Vault に変更がない（読んだだけ・調査のみ）
- ユーザーが「同期するな」「ob sync 不要」と明示
- 意図的にステージングしておきたいと明示された場合

---

## 失敗時の扱い

| エラー | 対応 |
|---|---|
| `ob: command not found` | `nvm use 22` が通っていない。コマンドにそのまま含めているので原則発生しないが、出たらユーザーに報告 |
| ネットワーク・認証エラー | 出力をそのままユーザーへ報告。`pm2 restart` 等の自動復旧は行わない。判断はユーザーに渡す |
| 解消できない同期エラー | **Vault 側のファイル状態は元に戻さない**。編集自体は完了させた上で「同期失敗」を明記して報告 |

---

## なぜこのルールがあるか（Why）

- VPS 側で Vault を編集しただけでは iPhone Obsidian には届かない
- `ob sync` がリモート（GitHub or Obsidian Sync）への push トリガになる
- sync を忘れると、iPhone 側は古い状態のまま編集を始めてしまい、**翌日にコンフリクト**として顕在化する
- 過去にも Vault 整備後の sync 忘れで、翌朝の iPhone 確認時に差分が見えなかった事例あり

---

## 関連

- [[Claude-Code標準運用]]
- [[../README]]
- `/root/company/CLAUDE.local.md` の「Obsidian Vault更新時の同期ルール」（Claude Code 側の正本）
