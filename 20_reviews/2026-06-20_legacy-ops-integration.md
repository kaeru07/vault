---
date: 2026-06-20
task: 旧Vault運用と現ゴール運用の統合点検＋段階別統合ゴール6件を承認へ追加
runId: 20260620-144248
targetApp: progress
monetizationImpact: medium
theme: [workflow, app-strategy, obsidian, monetization]
relatedRunIds: []
commitHashes: []
---

# 2026-06-20 旧Vault運用と現ゴール運用の統合点検

## 1. 作業目的
- ユーザー要望: 旧運用のvault等を再点検し、最新のゴール運用へ統合したい。整理して段階に分けてゴール承認へ追加。点検自体を承認に追加してもよいし、今点検しきってもよい。
- → 点検は今回やり切り、結果から段階別の統合ゴールを承認へ追加した。

## 2. 実施内容
- 点検: 旧Vault運用（`05_monetization/`のcandidate-001〜007・Epic A-D・収益化ロードマップ ＋ `20_reviews/`のChatGPT承認待ち・vloop_queue・案件別ToDo一覧・案件別ゴール進捗・完了ToDoログ ＋ vloopスキル ＋ GitHub Issue ＋ ChatGPT承認）と、現progressゴール運用（Goal/ゴール承認/auto-queue/factory-runner/ExecutionRun）が**並行2系統**であることを確認。旧側は〜2026-05-25(vloop11)で停滞気味。
- 概念対応表を作成し、正本寄せ先を確定（実行/キュー/ToDo/進捗/完了履歴=progress、Vault=判断ログ/ナレッジ/ChatGPTレビュー入口）。
- 点検doc新規: `06_research/2026-06-20_旧Vault運用と現ゴール運用の統合点検.md`。
- 段階別統合ゴール6件をゴール承認（アプリ系）へ追加: P1対応表確定 / P2 candidate→progress Goal移行 / P3 ToDo・キュー一本化 / P4 承認フロー一本化 / P5 vloop位置づけ確定 / P6 旧ドキュメントのアーカイブ・索引現行化。各enables/pros/cons付き。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| obsidian-sync-vault/06_research/2026-06-20_旧Vault運用と現ゴール運用の統合点検.md | 新規: 対応表・正本方針・段階別統合計画P1〜P6 |

（データ操作: ゴール承認へ統合ゴール6件をproposed/app_improvementで追加）

## 4. 検証結果
- /api/goals/propose created=6 / skipped=0
- /decide?tab=goalApproval 200で統合P1〜P6が アプリ系に描画。proposed=43（試した方がいい系16＋アプリ系27）。
- 機密スキャン: OK / ob sync: n/a / git push: vault反映

## 5. 未対応
- P1〜P6の実移行は承認後に各ゴールとして着手。今回は点検と計画のゴール化まで。

## 6. 危険ポイント
- 各Pは非破壊（移行→リンク化→アーカイブの順）。Vaultフォルダ削除・大量リネームは承認必須・バックアップ前提。収益化の公開可否/撤退は承認必須。

## 7. 次にやるべきこと
- P1（対応表確定）を承認して着手。
- 収益化候補candidateの移行可否・取捨選択をユーザー判断。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress / Vault運用
作業: 旧Vault運用と現ゴール運用の統合点検＋段階別統合ゴールP1〜P6を承認へ追加
runId: 20260620-144248

## 点検結果
旧Vault運用(candidate/vloop_queue/Epic A-D/ChatGPT承認待ち/案件別ToDo・進捗/
完了ToDoログ)と現progressゴール運用(Goal/ゴール承認/auto-queue/ExecutionRun)が
並行2系統。実行/キュー/ToDo/進捗/完了はprogressを正本、Vaultは判断ログ/レビュー
入口に役割限定する方針。

## 追加した統合ゴール(段階別)
P1対応表確定 / P2 candidate→Goal移行 / P3 ToDo・キュー一本化 / P4 承認フロー一本化 /
P5 vloop位置づけ確定 / P6 旧ドキュメントのアーカイブ・索引現行化。

## 確認したい観点
1. 正本をprogressに寄せる方針は妥当か(Vaultに残すべき役割の線引き)。
2. P1〜P6の順序・粒度は適切か。先にやるべきPは。
3. vloop(P5)は廃止とprogress連携どちらが筋が良いか。
4. 収益化candidate(P2)の移行で、捨てるべき/活かすべき候補の判断軸。
````
