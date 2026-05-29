# Research Topic 生成プロンプト（Hermes / Codex 用）

market-research の日次ログを **構造化 Research Topic** として出力させるためのテンプレ。
思想: 「自由文の AI ニュースメモ」ではなく「構造化 Research DB」。1 Topic = 1 カード = 1 判断単位。

正本仕様: news-app `docs/research-format.md`。news-app 側 parser がこの構造を解釈してカード表示する。

---

## 出力ルール（Hermes / Codex へ渡す指示）

- 日次ファイルは `# 市場調査ログ YYYY-MM-DD` で始める。
- `## 今日の結論`（3〜5 行）→ `## トピック一覧` → `## 今日のToDo候補` → `## 保留・未確認` → `## 取得状況` の順。
- トピックは `### Topic N: 一文タイトル` で区切り、下記項目を埋める。
- 自由文で長々書かず、各項目を短く構造化する。要約は 2〜3 行、根拠は出典の要点のみ。
- 参考URL は `[official]` `[reddit]` `[ranking]` などの出典ラベルを付ける（付けない場合はドメインから推定される）。
- `重要度` 未指定は B、`ToDo化` 未指定は no として扱われる。S/A は方針に効くものだけに絞る。
- 確度は 公式=高 / 二次情報=中 / 推測・未確認=低。
- 既存PJ（progress / news-app / mahjong / shogi / scrape-lab）に効くものは `既存PJへの影響` に必ず書く。

---

## テンプレ本体（コピーして埋める）

````markdown
# 市場調査ログ YYYY-MM-DD

## 今日の結論
（3〜5 行。各行 1 文で言い切る）

## トピック一覧

### Topic 1: 一文で言い切れるタイトル

topic-id:
kebab-case-stable-id

TL;DR:
1 行でこのトピックの要点。

- 種別:
  市場 | 競合 | ユーザー不満 | 収益化 | 技術 | ToDo候補
- 重要度:
  S | A | B | C
- 確度:
  高 | 中 | 低
- timelineKey:
  継続追跡テーマの slug（日をまたいで同じ値 / 任意）
- duplicateKey:
  重複候補を束ねる slug（任意）
- sourceDate:
  YYYY-MM-DD（情報そのものの日付 / 古いと stale）
- タグ:
  - #tag-a
  - #tag-b
- 要約:
  - 2〜3 行で要点。
- 根拠:
  - 出典の要点。
- 参考URL:
  - [official] https://example.com/...
  - [reddit] https://reddit.com/...
- 収益化への示唆:
  - 収益化にどう効くか。
- 既存PJへの影響:
  - Progress:
    - 
  - News App:
    - 
  - Mahjong:
    - 
- 次アクション:
  - 次に取る具体行動。
- ToDo化:
  yes | no
- 更新日時:
  YYYY-MM-DDThh:mm:ss+09:00

### Topic 2: ...

## 今日のToDo候補
- 

## 保留・未確認
- 

## 取得状況
- status: complete | partial
- 理由:
- 次回再調査対象:
````

---

## 判定基準（要約）

- 種別: 市場 / 競合 / ユーザー不満 / 収益化 / 技術 / ToDo候補。迷ったら general。
- 重要度: S=方針変更級 / A=近く反映 / B=把握（既定）/ C=参考。
- 確度: 高=公式裏取り / 中=二次情報 / 低=推測・未確認。
- ToDo化: そのまま作業 ToDo にできるなら yes。
- sourceType: official / reddit / github / ranking / paper / social / news / other。
- timelineKey: 同じテーマを日またぎで追うとき同じ slug を付ける（関連Timeline に並ぶ）。
- duplicateKey: 重複候補を束ねる slug（AI 判定なし・同一値が重複候補に並ぶ）。
- sourceDate: 情報そのものの日付。30 日以上前は stale バッジ。

詳細は news-app `docs/research-format.md` を参照。
