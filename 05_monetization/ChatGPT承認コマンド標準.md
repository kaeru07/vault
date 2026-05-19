---
title: ChatGPT 承認コマンド標準
type: monetization-design
issue: kaeru07/vault#24
created: 2026-05-19
updated: 2026-05-19
status: active
---

# ChatGPT 承認コマンド標準

> Issue #24。専用画面なしで **ChatGPT 上の短い1行返答だけで方向性承認**できるようコマンドを標準化。
> [[ChatGPT承認ゲート標準]] の運用詳細。**承認＝方向性判断は ChatGPT。status 書換は人間。Claude/vloop はコマンド結果を読んで次工程へ。**

## コマンド書式

```
<scenarioId> <action> [: 理由]
```

- 例: `candidate-001 approve`
- 例: `candidate-001 hold: 収益根拠が弱いので追加調査`
- 例: `candidate-001 reject: 競合過多`
- 1行1コマンド。複数候補は複数行可
- scenarioId は `candidate-XXX`（または明示シナリオ名）

## アクション定義と次工程

| コマンド | 意味 | 直後の次工程（誰が） |
|---|---|---|
| `approve` | 方向性承認 | ① 人間が シナリオ `status:` を approved・`approvedBy:` 記入 ② Claude/vloop が [[scenarios/candidate-001_progress投入設計]] の最初の3ToDo を progress 投入準備（投入は人間）③ decision-log 追記 |
| `hold` | 保留（追加調査/比較が必要） | ① status は pending_approval/hold 据え置き ② Claude/vloop が hold 理由に沿った追加調査 ToDo を作成（approved 化しない）③ decision-log 追記 |
| `reject` | 却下 | ① 人間が status: rejected ② Claude が [[idea-graveyard]]（着手前）/ [[../20_reviews/失敗レビュー]]（着手後）へ墓碑1ブロック ③ decision-log 追記 |

> **Claude/vloop は approve コマンドを見ても自分で status を approved に書き換えない**（人間のみ）。
> Claude がやるのは「approve なら投入準備・hold なら追加調査・reject なら墓場記録」までの**次工程の手当て**。

## 承認ログ記録形式（decision-log）

ChatGPT コマンド受領後、[[decision-log]] 履歴テーブル先頭へ1行（人間が status 確定時に確定記入。Claude は受領記録まで）:

```
| YYYY-MM-DD | candidate-XXX | <遷移> | ChatGPT指示+人間確定(役割) | <理由> | <コマンド原文 / 関連ファイル> |
```

- 「コマンド原文」を理由欄か末尾に残し、いつ何のコマンドで動いたか追跡可能にする
- approve/reject の status 確定は人間。hold は AI が追加調査着手まで可

## ChatGPT レビュー時の必須出力（vloop/Claude 側）

承認待ち候補を提示する時は**必ずコマンド例を併記**する。例:

```
承認候補: candidate-001（麻雀何切るアプリ公開 / high / 28点）
コマンド: `candidate-001 approve` / `candidate-001 hold: <理由>` / `candidate-001 reject: <理由>`
```

- [[../20_reviews/ChatGPT承認待ち]] / [[../20_reviews/candidate-001_ChatGPT承認パック]] の判断欄にコマンド例を常設
- [[../90_templates/chatgpt-approval-template]] の人間判断欄にコマンド行を含める

## vloop/Claude の挙動（コマンド結果を見て動く）

- vloop は各サイクルの pull 後、承認待ちファイルの判断欄に **コマンド結果が記入済か**を確認
- `approve` 記入＋人間が status: approved 済 → 次サイクルで progress 投入設計の ToDo を実作業対象化
- 記入なし/pending → 従来どおり判断材料作成まで（approved 化しない）
- 本標準は [[../03_prompts/claude-commands/vloop]] の確認禁止・承認ゲート相互参照と整合（挙動の明文化）

## 未対応点 / 仮説
- コマンド受領の「正本」は GitHub（承認待ちファイルの判断欄 or Issue コメント）とする仮説。ChatGPT チャットのみの口頭承認は decision-log/ファイルに転記されるまで未確定扱い
- 既存テンプレ（#19）・decision-log（#8）の様式は壊さず**コマンド行を追記**する非破壊方針

## 次の一手
1. ChatGPT が `candidate-001 approve|hold|reject` を承認待ちファイル判断欄 or Issue#22/#23 コメントで発行
2. approve なら人間が status 確定 → vloop が progress 投入設計の ToDo1（B1 build）を実作業化
3. 受領を decision-log へ記録

## 関連
- [[ChatGPT承認ゲート標準]] / [[decision-log]] / [[../20_reviews/ChatGPT承認待ち]] / [[../20_reviews/candidate-001_ChatGPT承認パック]]
- [[../90_templates/chatgpt-approval-template]] / [[../03_prompts/claude-commands/vloop]]
- Issue: kaeru07/vault#24（関連 #18 / #19 / #22 / #23）
