---
status: active
last_touched: 2026-05-13
monetization_impact: medium
theme: [mahjong, shorts, monetization, app-strategy, youtube, tiktok]
phase: development
---

# nanikiru-shorts（麻雀「何切る？」動画量産）

> 麻雀「何切る？」を Remotion で動画化し YouTube Shorts / TikTok に投稿するための**テンプレ生成アプリ + 投稿運用**。

---

## 目的

- **認知獲得 → アプリ DL の入口**を作る
- Shorts のコメント欄で**議論を発生させる**ことを最優先（=「正解」より「派閥」）
- 長期的には**麻雀学習ポータル**（[[mahjong]]）の入口チャネルにする

---

## Shorts / TikTok 戦略

### 動画構成（450 frames = 15s）

| Section | 範囲 | 内容 |
|---|---|---|
| Hook | 0-2s | キャッチコピー + マスコット |
| HandDisplay | 2-8s | 手牌（2 行）・ツモ・ドラ・質問 |
| Countdown | 8-10s | 3-2-1 + 焦り顔マスコット |
| Answer | 10-15s | 提案型ラベル・答え牌・解説・コメント誘導 CTA |

### 表現方針

- 「正解はこれ！」のような断定は避ける（`answerLabel` で「ロンちゃんは5p派！」等）
- コメント欄で議論が起きやすい構成（`discussCta` で派閥対立を作る）
- 重要ワードを `explanationHighlight` で大きく強調
- マスコットは「AI 先生」ではなく「一緒に考える相棒」

---

## 差別化

- **断定しない**：他チャンネルの多くが「正解はこれ！」なので、議論を促す立ち位置自体が差別化になる
- **AI 雀士キャラ（ロンちゃん）**：「派」を表明することでコメントが集まる
- **15 秒固定構成**：量産可能 = 投稿頻度で勝負できる
- **本家アプリへの導線**を最後に置ける（[[mahjong]]）

---

## 自動化案

- Remotion テンプレを **JSON 1 個 → 動画 1 本** に統一（量産しやすく）
- 問題 JSON の生成補助に Claude / Codex を使う（手牌・質問・派閥コメントまでセット）
- 投稿アップロードまでスクリプト化（YouTube Data API / TikTok API）
- ABテスト用に `answerLabel` バリエーション 2 〜 3 種を仕込む
- 投稿後のコメント数 / 視聴維持率を progress アプリへ集約

---

## 収益化方法

- **広告収益**：Shorts ファンド / TikTok 収益化
- **アプリ DL 誘導**：[[mahjong]] / 個別アプリへの送客
- **チャンネル登録誘導**：将来の教材販売・コミュニティ運営の母数作り
- **コラボ / タイアップ**：麻雀系メーカー・媒体との連携（中期）

---

## 現状の課題

- 動画テンプレが 1 パターンしかない → 視聴飽きリスク
- コメント反応の測定が手動
- 「コメントで回答してね！」を HandDisplay と Answer の両方に出すか統一するか未決
- 投稿頻度と運用負荷のバランス未検証

---

## 次アクション

- [ ] 別問題テンプレを増やす（answerLabel バリエーション A / B）
- [ ] 投稿後のコメント数を観測・効果測定
- [ ] HandDisplay の「▶ コメントで回答してね！」を discussCta と統一検討
- [ ] 投稿アップロードの自動化（最低限の CLI）
- [ ] 月次の再生 / 登録 / コメント数を [[../05_monetization/収益化候補一覧]] へ転記

---

## 技術メモ

- パス: `/root/company/apps/nanikiru-shorts`
- スタック: Remotion 4.0 / React 18 / TypeScript / Tailwind
- 出力: `out/sample.mp4`（1080 × 1920 / 30fps / 15s）
- 主要コマンド: `npm run typecheck` / `npm run render` / `npm run studio`
- データ: `src/data/sampleQuestion.ts`
  - `hookTitle` / `hookSubtitle` / `situation` / `hand` / `tsumo` / `doraIndicator`
  - `questionText` / `choices` / `answer` / `explanation`
  - optional: `answerLabel` / `discussCta` / `explanationHighlight`
  - `followCta`

---

## 関連

- [[mahjong]]
- [[../05_monetization/収益化ロードマップ]]
- [[../05_monetization/収益化候補一覧]]
- [[../06_research/GooglePlay市場調査]]
