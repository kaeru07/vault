# 03_prompts — Claude Code プロンプト集

Claude Code（VPS / iPhone）に渡すプロンプト雛形を保管する。**コピペで使える状態**を維持するのが目的。

## 雛形の3段階

| 雛形 | 規模目安 | 用途 |
|---|---|---|
| [[claude-code-light]] | 軽量（1〜3ファイル） | 文言修正・UI 微修正・単発不具合 |
| [[claude-code-standard]] | 標準 | 機能追加・画面改修・データ追加 |
| [[claude-code-full]] | フル | 新規アプリ・DB・認証・全体設計・複数アプリ横断 |

迷ったら **light → standard → full** の順に必要分だけ拡張する。最初から full を選ばない。

## タスク雛形

- `90_templates/claude-task-template.md` — 1 件の作業依頼を組み立てるための骨組み
  - 「対象 / 目的 / やること / 禁止 / 完了条件 / 検証 / 報告形式」を埋める形式

## 書き方の原則

- **何を直すか / どこを触るか / どこを触らないか / 完了条件 / 検証方法**（5項目）を必ず含める
- 既存仕様の破壊的変更は明確に「禁止」セクションへ書く
- 検証セット（typecheck + build + 動作確認）を完了条件に入れる
- 報告形式を指定する（実施内容 / 変更ファイル / 検証結果 / 未対応 / 次にやるべきこと）
- 機密情報・パス以外の認証情報をプロンプトに書かない

## プロンプトを改善したら

1. 該当 md を編集
2. 変更理由を `01_daily/today.md` か `04_reviews/` に 1 行残す
3. 大きな方向転換のときは `pm/decision-log.md` 側にも記録

## 関連

- [[../02_apps/progress]]
- [[../04_reviews/review-template]]
- [[../90_templates/claude-task-template]]
