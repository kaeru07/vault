---
date: 2026-06-07
task: 運用ルール更新 — 前進優先ルール（承認待ちで停止しない）＋前タスクのGitHub反映完了
runId: 20260607-160250
targetApp: company-meta
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260607-141704]
commitHashes: [804d12e, 1b98f02]   # 新ルール適用で push した前タスク(/yomi)の commit
# reviewFileCommit:
---

# 2026-06-07 前進優先ルール（承認待ちで停止しない）

> 1 作業 = 1 ファイル の自動生成レビュー。

---

## 1. 作業目的

- なぜこの作業をするのか: ユーザーから「今後の作業ルール: ユーザー承認待ちで停止しない」と明確な指示があったため恒久ルール化。
- 背景: 旧ルールでは「push/deploy はユーザー明示指示まで保留」だったため、検証済みの作業が「push 待ち」で滞留していた（前タスク /yomi 品質改善が未 push のまま）。
- 期待効果: 承認必須を 6 点に限定し、それ以外は実装→検証→GitHub 反映まで自走。停止ゼロと安全性を両立。

---

## 2. 実施内容

- 主な変更:
  - `CLAUDE.local.md` に §「前進優先ルール（2026-06-07）」を追記。**承認必須6点**＝①本番データ削除 ②DBスキーマ破壊変更 ③外部課金発生 ④認証/権限変更 ⑤不可逆操作(force push/履歴改変/復元不能削除) ⑥高セキュリティリスク変更。それ以外（通常の commit & push を含む）は事後報告で自動実施。
  - 旧「Claude Code 実行ルール（2026-06-05）」の push 保留、および § commit 後 push 必須化／§ GitHub 反映ルールの「push 待ち保留」を**上書き**すると明記。機密スキャン・push 前後チェックは継続必須。
  - メモリ `feedback-execution-confirmation-policy` に 2026-06-07 更新節を同期。
- 関連判断:
  - 反映先は「メモリのみ」か「CLAUDE.local.md＋メモリ」か → 後者。CLAUDE.local.md が運用の正本でハーネスに効くため。
  - 新ルールを即適用し、前タスク（/yomi 品質改善 runId 20260607-141704）の未 push を解消。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| CLAUDE.local.md | §前進優先ルール(2026-06-07)追記・旧push保留を上書き宣言 |
| ~/.claude/.../memory/feedback_execution_confirmation_policy.md | 2026-06-07更新節を追記（push保留撤回・承認必須6点・事後4点報告） |
| (適用) apps/mahjong | 前タスクの未pushを2コミットに分割しpush（804d12e 卓レイアウト / 1b98f02 品質改善） |

---

## 4. 検証結果

- typecheck / build / lint: n/a（ルール文書とメモリの更新。コード変更は前タスクで検証済み）
- 手動確認: mahjong push 前後チェック実施 → branch=main / remote=origin(kaeru07/mahjong) / secret scan 機密パターン無し / push 成功 582fdd7..1b98f02 (FF) / local=origin=1b98f02 / 作業ツリー clean。
- 機密パターン事前チェック: OK（ルール例示のみ・実値なし）
- ob sync: Fully synced
- git push: mahjong 1b98f02（pushed）／ obsidian-vault ミラーは本セッションで mirror+push 実施

---

## 5. 未対応

- なし（CLAUDE.local.md は company 管理下の非 git 私的ファイルのため GitHub 反映対象外。メモリも ~/.claude 配下で git 管理外）。

---

## 6. 危険ポイント

- 影響範囲: 運用ルールの恒久変更。今後 Claude Code は承認必須6点以外で確認を取らず push まで自走する。
- リスク緩和: 機密スキャン・push 前後チェックリストは存続。force push / 履歴改変 / 本番デプロイ（課金・不可逆該当時）は引き続き承認必須。
- ロールバック手段: CLAUDE.local.md の当該節を削除すれば旧運用（push 保留）に戻せる。

---

## 7. 次にやるべきこと

- フォローアップ: 以降の全作業を前進優先ルールで運用。
- 観察項目: 自走 push が増えることでの事故有無（数週間）。
- 関連: [[2026-06-07_mahjong-yomi-quality-improve]]（本ルール適用で push 完了した前タスク）。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象: company-meta（運用ルール変更）
作業: 前進優先ルール（承認待ちで停止しない）の恒久化＋前タスクのGitHub反映完了
runId: 20260607-160250
日付: 2026-06-07
関連commit: mahjong 804d12e / 1b98f02

## 変更内容
- CLAUDE.local.md に「前進優先ルール(2026-06-07)」追記。承認必須を6点に限定し通常push自動実施。旧push保留を上書き。
- メモリ feedback-execution-confirmation-policy を同期。
- 新ルール適用で前タスク(/yomi品質改善)をmahjong mainへpush(1b98f02)。

## 確認したい観点
- 承認必須6点の線引きは妥当か（特に「通常push自動実施／本番デプロイは6点該当時のみ確認」の切り分け）
- 機密スキャン・push前後チェックを残した安全策で十分か
- 旧ルール(push保留)の上書き宣言に漏れ・矛盾がないか
````

---

## 関連

- progress runId: 20260607-160250（正本）
- 関連 run: 20260607-141704
- 関連レビュー: [[2026-06-07_mahjong-yomi-quality-improve]]
- 関連プロンプト: [[../03_prompts/Claude-Code標準運用]]
- フォルダ運用: [[../20_reviews/README]]
