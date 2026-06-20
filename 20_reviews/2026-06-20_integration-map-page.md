---
date: 2026-06-20
task: 旧Vault→今のゴール運用 対応表ページ(一時)をprogressアプリに追加
runId: 20260620-181129
targetApp: progress
monetizationImpact: low
theme: [workflow, app-strategy]
relatedRunIds: [20260620-144248, 20260620-163521]
commitHashes: [5bd5d69]
---

# 2026-06-20 旧Vault→今のゴール運用 対応表ページ（一時）

## 1. 作業目的
- ユーザー指示「対応表作って、一時的にprogressアプリから見れるようにして」。統合ゴール①の成果物。

## 2. 実施内容
- `app/integration-map/page.tsx` 新設（server component）:
  - 一時ページ説明 / 対応表10行（旧Vault→今のprogress→所見）/ 正本の寄せ先（実行・キュー・ToDo・進捗・完了=progress、Vault=判断ログ・レビュー入口）
  - 統合の進め方①〜⑥を goals.json とタイトル先頭の丸数字で照合し、状態バッジ（承認待ち/実行中/後で/完了）を動的表示
  - ゴール承認へのリンク＋承認待ち件数（試した方がいい系/アプリ系）
  - iPhone向けに横スクロールテーブル＋簡潔レイアウト
- `lib/nav-menu.ts`「計画・候補」に `/integration-map` を追加（☰メニュー・/legacy画面一覧から到達可能・孤立ページ防止）。一時ページである旨を note に明記。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| app/integration-map/page.tsx | 新規: 対応表＋統合①〜⑥の一時ページ |
| lib/nav-menu.ts | 計画・候補に /integration-map を追加 |

## 4. 検証結果
- typecheck/build/lint: OK
- 手動: pm2 restart後 /integration-map 200・対応表/正本の寄せ先/統合の進め方①〜⑥/承認待ち件数 描画。/legacy 画面一覧に掲載確認。
- 機密スキャン: OK / ob sync: n/a / git push: 5bd5d69 pushed

## 5. 未対応
- 統合の実作業②〜⑥は承認後。対応表ページは統合完了後に撤去予定。
- 一時ページのため /guide・TERMS・operating-model のフルセット更新はスキップ（撤去前提・nav掲載のみ）。

## 6. 危険ポイント
- 読み取り専用の表示ページ。データ書き込みなし。

## 7. 次にやるべきこと
- 統合ゴール①を承認/クローズ判断。
- ②（収益化候補の移行）に着手するか判断。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: 旧Vault→今のゴール運用 対応表ページ(一時)を追加
runId: 20260620-181129 / commit: 5bd5d69

## 目的
統合ゴール①の成果物として、対応表をprogressアプリ内(/integration-map)で見られるように。

## 実施
対応表10行＋正本方針(progress集約)＋統合①〜⑥(ゴール状態連動)の一時ページを新設。
☰メニュー(計画・候補)から到達可能。iPhone向けレイアウト。

## 検証
tsc/build/lint OK。/integration-map 実描画200・/legacy掲載確認。

## 確認したい観点
1. 対応表の粒度・所見は妥当か(抜け漏れ)。
2. 一時ページをnav掲載のみで運用ドキュメント更新スキップの判断は妥当か。
3. ②(収益化候補移行)から着手すべきか、別の順がよいか。
````
