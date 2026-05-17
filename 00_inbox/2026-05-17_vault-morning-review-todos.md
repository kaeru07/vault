# ChatGPT ToDo投函 — Vault朝会・レビュー消化・状態同期

作成日: 2026-05-17
作成元: ChatGPT
目的: Vaultリポジトリ確認結果から、progressへ登録すべきToDo候補を投入する

```json
{
  "todos": [
    {
      "title": "Vault朝会システムを追加する",
      "priority": "high",
      "risk": "medium",
      "targetApp": "progress",
      "targetPath": "/root/company/apps/ny01/progress",
      "status": "pending_approval",
      "assignee": "claude",
      "source": "chatgpt_review",
      "taskPrompt": "Obsidian Vaultとprogressを連携し、毎朝確認すべきレビュー・今日進めるべき作業・収益化優先度の高い候補を整理する『Vault朝会』機能または運用スクリプトを設計・実装してください。20_reviews/_review_queue.md、02_apps/*.md、05_monetization/*.md、必要に応じてprogressのExecutionRun/タスク情報を参照し、01_daily/YYYY-MM-DD.md または progress内の朝会画面に要約を出せるようにしてください。実装前に既存の /morning /logs /pending /queue の構造を確認し、既存導線を壊さないこと。",
      "work": "Vaultの未レビュー・アプリ状態・収益化判断を朝会としてまとめ、今日やるべきことを見える化する",
      "allowed": [
        "/root/company/apps/ny01/progress/**",
        "/root/company/obsidian-sync-vault/**",
        "/root/company/obsidian-vault/**"
      ],
      "forbidden": [
        ".env* の実値出力・変更",
        "APIキー・トークン・秘密情報の記録",
        "既存ExecutionRunやreview_queueの破壊的変更",
        "未レビュー項目を勝手にレビュー済みにすること"
      ],
      "doneCriteria": [
        "Vault朝会で読む情報源と出力先が明確になっている",
        "未レビュー上位3件、今日やる作業候補3件、収益化観点の警告が出せる",
        "既存のprogress導線またはObsidian Dailyに無理なく統合されている",
        "npm run typecheck と npm run build を実行し、結果を報告している",
        "ExecutionRun POST、20_reviews生成、_review_queue追記、Vault同期、GitHub pushまで完了している"
      ],
      "memo": "記事の朝6時まとめを、そのまま家庭運用ではなくAI開発工場向けに変換する中核ToDo。"
    },
    {
      "title": "progress.mdの状態ノートを最新化する",
      "priority": "high",
      "risk": "low",
      "targetApp": "obsidian-vault",
      "targetPath": "/root/company/obsidian-sync-vault/02_apps/progress.md",
      "status": "pending_approval",
      "assignee": "claude",
      "source": "chatgpt_review",
      "taskPrompt": "02_apps/progress.md が 2026-05-13 時点の内容で止まっているため、直近の vault-inbox API、ChatGPT Inbox取り込み、Codex SDK実験、20_reviews/_review_queue運用、GitHub反映ルールを踏まえて最新化してください。単なる追記ではなく、現在の役割・主要機能・現状課題・改善候補・今後の方向性を実態に合わせて整理してください。",
      "work": "Obsidianを正本として使うため、progressアプリの状態ノートを現状に追いつかせる",
      "allowed": [
        "/root/company/obsidian-sync-vault/02_apps/progress.md",
        "/root/company/obsidian-vault/02_apps/progress.md",
        "/root/company/obsidian-sync-vault/20_reviews/_review_queue.md",
        "/root/company/obsidian-vault/20_reviews/_review_queue.md"
      ],
      "forbidden": [
        "実装コードの変更",
        "機密情報の記載",
        "過去レビューの削除",
        "未確認の完了断定"
      ],
      "doneCriteria": [
        "progress.md の last_touched が更新されている",
        "Vault投函API・ChatGPT Inbox・Codex連携・レビューキュー運用が反映されている",
        "現状課題に『レビュー待ち滞留』『状態ノート陳腐化』が記載されている",
        "ob sync、ミラー、GitHub pushが完了している",
        "ExecutionRun POSTと20_reviews/_review_queue追記が完了している"
      ],
      "memo": "現在のprogress.mdが古いため、AIがVaultを読むと判断がズレるリスクがある。"
    },
    {
      "title": "review_queue未レビューを優先度順に整理してレビュー消化計画を作る",
      "priority": "high",
      "risk": "low",
      "targetApp": "obsidian-vault",
      "targetPath": "/root/company/obsidian-sync-vault/20_reviews/_review_queue.md",
      "status": "pending_approval",
      "assignee": "claude",
      "source": "chatgpt_review",
      "taskPrompt": "20_reviews/_review_queue.md の未レビューが溜まっているため、レビュー対象を収益化・基盤重要度・依存関係の観点で優先順位付けしてください。特に vault-inbox-token-setup、vault-inbox-hardening、vault-inbox-api、chatgpt-inbox-import、progress-codex-experiment、service-map を優先候補として確認し、今日レビューすべき3件、後回しでよいもの、次にToDo化すべきものを整理してください。レビュー済みチェックは勝手に付けず、計画ファイルを作るだけにしてください。",
      "work": "溜まった未レビューを消化しやすくするため、優先順位とレビュー計画を作る",
      "allowed": [
        "/root/company/obsidian-sync-vault/20_reviews/**",
        "/root/company/obsidian-vault/20_reviews/**",
        "/root/company/obsidian-sync-vault/01_daily/**",
        "/root/company/obsidian-vault/01_daily/**"
      ],
      "forbidden": [
        "未レビュー項目を勝手に[x]へ変更すること",
        "レビュー本文を読まずに採用・差し戻し判断を断定すること",
        "古いレビューを削除すること",
        "機密情報の記載"
      ],
      "doneCriteria": [
        "未レビューを高・中・低の優先度に分類している",
        "今日レビューすべき3件が明確になっている",
        "各レビュー対象の見るべき観点が整理されている",
        "必要なら01_dailyまたは20_reviews配下にレビュー消化計画が作成されている",
        "ExecutionRun POST、20_reviews生成、_review_queue追記、Vault同期、GitHub pushまで完了している"
      ],
      "memo": "今のボトルネックは実装不足よりレビュー滞留。まず基盤系レビューから消化する。"
    }
  ]
}
```
