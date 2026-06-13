---
date: 2026-06-13
task: Inboxレビュー「修正する」に修正指示プロンプト入力→次回自動実行へ反映
runId: 20260613-234811
targetApp: ny01/progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260613-174719]
commitHashes: [f5214fb]
---

# 2026-06-13 Inboxレビュー「修正する」修正指示プロンプト

> Codex 実装 → Fable レビュー（reviewer）・e2e検証・テストデータ消去・コミット。

## 1. 背景 / 目的
- 「修正する」は needs_followup にするだけで、具体的な修正内容を入力・保存する導線が弱く、次回自動実行で何を直すか不明確だった。
- レビュー画面を「次回自動実行への修正依頼ボックス」にする。

## 2. 変更内容
- 「修正する」押下時、カード内に textarea を展開（モーダルでなくiPhone優先のカード内展開）。「修正依頼として保存」「キャンセル」。
- 保存で `reviewStatus=needs_followup` ＋ ExecutionRun に `fixPrompt`/`fixRequestedAt`/`fixRequestedBy='human'` を保存（`reviewMemo` にも反映）。空欄は警告し送信しない。
- 要修正カードに修正指示を表示（rows プレビュー＋detail 全文）。
- followup おすすめ次作業に fixPrompt を反映: reason 冒頭「人間の修正指示: …」/ doneCriteria 先頭「人間の修正指示を満たす: …」/ notes 全文（上限）。既存候補も更新パスで反映。
- 問題なし/あとで/レビュー済み・物理削除なしは不変。

## 3. 変更ファイル
types/execution-run.ts / lib/execution-run-writer.ts / app/api/execution-runs/[runId]/route.ts / components/newux/InboxActions.tsx / lib/command-center.ts / lib/knowledge-loop.ts / app/guide/page.tsx / docs/operations/current-operating-model.md

## 4. 修正プロンプトの保存先
- ExecutionRun（`data/real/execution-runs.json`）の `fixPrompt` / `fixRequestedAt` / `fixRequestedBy`。新JSON・新DBは作らず既存構造に追加（後方互換）。

## 5. 次回自動実行への連携
- `generateFollowupRecommendationForRun` → `buildFollowupRecommendation`（および既存候補の更新パス）で fixPrompt を reason/doneCriteria/notes に反映。
- 承認（人間）→ Epic → Factory 実行時に、その reason/doneCriteria が作業指示になる。自動Epic化はせず承認必須（安全モデル維持）。

## 6. 状態遷移
- 問題なし→reviewed（消込・レビュー済み残置）/ あとで→snoozed / 修正する→（textarea入力→保存）needs_followup+fixPrompt / キャンセル→状態不変で入力欄を閉じる。

## 7. 検証結果（Fable独立実行）
- tsc 0 / lint 0 / build 成功（50ページ）/ `/` `/decide` `/guide` 200。
- e2e: 実 needs_followup run `20260608-140533` に PATCH で fixPrompt 保存（fixRequestedBy=human）＋ followup 候補の reason 冒頭・doneCriteria 先頭に反映を確認。**検証後にテストデータを消去**（run.fixPrompt=None / 候補からテスト文・fix_prompt_updated履歴を除去）。
- 空欄保存ブロック・問題なし/あとで不変はコード上確認。Codex単体テスト（run-fix-1）の残存なし。

## 8. iPhone表示確認結果
- 未実機（ポート待受制限）。カード内1カラム・textarea `w-full`・ボタン `flex-wrap` で崩れにくい構成。実機目視は残課題。

## 9. 未確認事項 / 10. 残課題
- 実機での 修正する→入力→保存→要修正表示→候補反映 の目視。
- 承認→Factory実行時に fixPrompt が実際の dispatch プロンプト本文へ乗るか（現状は recommendation の reason/doneCriteria 経由）。
- fixPrompt の後編集UI（現状は再度「修正する」で上書き保存）。

## 11. 次にやるべきこと
- iPhone実機確認。承認導線での fixPrompt 反映の最終確認。

## ChatGPT レビュー依頼文
```
対象: progress Inboxレビュー「修正する」修正指示プロンプト（runId 20260613-234811 / commit f5214fb）
変更: types/execution-run.ts, lib/execution-run-writer.ts, app/api/execution-runs/[runId]/route.ts,
  components/newux/InboxActions.tsx, lib/command-center.ts, lib/knowledge-loop.ts,
  app/guide/page.tsx, docs/operations/current-operating-model.md
背景: 「修正する」を修正依頼ボックス化。textareaで修正指示→ExecutionRunにfixPrompt保存→followow候補のreason/doneCriteria/notesへ反映→承認後の作業指示に。
観点:
1. fixPromptの保存先をExecutionRunにした判断（inbox item でなく run 紐付け）は適切か。
2. 次回作業指示への渡し方（recommendation経由・承認必須）で「優先的に拾う」要件を満たすか。dispatchプロンプト本文への直接注入は必要か。
3. 空欄保存ブロック・キャンセルで状態不変・問題なし/あとで不変の回帰がないか。
4. fixPrompt後編集UIの要否。
```
