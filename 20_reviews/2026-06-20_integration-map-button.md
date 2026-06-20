---
date: 2026-06-20
task: 司令塔トップに対応表ページへの一時ボタンを配置
runId: 20260620-192536
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: [20260620-181129]
commitHashes: [fd38e09]
---

# 2026-06-20 司令塔トップに対応表ページへの一時ボタン

## 1. 作業目的
- ユーザー要望「progressアプリから遷移できるように一時ボタンも配置して」。/integration-map（対応表）への目立つ導線を追加。

## 2. 実施内容
- `app/page.tsx`: ReviewCopyButton 直後に `/integration-map` への Link（インディゴ枠・🗺 旧Vault→今のゴール運用 対応表・「一時 →」バッジ）を追加。コメントで統合完了後に撤去予定と明記。☰メニュー掲載と併用。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| app/page.tsx | 司令塔トップに /integration-map への一時ボタンを追加 |

## 4. 検証結果
- typecheck/build/lint: OK
- 手動: pm2 restart後 / 200・ボタン文言と /integration-map href 描画確認。
- 機密スキャン: OK / ob sync: n/a / git push: fd38e09 pushed

## 5. 未対応
- 統合完了後にボタンとページ（/integration-map）を撤去。

## 6. 危険ポイント
- 表示リンク追加のみ。ロジック・データ変更なし。

## 7. 次にやるべきこと
- 統合②（収益化候補の移行）に着手するか判断。

## 8. ChatGPT レビュー依頼文
````text
対象アプリ: progress
作業: 司令塔トップに対応表ページ(/integration-map)への一時ボタンを配置
runId: 20260620-192536 / commit: fd38e09

ホーム上部に対応表への導線(一時バッジ付き)を追加。tsc/build/lint OK・/ 200確認。
統合完了後に撤去予定。確認観点: 導線の置き場所は適切か/一時バッジで意図が伝わるか。
````
