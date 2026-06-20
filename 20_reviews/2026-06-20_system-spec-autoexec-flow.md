---
date: 2026-06-20
task: システム仕様ページを自動実行フロー中心に再構成＋調査/ゴール生成モードを明記（iPhone向け折りたたみ）
runId: 20260620-092650
targetApp: progress
monetizationImpact: low
theme: [workflow, obsidian]
relatedRunIds: [20260620-073504]
commitHashes: [b4cf4ca]
---

# 2026-06-20 システム仕様ページを自動実行フロー中心に再構成

> 1 作業 = 1 ファイルの自動生成レビュー。

---

## 1. 作業目的

- なぜこの作業をするのか: ユーザー要望 — (1) 自動実行時に行われている「調査」もシステム仕様としてまとめる、(2) システム仕様ページが縦長でまとまっておらず分かりづらい・iPhoneで見ることを意識して縦長にしすぎない、(3) 一番知りたいのは「自動実行時に行われる処理のフロー」なのでその方針でまとめ直す。
- 背景: 旧 `/guide?tab=system` は16セクションが全部縦積み（344行）で、肝心の自動実行フローが埋もれ、かつ「調査からのゴール提案」「アイドル時のゴール生成モード」が仕様として書かれていなかった。
- 期待効果: iPhoneで開いて最初に「自動実行で何が起きるか」が分かり、詳細はタップで開ける。調査とゴール生成モードが正式に仕様化される。

---

## 2. 実施内容

- `components/operations/SystemSpecification.tsx` を全面再構成:
  - (A) ヘッダ（要点＋Factory状態メトリクス）
  - (B) ★主役「自動実行で行われる処理（11/14/16/23時）」を先頭固定。全体像（折返しフロー）＋12ステップの番号リスト。各ステップに「調査/ゴール生成/安全/実行」のタグ。② に調査、③-b にゴール生成モードを明示。
  - (C) 参照系（どこで止まるか/Runner・Executor/キュー並び順/状態モデル/正本データ/API/画面/用語/設計原則・互換・変更時チェック）を `<details>` アコーディオンで**既定折りたたみ**→iPhone初期スクロールを大幅短縮。
- 実フローを runScheduledFactory→runFactory の実装どおりに正確化（lock→ON確認→危険判断スキップ→①Review Fix→②調査ゴール提案→③Epic選定→③-a次の一歩→③-bゴール生成モード→④Claude実行(上限→Codex)→⑤検証+ExecutionRun→⑥doneCriteria→⑦Prompt Queue→⑧Envelope Run）。
- `app/guide/page.tsx`: systemタブの説明をフロー中心に更新。FAQに「自動実行する作業が無くなったらどうなる?」（ゴール生成モード）を追加。
- `lib/command-center.ts`: TERMS.proposedGoal を「調査（外）/ゴール生成モード（中）」の2系統補充に更新。
- `docs/operations/current-operating-model.md`: frontmatter updated/updateNote 更新、パイプライン節に定時実処理順・②調査ゴール提案・③-bゴール生成モードを追記。
- CLAUDE.local「progress 運用ドキュメント セット更新ルール」4点（運用ページ/TERMS/図/operating-model）をセットで更新。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| progress/components/operations/SystemSpecification.tsx | 全面再構成。自動実行フローを主役に先頭固定、参照系をアコーディオン折りたたみ、調査/ゴール生成モードを明記 |
| progress/app/guide/page.tsx | systemタブ説明をフロー中心に更新、FAQにゴール生成モード項目追加 |
| progress/lib/command-center.ts | TERMS.proposedGoalを調査/ゴール生成モード2系統に更新 |
| progress/docs/operations/current-operating-model.md | frontmatter更新＋パイプライン節に実処理順/調査提案/ゴール生成モードを追記 |

---

## 4. 検証結果

- typecheck: OK（tsc --noEmit exit 0）
- build: OK（npm run build exit 0）
- lint: OK（next lint 変更4ファイル 0）
- 手動確認: pm2 restart 後に実描画確認。/guide?tab=system 200・新フロー見出し/「調査からゴールを提案」/「ゴール生成モード」/折りたたみ見出し（どこで止まるか等）描画・旧16セクション(0件)。/guide・/guide?tab=report も200で回帰なし。guide FAQ新項目描画確認。
- 機密パターン事前チェック: OK（コード・ドキュメントのみ・秘密情報なし）
- ob sync: n/a（ob sync 一旦中止中）
- git push: b4cf4ca / pushed（origin/main）

---

## 5. 未対応

- iPhone実機での折りたたみ操作感・文字折返しの最終確認（curl＋サーバ描画で代替）
- 全体像フローの折返しがiPhone幅で2〜3段に綺麗に収まるかの実機確認

---

## 6. 危険ポイント

- 既存機能への影響リスク: 低。表示専用ページの再構成のみ。ロジック・データ書き込みは変更なし。
- DB / 認証 / 本番 / 機密: 触れていない。
- ロールバック手段: commit b4cf4ca を revert すれば旧16セクション版に戻る。
- 観察項目: アコーディオンの開閉がiPhone Safariで問題なく動くか。

---

## 7. 次にやるべきこと

- iPhone実機でアコーディオン開閉・縦長感を確認
- 必要なら主役フローにアンカー/TOCを追加
- ゴール生成モード本体（runId 20260620-073504）の proposed枠<3での実機候補生成確認（前タスクの宿題）

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の作業報告です。レビューしてください。

対象アプリ: progress（AI工場の司令塔・ポート3010）
作業: システム仕様ページを自動実行フロー中心に再構成＋調査/ゴール生成モードを明記
runId: 20260620-092650
日付: 2026-06-20
GitHub commit: b4cf4ca（kaeru07/ny01 main）

## 作業目的
ユーザー要望(1)自動実行時の調査もシステム仕様にまとめる (2)システム仕様ページが
縦長で分かりづらい→iPhone向けに縦長にしすぎない (3)一番知りたいのは自動実行の
処理フロー→その方針でまとめ直す。

## 実施内容
- SystemSpecification.tsxを全面再構成。自動実行フロー(11/14/16/23時)を全体像+
  12ステップ番号リストで先頭固定(調査②/ゴール生成③-bをタグで明示)。参照系
  (安全条件/Runner/データ/API/画面/用語/設計原則)をdetailsアコーディオンで
  既定折りたたみ→iPhone初期スクロール短縮。
- 実フローをrunScheduledFactory→runFactory実装どおり正確化。
- guide systemタブ説明更新+FAQ追加、TERMS更新、operating-model.md更新
  (CLAUDE運用ドキュメント4点セット更新)。

## 変更ファイル
- SystemSpecification.tsx(再構成) / guide/page.tsx / command-center.ts /
  current-operating-model.md

## 検証結果
- tsc / build / lint: すべてOK
- 実描画: pm2 restart後 /guide?tab=system 200・新フロー/調査/ゴール生成モード/
  折りたたみ描画・旧16セクション削除確認。他タブ200回帰なし。
- 機密スキャン: OK

## 未対応
- iPhone実機での折りたたみ操作感・折返しの最終確認

## 危険ポイント
- 影響低(表示専用ページの再構成のみ・ロジック変更なし)。

## 確認したい観点
1. 自動実行フローの12ステップは、ユーザーが「何が起きるか」を掴むのに過不足ないか。
2. 参照系をアコーディオン既定折りたたみにする方針は、iPhone閲覧として妥当か
   (重要情報が隠れすぎないか)。
3. 調査(外)/ゴール生成モード(中)の2系統補充という整理は分かりやすいか。
````
