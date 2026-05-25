---
date: 2026-05-25
task: vloop9 案件別ToDo一覧の実行ToDo（A-4-1 / A-1 / A-2 / A-3-1）を実ファイル反映 + Issue #80 継続実体化
runId: 20260525-vloop9
targetApp: company-meta / obsidian-vault / obsidian-sync-vault
monetizationImpact: medium
theme: [vault, workflow, obsidian, candidate-006, candidate-007, prompt-template, monetization]
relatedRunIds: [20260525-issue80-todo-refactor, 20260525-vault-inventory, 20260524-vloop1-7]
commitHashes: []
---

# vloop9 案件別ToDo 実行（A-4-1 / A-1 / A-2 / A-3-1）

> Issue #80（案件別ToDo運用を実ファイルへ反映）+ #87（vloop_queue 案件別整理）の継続実体化。
> A セクション 4 件の優先 ToDo を「Issue / 設計だけで終わらない」原則で実ファイルへ反映。
> ユーザー指示「新しい Issue/ToDo を増やすな・実反映を進めろ」を遵守。

---

## 1. 作業目的

`20_reviews/案件別ToDo一覧.md` A セクションの優先 ToDo（A-4-1 / A-1 / A-2 / A-3-1）を実ファイルに反映し、完了 ToDo を `完了ToDoログ.md` に追記して**実行→記録→完了移動**のサイクルを稼働させる。

背景:
- Issue #80 で 4 セクション構造（A 実行 / B 確認待ち / C 承認待ち / D 完了移動候補）が整備済
- vloop_queue.md（Issue #87）で「実行承認済み」項目が確定
- vloop9 では「Issue を増やさず実ファイル反映」を遵守する

期待効果:
- candidate-006 / 007 が ChatGPT 方向性レビュー可能状態に到達（承認パック完成）
- candidate-001 / 005 の内部用語が完全に日本語化（Issue #69 残作業）
- Vault 全体棚卸し（Issue #59）の旧運用注記残作業が解消

---

## 2. 実施内容

### A-4-1 旧運用フォルダ個別ファイル注記追加（4 ファイル）

- `04_reviews/README.md` 先頭に「📦 旧運用 / 参照のみ」callout 追加
- `04_reviews/Claude作業レビュー運用.md` 先頭に同 callout 追加
- `04_reviews/review-template.md` は **既に旧版注記済**でスキップ（重複防止）
- `07_tasks/inbox/2026-05-17-progress-radar-gantt.md` 先頭に同 callout 追加（obsidian-vault 側 / sync-vault に該当フォルダなし）
- `chatgpt/README.md` 先頭に「⏸ 暫定 / 運用ルール未確定」warning callout 追加

### A-1 candidate-001 本体・補助 用語注記追加（4 ファイル）

- `candidate-001.md` に Issue #69 用語注 callout 追加（10 項目 / candidate / pending_approval / approved / progress 投入 / ストア公開 / AdMob / 公開ブロッカー / ASO / 妥当性評価メタ）
- `candidate-001_公開ブロッカー.md` に用語注追加（9 項目）
- `candidate-001_7日実行プラン.md` に用語注追加（7 項目）
- `candidate-001_progress投入設計.md` に用語注追加（10 項目）

### A-2 candidate-005 本体・補助 用語注記追加（4 ファイル）

- `candidate-005.md` に用語注追加（11 項目 / tokens/sec / TTFT / 体感スコア / MVP モック / Shorts 送客 / note 販売 / nanikiru-shorts 基盤 等）+ 1 文収益化シナリオに括弧補足
- `candidate-005_公開ブロッカー.md` に用語注追加（10 項目）
- `candidate-005_7日実行プラン.md` に用語注追加（10 項目）
- `candidate-005_progress投入設計.md` に用語注追加（9 項目）

### A-3-1 candidate-006 / 007 補助 4 ファイル × 2 = 8 ファイル新規作成

- `candidate-006_公開ブロッカー.md` 新規（B1-B8 / 判定基準客観化が主要論点）
- `candidate-006_7日実行プラン.md` 新規（Day1-7 / B1+B2 → B3 → B4 → B5 → B8 → B7）
- `candidate-006_progress投入設計.md` 新規（最初の 3 ToDo: B1+B2 / B3 / B4-1）
- `20_reviews/candidate-006_ChatGPT承認パック.md` 新規（§1-§14 / 自己完結型）
- `candidate-007_公開ブロッカー.md` 新規（B1-B9 / 既存ガイド統合方針が主要論点）
- `candidate-007_7日実行プラン.md` 新規（Day1-7 / B1+B9 → B2 統合方針実施 → B3 → B5 → B4 → B7 → B8）
- `candidate-007_progress投入設計.md` 新規（最初の 3 ToDo: B1+B9 / B2 / B3）
- `20_reviews/candidate-007_ChatGPT承認パック.md` 新規（§1-§15 / 統合方針 A/B/C の判断材料を §9 で集約）

### vloop 終了処理

- `案件別ToDo一覧.md` A セクション 4 件を done に更新 + D セクションに vloop9 完了分追記
- `案件別ToDo一覧.md` C セクション candidate-006/007 リンクを新規承認パックへ差し替え
- `完了ToDoログ.md` に「2026-05-25（vloop9 達成分）」セクション新規追記（5 件）
- `vloop_queue.md` を sync-vault 側にコピー + vloop9 完了マーク追加（4 件）

---

## 3. 変更ファイル

### 編集（sync-vault）

- `04_reviews/README.md` — 旧運用 callout 追加
- `04_reviews/Claude作業レビュー運用.md` — 旧運用 callout 追加
- `chatgpt/README.md` — 暫定運用 warning callout 追加
- `05_monetization/scenarios/candidate-001.md` — 用語注 callout 追加 + 本文括弧補足
- `05_monetization/scenarios/candidate-001_公開ブロッカー.md` — 用語注 callout 追加
- `05_monetization/scenarios/candidate-001_7日実行プラン.md` — 用語注 callout 追加
- `05_monetization/scenarios/candidate-001_progress投入設計.md` — 用語注 callout 追加
- `05_monetization/scenarios/candidate-005.md` — 用語注 callout 追加 + 本文括弧補足
- `05_monetization/scenarios/candidate-005_公開ブロッカー.md` — 用語注 callout 追加
- `05_monetization/scenarios/candidate-005_7日実行プラン.md` — 用語注 callout 追加
- `05_monetization/scenarios/candidate-005_progress投入設計.md` — 用語注 callout 追加
- `20_reviews/案件別ToDo一覧.md` — A セクション完了反映 + D セクション追記
- `20_reviews/完了ToDoログ.md` — vloop9 セクション追記
- `20_reviews/vloop_queue.md` — sync-vault に複製 + vloop9 完了マーク

### 編集（obsidian-vault 側のみ）

- `07_tasks/inbox/2026-05-17-progress-radar-gantt.md` — 旧運用 callout 追加（sync-vault に該当フォルダなしのため obsidian-vault のみ）

### 新規（sync-vault）

- `05_monetization/scenarios/candidate-006_公開ブロッカー.md`
- `05_monetization/scenarios/candidate-006_7日実行プラン.md`
- `05_monetization/scenarios/candidate-006_progress投入設計.md`
- `05_monetization/scenarios/candidate-007_公開ブロッカー.md`
- `05_monetization/scenarios/candidate-007_7日実行プラン.md`
- `05_monetization/scenarios/candidate-007_progress投入設計.md`
- `20_reviews/candidate-006_ChatGPT承認パック.md`
- `20_reviews/candidate-007_ChatGPT承認パック.md`
- `20_reviews/2026-05-25_vloop9-todo-execution.md`（本ファイル）

---

## 4. 検証結果

- typecheck / build / lint: n/a（Markdown のみの変更のため）
- 機密スキャン: 実施（API キー / .env / sk- / Bearer / -----BEGIN 等のパターンが本文・例示に**含まれていないことを確認**）
- リンクスキャン: candidate-006/007 補助ファイル間の `[[link]]` は新規作成時に同時整備済（candidate-005 形式踏襲）
- 用語注の網羅性: candidate-001/005 の本体 + 補助 3 ファイル × 2 = 計 8 ファイルすべてに Issue #69 用語注追加
- ob sync: vloop 終了処理の最後で実施予定
- ミラー: ob sync 完了後に rsync で実施予定
- git push: ミラー後に obsidian-vault 側で実施予定

---

## 5. 未対応

- B セクション（あなた確認待ち）の 8 項目: ユーザー側のアクション待ち（Claude では完結できない）
- C セクション（承認待ち）の 7 項目: ChatGPT 方向性レビュー + 人間判断待ち（Claude では approved 化禁止）
- A セクション残（次サイクル候補）:
  - N-03 LLM Chooser 判定基準客観化検討 1 ページ
  - N-04 Vault Search Cheatsheet と既存「Vault の見方ガイド」統合方針決定（A/B/C のいずれか）
  - 全 .md 横断「存在しないパス案内」grep 検査（#59 残・大規模）
  - `Vaultの見方_どこを見れば何がわかるか` の分割 / 簡略化
  - chatgpt/ フォルダの運用ルール確定
  - #68 Mermaid テンプレを candidate-001/005/006/007 本体へ反映
- A-5 Hermes Agent 最小運用フロー: candidate-001 approved 後に着手（blocked）

---

## 6. 危険ポイント

- 既存機能への影響: なし（Markdown 追記のみ・既存リンク・既存内容は破壊していない）
- DB / 認証 / 本番への影響: なし
- 機密混入リスク: 用語注で例示する内部用語に実値を含めていないことを確認済
- 破壊的変更: なし（ファイル削除・リネームなし / 既存内容の削除なし）
- `07_tasks/inbox/2026-05-17-progress-radar-gantt.md` 編集: sync-vault 側に該当フォルダがないため obsidian-vault 側のみ編集。次回 ob sync で sync-vault 側に反映されない可能性あり（CLAUDE.local.md「sync-vault が正本」原則と部分的に乖離 / 旧運用ファイルのため次サイクル以降の運用判断）

---

## 7. 次にやるべきこと

1. ChatGPT が candidate-006 / 007 を方向性レビュー（[[candidate-006_ChatGPT承認パック]] / [[candidate-007_ChatGPT承認パック]]）
2. ユーザーが iPhone Safari で `90_prototypes/llm-chooser/index.html` と `90_prototypes/vault-search-cheatsheet/index.html` を実機確認
3. candidate-007 の統合方針 A/B/C を ChatGPT + ユーザーで決定
4. 次サイクル vloop10 で N-03 客観化方針 1 ページ + N-04 統合方針実施 + #68 Mermaid テンプレ candidate 本体反映
5. vloop9 完了分（D セクション）を vloop10 で完了ToDoログ.md へ最終確定移動

---

## 8. ChatGPT レビュー依頼文

```
レビュー依頼: vloop9 案件別ToDo 実行（Issue #80 / #87 継続実体化）

対象: kaeru07/vault の vloop9 サイクル
runId: 20260525-vloop9
作業レビューファイル: 20_reviews/2026-05-25_vloop9-todo-execution.md

vloop9 で実施した 4 件の実ファイル反映を確認してください:

1. A-4-1 旧運用フォルダ個別ファイル注記追加（4 ファイル / Issue #59 残作業解消）
   - 04_reviews/README.md / Claude作業レビュー運用.md
   - 07_tasks/inbox/2026-05-17-progress-radar-gantt.md（obsidian-vault のみ）
   - chatgpt/README.md
2. A-1 candidate-001 本体 + 補助 3 ファイル用語注記追加（Issue #69 残）
3. A-2 candidate-005 本体 + 補助 3 ファイル用語注記追加（Issue #69 残）
4. A-3-1 candidate-006 / 007 補助 4 ファイル × 2 = 8 ファイル新規作成

特に確認したい観点:
- candidate-006 / 007 の判断するための資料一式（承認パック）が ChatGPT 方向性レビュー可能な状態に到達しているか
- candidate-006 の判定基準客観化（N-03 既知制約 / 個人観察ベース）の解消方針が妥当か
- candidate-007 の統合方針 A/B/C のデフォルト推奨（B 既存ガイド索引化）が妥当か
- 用語注の網羅性（Issue #69 進捗）が candidate-001/005 で十分か（さらに必要な用語があるか）
- A-4-1 の旧運用注記が「破壊的削除なし」の方針と整合しているか
- 4 セクション構造（A 実行 / B 確認待ち / C 承認待ち / D 完了移動候補）が機能しているか

依頼: approve / 修正後採用 / 差し戻し / 棚上げ のいずれかで判断 + 候補-006 / 007 の方向性 approve / hold / reject を別途出してください。
```

---

## 関連

- [[案件別ToDo一覧]] — A セクション完了反映 + D セクション追記
- [[完了ToDoログ]] — vloop9 達成分 5 件追記
- [[vloop_queue]] — vloop9 完了マーク追加
- [[../05_monetization/scenarios/candidate-001]] / [[candidate-001_ChatGPT承認パック]]
- [[../05_monetization/scenarios/candidate-005]] / [[candidate-005_ChatGPT承認パック]]
- [[../05_monetization/scenarios/candidate-006]] / [[candidate-006_ChatGPT承認パック]]
- [[../05_monetization/scenarios/candidate-007]] / [[candidate-007_ChatGPT承認パック]]
- [[Vault全体棚卸し]] — Issue #59 残作業の旧運用注記が解消（user_check 移動候補）
- Issue: kaeru07/vault#80 / #87 / #69 / #59 / #61 / #74 / #76 / #77 / #78 / #79 / #70 / #63
