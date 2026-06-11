---
date: 2026-06-12
task: アカウント登録・サブスク・課金系の候補を今日の判断から除外（AI保留へ）
runId: 20260612-083126
targetApp: progress
monetizationImpact: low
theme: [workflow]
relatedRunIds: [20260612-013213, 20260612-005632]
commitHashes: [b4d65b9]
reviewFileCommit:
---

# progress 時期尚早な人間作業候補の除外レポート

## 作業目的

「今日の判断」にストア公開申請・課金/サブスク・AdMob設定などの人間作業カードが出ていたが、どのアプリに対しても行っていない処置（全アプリ未公開）であり時期尚早。ユーザー指示により除外する。

## 実施内容

- buildInbox で isHumanTaskTitle（（ユーザー作業）/公開申請/ストア公開/AdMob/課金/サブスク/API契約/本人確認/アカウント登録/認証情報）に該当する候補をカード化せず AI保留（件数のみ）へ
- 必要になるタイミング（BirdLog MVP完成→公開申請）は Revenue の収益化ロードマップが現在地として案内
- 結果: 今日の判断 0件（🎉 工場を止める判断はありません。AI工場は稼働を続けます）

## 変更ファイル

- progress/lib/command-center.ts — 人間作業候補をAI保留へ
- progress/docs/operations/current-operating-model.md — 人間作業行の扱い・変更履歴

## 検証結果

- tsc 0 / lint 0 / build 成功 / /decide 200
- スクショ: ![[../attachments/screenshots/2026-06-12-decide-no-humantask.png]] / ![[../attachments/screenshots/2026-06-12-home-no-humantask.png]]
- git push: f5d00c9..b4d65b9 成功

## 未対応

- 人間作業カードの「復活条件」（実際に公開申請が必要になった時の表示再開）は未設計

## 危険ポイント

- 人間作業の表示経路が当面なくなるため、本当に必要な人間作業が発生した場合に気づき遅れるリスク（Revenueロードマップでカバーする想定）

## 次にやるべきこと

- BirdLog MVP完成時に公開申請カードを出す条件設計（milestone連動）

## ChatGPT レビュー依頼文

```
progress アプリの軽微変更レビューをお願いします。
runId: 20260612-083126 / commit: b4d65b9（kaeru07/ny01 main）
変更: ストア公開申請・課金/サブスク・アカウント登録系の自動生成候補を「今日の判断」から除外しAI保留へ（全アプリ未公開で時期尚早のため）。
確認観点: 人間作業の表示経路が無くなることのリスク / 復活条件（milestone連動）の設計案。
```
