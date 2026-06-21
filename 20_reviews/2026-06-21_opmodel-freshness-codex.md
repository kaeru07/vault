---
date: 2026-06-21
task: 運用ドキュメント鮮度チェック(Codex委譲実装・Claude検証)
runId: 20260621-152802
targetApp: progress
monetizationImpact: none
theme: [workflow]
relatedRunIds: []
commitHashes: [fb71478]
---

# 2026-06-21 運用ドキュメント鮮度チェック（Codex委譲）

## 1. 作業目的
- active[low]ゴール「/guideの運用ページ鮮度を点検する仕組み」を実装。ユーザー方針「実装はCodexに委譲してClaude消費を抑える」の初適用。

## 2. 実施内容
- 実装(Codex / mcp__codex, sandbox workspace-write, approval never):
  - `lib/operating-model.ts`: `operatingModelStaleDays(updated)` / `OperatingModelFreshness` / `getOperatingModelFreshness()`(updatedから経過日数・`stale = days >= 14`)
  - `app/guide/page.tsx`: system タブで stale 時のみ amber 警告バナー表示
- 検証(Claude): 指定2ファイルのみ・tsc0/build0/lint0・秘密なし・/guide?tab=system 200。現在 updated=2026-06-21(days0)で非stale→バナー非表示(正常)。

## 3. 変更ファイル
| ファイル | 変更内容 |
|---|---|
| lib/operating-model.ts | 鮮度計算 getOperatingModelFreshness 等(Codex実装) |
| app/guide/page.tsx | systemタブに stale 警告バナー(Codex実装) |

## 4. 検証結果
- typecheck/build/lint OK・/guide?tab=system 200・秘密スキャンclean・git push fb71478

## 5. 未対応 / 6. 危険ポイント
- 表示専用・データ書き込みなし。閾値14日は固定(必要なら調整)。

## 7. 次にやるべきこと
- 以降の実装も Codex 優先で委譲し Claude 消費を抑える(方針・memory記録済み)。

## 8. ChatGPT レビュー依頼文
````text
対象: progress 運用ドキュメント鮮度チェック / runId 20260621-152802 / commit fb71478
実装をCodexに委譲・Claudeが検証(tsc/build/lint)。14日超で/guideに警告バナー。
確認観点: Codex委譲→Claude検証の分担運用は妥当か / 鮮度閾値14日は適切か。
````
