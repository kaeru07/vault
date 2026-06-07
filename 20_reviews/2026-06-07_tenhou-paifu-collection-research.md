---
date: 2026-06-07
task: 天鳳 公開牌譜 収集方法の調査（/yomi 高品質牌譜収集）
runId: 20260607-212218
targetApp: mahjong
monetizationImpact: low
theme: [market-research, app-strategy, workflow]
relatedRunIds: [20260607-190005]
commitHashes: [cb802fc]
# reviewFileCommit:
---

# 2026-06-07 天鳳 公開牌譜 収集方法の調査

> 1 作業 = 1 ファイル の自動生成レビュー。

---

## 1. 作業目的

- なぜ: `/yomi` 問題生成用に、天鳳の高品質公開牌譜を**認証不要・Cookie不要**で `data/imported/` に集める方法を調査する。
- 条件: 公開牌譜のみ／認証不要／Cookie不要／鳳凰卓優先／mjai か JSON へ変換可能な形式優先。

---

## 2. 実施内容（調査結果）

- Web 調査で houou-logs README・`tenhou.net/sc/raw/`・Qiita のツール一覧を確認。
- **公開フル牌譜は鳳凰卓(scc)のみ**と判明。`sc/raw/` は認証不要、年次 zip `scrawYYYY.zip`(2006-2025)+`list.cgi`(約5分毎)。プレフィックス sca個室/scb段位戦(一般上級特上)/scc鳳凰卓/scd雀荘戦/sce技能戦。
- **特上卓上位の公開フル牌譜は入手不可**（本人DLのみ）。ただし鳳凰卓＝七段+/特上点到達の最上位公開帯なので「高品質」目的は達成。
- 推奨ツール: **Apricot-S/houou-logs**（Python/MIT・phoenix-logs後継、fetch→download→export、レート制御内蔵）＋ mjlog2json / tenhou-to-mjai（変換）。
- **最重要リスク**: 天鳳・houou-logs 双方が**牌譜の再配布を禁止**。生牌譜の GitHub push は規約抵触、`/yomi`（天鳳と無関係サービス）への適用もグレー。
- 対策として `.gitignore` に生牌譜除外を追加（git check-ignore で検証）し、調査結果を `docs/tenhou-collection.md` に整理。

### 要求された出力

- おすすめ収集方法: houou-logs で鳳凰卓 scc を収集 → mjlog → JSON/mjai → `/yomi` 変換 → `validate-yomi.mjs` → S/A 採用。
- 必要ツール: Python3 + pip/uv、houou-logs、mjlog2json or tenhou-to-mjai、自作変換アダプタ。
- 実装難易度: 低〜中（収集=低、変換=低、mjai→yomi アダプタ=中）。
- 継続収集: 可（list.cgi 増分 + 年次 zip バックフィル、SQLite で差分管理）。
- 自動化: 技術的に可（cron・同時1session/20分間隔のマナー必須）。ただし生牌譜の自動 push は規約違反。

---

## 3. 変更ファイル

| ファイル | 変更内容 |
|---|---|
| apps/mahjong/docs/tenhou-collection.md | 収集方法/ツール/難易度/継続/自動化/規約リスクと対策（新規） |
| apps/mahjong/.gitignore | 生牌譜(*.mjlog/data/imported配下のxml/json/raw/tenhou)を除外 |

---

## 4. 検証結果

- 機密スキャン: OK
- gitignore 検証: OK（生牌譜=ignored、README=tracked を git check-ignore で確認）
- typecheck / build: n/a（docs と gitignore のみ・コード変更なし）
- ob sync: Fully synced
- git push: mahjong cb802fc（pushed）／ obsidian-vault は本セッションで mirror+push

---

## 5. 未対応

- 実牌譜の収集は未実行（調査のみ）。
- mjai/JSON → /yomi 変換アダプタは未実装（公開可否の判断後に実装）。

---

## 6. 危険ポイント（＝承認必須事項）

- **利用規約（牌譜再配布禁止）**が最大の論点。生牌譜の公開リポジトリ push は抵触。牌譜由来の `/yomi` 問題を公開する可否は**人間の法務判断が必要**で、ここは自走しない（承認必須）。
- 対策済み: `.gitignore` で生牌譜を git 管理外に。
- 推奨運用: 「収集＋問題生成＋ローカル検証」は半自動、「公開(push)」は手動承認ゲート。

---

## 7. 次にやるべきこと

- ユーザー判断: 牌譜由来問題の**公開可否**（規約/法務）。
  - OK → houou-logs で鳳凰卓収集 → 変換アダプタ実装 → S/A のみ採用。
  - NG → ローカル生成のみ運用、または規約が許す別ソース（自作対局・許諾データ）検討。
- 変換アダプタの「読み筋説明可能か／正解一意か」判定ロジック設計。

---

## 8. ChatGPT レビュー依頼文

````text
以下は Claude Code の調査報告です。レビューしてください。

対象アプリ: mahjong
作業: 天鳳 公開牌譜 収集方法の調査（/yomi用）
runId: 20260607-212218
日付: 2026-06-07
GitHub commit: mahjong cb802fc

## 調査結論
- 公開フル牌譜は鳳凰卓(scc)のみ。特上卓上位は公開入手不可だが鳳凰卓が最上位公開帯。
- ツール: Apricot-S/houou-logs(収集)+mjlog2json/tenhou-to-mjai(変換)。認証/Cookie不要。
- 難易度低〜中、継続・自動化可。
- 最大リスク: 天鳳の利用規約が牌譜再配布を禁止。生牌譜のGitHub pushは抵触。
- 対策: .gitignoreで生牌譜を除外。公開可否は人間判断(承認必須)。

## 確認したい観点
- 利用規約リスクの評価は妥当か。牌譜由来の「変換済み問題」公開はどこまで許容されるか
- 鳳凰卓のみで品質目的を満たすという判断は妥当か
- 自動化の線引き(収集は半自動・公開は手動承認)は妥当か
- 規約に抵触しない代替の高品質牌譜ソースはあるか
````

---

## 関連

- progress runId: 20260607-212218（正本）
- 関連 run: 20260607-190005
- 関連レビュー: [[2026-06-07_yomi-ingestion-rule]]
- 関連アプリ: [[../02_apps/mahjong]]
- フォルダ運用: [[../20_reviews/README]]
