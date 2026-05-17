# ChatGPT履歴ZIP再配置確認とObsidian整理準備

## 登録元
- 登録者: ChatGPT
- 登録日: 2026-05-17
- 種別: ToDo / 作業依頼メモ
- 優先度: high
- リスク: medium
- 担当想定: Claude Code

## 目的
ChatGPT履歴ZIPを置き直したため、前回の「壊れている/展開できない」可能性を前提にせず、現在配置されているZIPを改めて確認し、問題なければObsidian整理作業へ進める状態にする。

## 対象ZIP
/root/company/obsidian-sync-vault/00_inbox/aaab060467257cf6b92e5f0e1a1f459027a8c9b6eca0fcdfc82d3196447105e2-2026-05-16-02-39-30-5c9f0a4bd0d84732bb36ec2ed5a100f3.zip

## 背景
- 以前、同ZIPについて「壊れている/展開できない」可能性がある報告があった。
- その後、ZIPを置き直した。
- そのため、今回の確認では現在配置されているZIPを正として再確認する。

## 確認項目
- [ ] ファイル存在確認
- [ ] 更新日時確認
- [ ] ファイルサイズ確認
- [ ] fileコマンドでZIPとして認識されるか確認
- [ ] unzip -tで整合性確認
- [ ] zipinfoで中身確認
- [ ] conversations.jsonの有無確認
- [ ] 問題があればエラー内容をそのまま記録
- [ ] 問題なければObsidian整理作業に進める状態として記録

## 実行してよい確認コマンド
- ls -lh 対象ZIP
- stat 対象ZIP
- file 対象ZIP
- unzip -t 対象ZIP
- zipinfo 対象ZIP | head -50
- python3 -m zipfile -t 対象ZIP

## Obsidian整理方針
- 全会話をいきなりMarkdown化しない。
- まずはインデックス作成、重要会話抽出、カテゴリ別まとめを優先する。
- high重要度の会話だけ個別要約Markdownを作成する。
- 機密情報は値を書かず、種別だけ記録する。

## 禁止事項
- 元ZIPを削除しない。
- ZIPを勝手に移動しない。
- conversations.jsonの中身をこの作業では大量展開しない。
- APIキー、Cookie、トークン、.env、パスワード、支払い情報、個人情報をMarkdownに書かない。
- Obsidian Vault直下に大量ファイルを散らばらせない。
- pushできていないのにGit反映済みと書かない。

## 完了条件
- 現在配置されているZIPの状態が確認されている。
- ZIPが正常か、壊れているかが判断されている。
- conversations.jsonの有無が確認されている。
- エラーがあればコマンド名とエラー内容が記録されている。
- 次に進める作業が明確になっている。

## 最終報告に含めること
- ZIPは正常か / 壊れているか
- ファイルサイズ
- 更新日時
- 読めた主要ファイル
- conversations.jsonの有無
- 失敗したコマンドとエラー内容
- 次に取るべき対応
