# nanikiru-shorts

麻雀「何切る？」を Remotion で動画化し YouTube Shorts / TikTok に投稿するためのテンプレ生成アプリ。

## 概要

- パス: `/root/company/apps/nanikiru-shorts`
- スタック: Remotion 4.0 / React 18 / TypeScript / Tailwind
- 出力: `out/sample.mp4`（1080×1920 / 30fps / 15s）
- 主要コマンド: `npm run typecheck` / `npm run render` / `npm run studio`

## 動画構成（450 frames = 15s）

| Section | 範囲 | 内容 |
|---|---|---|
| Hook | 0-2s | キャッチコピー + マスコット |
| HandDisplay | 2-8s | 手牌（2行）・ツモ・ドラ・質問 |
| Countdown | 8-10s | 3-2-1 + 焦り顔マスコット |
| Answer | 10-15s | 提案型ラベル・答え牌・解説・コメント誘導CTA |

## データ構造（`src/data/sampleQuestion.ts`）

- `hookTitle` / `hookSubtitle` / `situation` / `hand` / `tsumo` / `doraIndicator`
- `questionText` / `choices` / `answer` / `explanation`
- optional: `answerLabel` / `discussCta` / `explanationHighlight`
- `followCta`

## 表現方針

- 「正解はこれ！」のような断定は避ける（`answerLabel` で「ロンちゃんは5p派！」等）
- コメント欄で議論が起きやすい構成（`discussCta` で派閥対立を作る）
- 重要ワードを `explanationHighlight` で大きく強調
- マスコットは「AI 先生」ではなく「一緒に考える相棒」

## TODO

- [ ] 別問題テンプレを増やす（answerLabel バリエーション A/B）
- [ ] 投稿後のコメント数を観測・効果測定
- [ ] HandDisplay の「▶ コメントで回答してね！」を discussCta と統一検討

## 関連

- [[mahjong]]
- [[../05_monetization/ideas]]
