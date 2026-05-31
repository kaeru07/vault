# レビューキュー

> ChatGPTレビュー用の入口ファイル。
> iPhone Obsidianでは、このチェック状態をレビュー状態の正本として扱う。

## 未レビュー

- [ ] [[2026-05-31_factory-ux]]
  - createdAt: 2026-05-31 15:20
  - app: progress
  - project: progress / AI工場統合 / 工場ページUX改善
  - priority: medium
  - summary: 工場(/epic)・Epic詳細・Automationの初見UXを改善。(1)工場の使い方モーダル(FactoryGuideModal:工場とは/普段の流れ/続きから実行/Codexへ引き継ぐ※自動起動しない/承認待ち/よくあるケースClaude上限→Codex)。(2)現在地表示(FactoryStatusBar:既存health/automation/claude-limit APIを読むだけで🔴Claude上限>🟡承認待ち>🟢作業中>🟣Codex引き継ぎ可能>⚪待機を優先度判定+一言ガイド+CTA遷移)。(3)ボタン説明文(続きから実行『Claudeへ続きを依頼するための情報をまとめます』/Codex引き継ぎ『Claude上限時にCodexへ作業を引き継ぐためのプロンプトを作ります』)。(4)blocked導線改善(AutoFallbackPanelのblockedを『なぜ止まっているか』+理由別ワンタップ遷移:承認/Decision/設定)。(5)/epic上部に運用導線インフォカード。変更禁止範囲(Auto Fallback/Codex安全判定/Approval/Decision/ExecutionRun保存仕様)は不変・新規APIゼロ・既存機能削除なし。検証:build/tsc/lint OK、/epic・/epic/epic-91・/automation全て200+新要素描画、現在地は実データで🟣に解決、モバイル優先。未対応:初回自動表示・実上限時の見え方確認・progressコード未commit(ユーザー判断)。
  - result: 

- [ ] [[2026-05-31_claude-limit-detection]]
  - createdAt: 2026-05-31 14:51
  - app: progress
  - project: progress / AI工場統合 / Claude上限自動検知（Auto Resume前提）
  - priority: high
  - summary: 「Claude上限として扱う」手動ボタン依存を廃止し、vloop実行ログ(stop_reason)/ExecutionRun(errors[]・fallbackReason・runStatus=failed)/Automation Log の3ログから自動判定する層を実装。ExecutionRunは構造化フィールドのみ走査しrawReportの話題語『上限』(実データ38件)で誤検知しない。パターン(rate limit/usage limit/quota/429/limit reached/claude usage limit/5-hour limit/overloaded/和文上限語)一致+failed→high、一致のみ→medium、failedだが上限語なし→low、stop_reason一致→high・上限制限単独→low、automation-log→low。high/medium→detected→既存triggerAutoFallback発火、lowのみ→ambiguous=block_for_review(誤判定回避)。GET=検知のみ/POST=検知→ログ→trigger or force。Auto Fallbackの安全ゲート(evaluateAutoFallback)は不変・前段に追加。手動ボタンはforce上書きで残置。検証:curl 7ケース(none/ambiguous/detected-high/POST自動発火で既存ゲート到達/block_for_review/force)+tsc/build/lint OK、合成データは159runs/automation-log4行へ完全復元。Auto Resume本体は未実装(検知の完成のみ)。progressコードは未commit(ユーザー判断)。
  - result: 

- [ ] [[2026-05-31_auto-fallback]]
  - createdAt: 2026-05-31 01:59
  - app: progress
  - project: progress / AI工場統合 / Phase4 Auto Fallback発火
  - priority: high
  - summary: Claude上限時の半自動Codex引き継ぎを発火実装。Automation『Claude上限として扱う』ボタン→evaluateAutoFallbackが優先順(設定OFF→承認待ち→decisionPolicy≠autonomous→pending_approval→requiresClaude→keyword補助)で厳格判定→安全ならgenerateCodexPromptを自動生成し通知(codex_ready)、不可ならblocked理由表示(approval_required/decision_required/requires_approval/requires_claude/destructive/no_codex_candidate)。automation-log.ndjson(追記専用)にfallbackTriggered/Reason/Target/codexPromptGenerated/sourceRunId/safetyGuard/blockedReason記録。Codex自動起動・CLI直叩き・Hermes不使用。検証:OFF→blocked、実epic-91→blocked(requires_approval[pending3]/destructive=正当)、クリーン一時epic→codex_ready(安全判定付き/```不含/executorUsed=codex指示)、temp削除でデータ復元。tsc/build/lint OK。
  - result: 
- [ ] [[2026-05-31_shikaku-quiz-mvp]]
  - createdAt: 2026-05-31 00:58
  - app: shikaku-quiz
  - project: 新規アプリ / 資格クイズ ITパスポート対策 MVP
  - priority: medium
  - summary: 完全新規(作りかけでない)アプリとして資格学習クイズMVPを静的HTML1枚で実装。ITパスポート風4択12問を自作(過去問転載なし・著作権配慮)、分野選択/シャッフル/即時正誤+解説/分野別正答率/自己ベストlocalStorage/AdSenseプレースホルダ2箇所。外部CDNゼロでオフライン・file://動作。JS構文(vm.Script)・データ整合(12問a範囲内/4択/分野6:3:3)・外部参照ゼロを検証。実機描画は未確認(ユーザー確認待ち)。既存ファイル非変更・.env非参照・公開/課金なし。収益化はWeb公開+AdSense+他資格横展開。本番は問題の内容監修必須。
  - result: 
- [ ] [[2026-05-31_codex-handoff]]
  - createdAt: 2026-05-31 00:25
  - app: progress
  - project: progress / AI工場統合 / Phase3 Codex半自動引き継ぎ
  - priority: high
  - summary: Claude上限時にCodexへ手動でスムーズ引き継ぐ半自動切替を実装。generateCodexPromptが既存正本(ExecutionRun/Decision/NextActions/Approval/Epic/変更ファイル/未完了)から単一プレーンテキスト(冒頭に安全判定固定・入れ子コードフェンスなし・長JSONなし・モバイルコピー対応)を生成。Epic詳細[次回予定]とAutomationに「Codexへ引き継ぐ」パネル(生成元情報+安全判定付き✓+コピー)、Automationに「Codex結果を戻す」フォーム(executorUsed=codex/source=codex_mobile、epicId自動解決)。完全自動切替・Codex CLI直叩き・Hermes外部連携は不使用。tsc/build/lint OK、codex-prompt安全判定付き1628字で```不含、結果戻しE2E(codex_mobile)確認後テストRun削除。Auto Fallback発火とCodex報告構造化パースは後回し。
  - result: 
- [ ] [[2026-05-30_automation-engine]]
  - createdAt: 2026-05-30 23:35
  - app: progress
  - project: progress / AI工場統合 / Phase2 Automation強化
  - priority: high
  - summary: Phase2でAI工場エンジン(Automation)強化。ExecutionRun↔Epic自動結合(resolveEpicId: epicId明示→relatedTodoIds→targetApps、DB変更なし後方互換)をPOSTに組込み、targetApp=ny01/progress・epicId省略のRunがepic-91へ自動付与されることを実証。/operationsを/automationへ307統合し、承認=/approvals・決定=/decisions・Epic=/epic・全体ヘルス=Dashboardへ役割分離(独立プロダクト化しない)。Automation画面に現在状態(running/paused/blocked)・vloop制御・Executor設定(Claude/Codex/Both)・Auto Resume/Auto Fallbackトグル(automation-config.jsonへ永続化、発火は後続)・Codex切替ポリシー(ALLOW/DENY共有定数化)・次回予定・Factory配置を実装。tsc/build/lint OK・全ルート200/307。Auto Resume/Fallbackは設定保存のみで自動発火させず安全側。
  - result: 
- [ ] [[2026-05-30_epic-detail-mvp]]
  - createdAt: 2026-05-30 21:56
  - app: progress
  - project: progress / AI工場統合 / Phase1 Epic詳細
  - priority: high
  - summary: Phase1完了。Epic詳細 /epic/[epicId] をAI工場の主画面として新設(+一覧/epic)。不足はExecutionRun↔Epic結合キーのみと特定し、ExecutionRun.epicId?とEpic.targetApps?を追加(任意・後方互換)で解消(新DB/移行なし)。getEpicDetailは既存正本の読み取り集約のみ(書き込みゼロ)。前回作業/決定事項/次回予定/承認待ち/実行履歴/自動実行状態を1画面化、handoffはUI非表示で「続きから実行」ボタンとして内部利用。Phase0(operationsのhandoffラベル→継続実行情報)も実施。ナビは工場導線追加(BottomNavはAI自走と差し替え)。tsc/build/lint OK・/epic/epic-91 HTTP200全セクション。一時500(前回POSTのnextActionsがオブジェクト配列)はnextActionText正規化で堅牢化。
  - result: 
- [ ] [[2026-05-30_ai-factory-progress-integration]]
  - createdAt: 2026-05-30 21:02
  - app: progress
  - project: progress / AI工場統合設計 / handoff内部化
  - priority: high
  - summary: AI工場を別システムにせずProgressへ進化させる統合路線に確定。データ層は既にhandoff派生済み(generateHandoffView, 正本ではない明記)＝IA問題と特定。handoff2系統(session.handoffText自由文UI露出 / generateHandoffView派生)を整理し、handoffはUIラベル撤去・「続きから実行」アクション化・内部API維持。正本はVault/Progress/ExecutionRun/Decision Logの4固定。Progressを単一シェル化(Dashboard/Epic/ToDo/Approval/Decision/ExecutionRun/Automation/Factory)、operations→Automation+Epic詳細へ解体、ai-driveモック撤去。Epic詳細=縦串/全Approval・Decision・Run=横串。実装Phase0(ラベル撤去・非破壊)→4(Factory)。実装変更ゼロ・方針確定。
  - result: 
- [ ] [[2026-05-29_research-os]]
  - createdAt: 2026-05-29 08:29
  - app: news-app
  - project: news-app / Research OS / Topic横断
  - priority: medium
  - summary: Research DBを構造化Research OSへ。Topic単位URL(/research/topic/[id]:単独+関連Timeline+重複候補)、タグ検索(/research/tag/[tag])、ToDo化導線(/research/todo-candidates:JSON shape)を新設。timelineKey/duplicateKey/similarityHints/sourceDate/relatedTopics/duplicateCandidatesを型・parser・UI・横断集計に追加。evidence/sources metrics、stale判定、confidence UI強化、一覧にtodo/stale件数+タグ雲。Hermes/Codex schema更新。2026-05-26を新フォーマット化(timeline共有/stale demo)。build成功・fallback維持。GitHubミラー未push。
  - result: 
- [ ] [[2026-05-28_research-structured-db]]
  - createdAt: 2026-05-28 22:59
  - app: news-app
  - project: news-app / Research構造化DB / Hermes-Codex接続準備
  - priority: medium
  - summary: Researchを構造化Research DBへ強化。schema拡張(topicId/summaryTlDr/confidence/tags/sourceType/affectsProjects/todoCandidate/updatedAt)、parser強化(日英見出し/[label]URL/sourceType推定/確度正規化/affectsProjectsネスト/yes-no)、TopicCardにTL;DR・確度・タグ・sourceTypeバッジ・ToDo候補・影響PJ追加、一覧/概要にTopic数・S/A・上位タグ・TL;DR表示、Hermes/Codex用テンプレ3点追加。旧フォーマットはfallback維持。build成功。GitHubミラー未push。
  - result: 
- [ ] [[2026-05-28_research-topic-cards]]
  - createdAt: 2026-05-28 21:53
  - app: news-app
  - project: news-app / research詳細表示 / Vault連携
  - priority: medium
  - summary: Vault由来の市場調査Markdownを1トピック=1カードで表示。新フォーマット解析(結論/トピック一覧/ToDo/保留/参考URL)とTopicカードUI(種別・重要度バッジ・要約・収益化示唆・次アクション・参考URL外部リンク・根拠折りたたみ)を追加。解析不能は従来MarkdownViewにfallback。2026-05-27をカード表示で確認、build成功。GitHubミラーは未push。
  - result: 
- [ ] [[2026-05-25_vloop11-vault整備]]
  - createdAt: 2026-05-25 21:38
  - app: company-meta / obsidian-vault / obsidian-sync-vault
  - project: vault / workflow / Issue #59 残 / リンク検査
  - priority: medium
  - summary: vloop11 Vault 整備（Issue #59 残作業実体化）。Vaultの見方ガイド簡略化（1 分索引 + 最新候補状況 + 分割方針 / 破壊的削除なし）+ 全 .md 横断「存在しないパス案内」grep 検査（162 ファイル / 1,454 リンク中 185 件検出 / カテゴリ A-F 分類 + 修正方針案 X/Y/Z 比較 + Claude 推奨案 X）。Issue #59 全体クローズ判断材料が揃った状態に到達。
  - result: 
- [ ] [[2026-05-25_vloop10-aセクション残実体化]]
  - createdAt: 2026-05-25 21:02
  - app: company-meta / obsidian-vault / obsidian-sync-vault
  - project: vault / workflow / candidate-006-007 / 判定基準客観化 / 統合方針 / Mermaid
  - priority: high
  - summary: vloop10 案件別ToDo A セクション残 4 件を実ファイル反映。#68 Mermaid テンプレを candidate-001/005/006/007 本体へ反映（状態色分け 4 件）+ N-03 LLM Chooser 判定基準客観化方針 1 ページ（3 層構造）+ N-04 Vault Search Cheatsheet 統合方針 A/B/C 比較整理（Claude 推奨案 B）+ chatgpt/ フォルダ運用ルール README v1 確定。candidate-006/007 が判定基準客観化方針 + 統合方針推奨案付きで ChatGPT 方向性レビュー可能状態に到達。
  - result: 
- [ ] [[2026-05-25_vloop9-todo-execution]]
  - createdAt: 2026-05-25 12:42
  - app: company-meta / obsidian-vault / obsidian-sync-vault
  - project: vault / workflow / candidate-006-007 / 用語注記 / 案件別ToDo実行
  - priority: high
  - summary: vloop9 案件別ToDo 実行（Issue #80 / #87 継続実体化）。A セクション 4 件を実ファイル反映。A-4-1 旧運用フォルダ個別ファイル注記追加（#59 残解消 / 4 ファイル）+ A-1 candidate-001 本体 + 補助 3 ファイル用語注記（#69 残）+ A-2 candidate-005 本体 + 補助 3 ファイル用語注記（#69 残）+ A-3-1 candidate-006/007 補助 4 ファイル × 2 = 8 ファイル新規（公開ブロッカー / 7 日プラン / progress 投入設計 / ChatGPT 承認パック）。新規 Issue / ToDo を増やさず既存設計を実体化。candidate-006/007 が ChatGPT 方向性レビュー可能状態に到達。
  - result: 
- [ ] [[2026-05-25_issue-80-todo-refactor]]
  - createdAt: 2026-05-25 00:45
  - app: company-meta / obsidian-vault
  - project: vault / workflow / 案件別ToDo一覧 正本化
  - priority: high
  - summary: Issue #80 実行パック。Phase 1-5 を 1 サイクルで実ファイル反映。完了ToDoログ.md 新規（vloop1-8 で達成した 20+ 件移動）+ 案件別ToDo一覧.md 全面リファクタ（A 実行 / B 確認待ち / C 承認待ち / D 完了移動候補 の 4 セクション / 承認のみチェックボックス）+ START_HERE 入口 6→4 件（1-2 タップ）+ 次に実体化するToDo 非推奨化（status: deprecated）+ 重複統合。新規 Issue / ToDo を増やさず既存設計を実体化。
  - result: 
- [ ] [[2026-05-25_vault-inventory]]
  - createdAt: 2026-05-25 00:30
  - app: company-meta / obsidian-vault
  - project: vault / 全体棚卸し / 旧運用整理
  - priority: medium
  - summary: vloop8 Vault 全体棚卸し（Issue #59）。Vault全体棚卸し.md 新規（Phase 1-6・全体マップ Mermaid 図・主要 17 フォルダ実在マップ + 14 主要 .md 正本マップ・旧/新/暫定/削除候補分類・正本マッピング 7 種・iPhone 確認手順 8 ステップ + 復旧手順・完了条件 8/8 達成）。破壊的削除一切なし（注記対応のみ）。04_reviews/ + 07_tasks/inbox/ 個別注記は次サイクル / 「無題のフォルダ」削除はユーザー判断。
  - result: 
- [ ] [[2026-05-24_hermes-codex-integration-research]]
  - createdAt: 2026-05-24 23:40
  - app: company-meta / obsidian-vault
  - project: monetization / ai-workflow / research-automation
  - priority: medium
  - summary: vloop7 Hermes Agent × Codex 組み込み検討 材料整備（Issue #67）。06_research/hermes-agent-codex-組み込み検討.md 新規 10 セクション。5 段階運用ループ + 既存資産接続点（7 資産マップ）+ Hermes 独自価値範囲（SNS 反応・レビュー不満抽出）+ 試用対象 candidate-001 選定（理由 5 件）+ 最小運用フロー 6 ステップ + Hermes 無し迂回ルート + 採用閾値仮説（週 5 本投稿 / 月 8h 手動分析）+ Hermes 出力で approved 化しないルール明記。未確認情報の扱い注記済。Claude 推奨: candidate-001 approved 後に判断。
  - result: 
- [ ] [[2026-05-24_candidate-006-007-and-project-todos]]
  - createdAt: 2026-05-24 23:10
  - app: company-meta / obsidian-vault
  - project: monetization / candidate-006-007 / 案件別ToDo一覧
  - priority: high
  - summary: vloop6 candidate-006（N-03 LLM Chooser / 26-40 / コンパクト版）+ candidate-007（N-04 Vault Search Cheatsheet / 28-40 / 既存資産との重複整理 3 統合方針案付き）として scenarios 正規化。新運用ルール「案件別ToDo運用ルール」準拠の案件別ToDo一覧.md 新規作成（6 案件）。scenarios/README 5→7 件 + idea_trace §8/§9 反映 + 00_START_HERE 入口 5→6 件化（案件別ToDo一覧を最優先①）。補助 4 ファイル × 2 = 8 ファイルは次サイクル（やりっぱなし防止キュー優先 6 登録）
  - result: 
- [ ] [[2026-05-24_vault-accumulation-rule-residual]]
  - createdAt: 2026-05-24 22:35
  - app: company-meta / obsidian-vault
  - project: vault / workflow / 蓄積運用ルール準拠 残作業
  - priority: medium
  - summary: vloop5 Vault 蓄積運用ルール準拠 Epic 残作業。idea_trace（用語注記 + 凡例日本語化 + 1 枚図 classDef）+ Issue完了判定ルール（用語注記 + 状態 6 分類判定フロー Mermaid 新規）+ 試作ループ検証（用語注記 + 1 枚図 classDef）+ 次に実体化するToDo（vloop1-5 反映 + 優先順位再編成 + 優先 4/5 追加）。partial_done を継続管理する運用を実証。
  - result: 
- [ ] [[2026-05-24_vault-accumulation-rule-compliance]]
  - createdAt: 2026-05-24 21:00
  - app: company-meta / obsidian-vault
  - project: vault / workflow / 蓄積運用ルール準拠
  - priority: high
  - summary: Vault 蓄積運用ルール準拠 Epic（#70/#63/#68/#69 一体実装）。次に実体化するToDo.md 新規（planned_only/artifact_exists/user_check/done 分類 + 次サイクル優先キュー + やりっぱなし防止ルール）+ 案の情報源と採用理由.md 新規（idea_trace 日本語エイリアス + 早見表 + APIなし表）+ 現在地図テンプレ.md 新規（Mermaid 標準テンプレ + 状態色分け + 3 種）+ epics.md classDef 追加（テンプレ反映実証）+ 00_START_HERE 日本語化 + 5 入口化 + scenarios/README 用語注記 + candidate-005 並走候補セクション
  - result: 
- [ ] [[2026-05-24_candidate-005-approval-pack]]
  - createdAt: 2026-05-24 20:30
  - app: company-meta / obsidian-vault
  - project: monetization / candidate-005 / approval
  - priority: high
  - summary: candidate-005 承認材料 4 ファイル完備（公開ブロッカー B1-B9 / 7 日実行プラン Day1-7 + candidate-001 並走表 / progress 投入設計 + 3 ToDo + チェックリスト / ChatGPT 承認パック §1-§14 自己完結）。candidate-001 と同水準。status は candidate のまま（pending_approval にも approved にも上げていない / ChatGPT 承認待ち.md にも追加していない・人間判断待ち）。scenarios/README + idea_trace + 00_START_HERE にリンク追加
  - result: 
- [ ] [[2026-05-24_candidate-005-and-n03-n04-prototypes]]
  - createdAt: 2026-05-24 19:20
  - app: company-meta / obsidian-vault
  - project: monetization / prototype-loop / candidate-005
  - priority: high
  - summary: 試作ループ Epic 拡張（vloop2）。candidate-005 として token-speed-tool を scenarios 正規化（28/40 / 6 軸 25/30 / approved 化せず candidate のまま）+ N-03 LLM Chooser 静的 HTML 試作（クイズ 4 種 + 4 LLM カード + 比較表 9×4）+ N-04 Vault Search Cheatsheet 静的 HTML 試作（検索ボックス + 20 件対応表 + iPhone/GitHub Tips）+ idea_trace を 8→10 案体制に拡張 + 試作ループ検証 Phase6 追記 + 00_START_HERE に candidate-005 + 試作モック 3 件入口追加
  - result: 
- [ ] [[2026-05-24_idea-trace-and-token-speed-prototype]]
  - createdAt: 2026-05-24 01:35
  - app: company-meta / obsidian-vault
  - project: monetization / prototype-loop / idea-trace
  - priority: high
  - summary: 試作ループ Epic（#60/#61/#62/#63）一体実装。idea_trace.md（8 案カード+APIなし早見表）+ token-speed-tool.md（trace #62 + MVP 仕様 #60 を 1 ファイル集約）+ 90_prototypes/token-speed-tool/（静的 HTML+JSON モック・外部 CDN ゼロ・サンプル 5 件）+ 試作ループ検証.md（14 案追加 / 3 候補選定 / N-01 試作着地 / Phase1-5 完了）+ 00_START_HERE.md にリンク 3 件追加。10/10 完了条件達成（任意 1 未達）
  - result: 
- [ ] [[2026-05-18_issue2-monetization-design]]
  - createdAt: 2026-05-18 01:26
  - app: company-meta
  - project: monetization / vault設計
  - priority: high
  - summary: Issue #2(収益化自動化運用設計)が誤完了報告だったと確認(8ファイル無/commit無/OPEN)→正規に8ファイル作成しGitHub反映(16d03e4 closes#2)・Issue完了コメント。AI勝手approved化禁止/週1手動定期から段階導入を明記。8/8反映をls-tree+APIで確認
  - result: 
- [ ] [[2026-05-18_radar-deploy-and-todo-reconcile]]
  - createdAt: 2026-05-18 00:38
  - app: ny01/progress
  - project: progress-visibility / radar
  - priority: medium
  - summary: git pull=新規ToDoなし。既実装/radarの残対応をユーザー指示で実施。pm2 restart progress→/radar HTTP200・描画確認(本番反映)。Vault radar ToDoメタを実態done/100%へ整合(外部プロセスの0%注記を承認上書き・要運用整合観察)
  - result: 
- [ ] [[2026-05-17_progress-radar-gantt]]
  - createdAt: 2026-05-18 00:05
  - app: ny01/progress
  - project: progress-visibility / radar
  - priority: high
  - summary: Vault ToDo実行。progressに/radar案件レーダー(横スクロールガント+今やる1件+バー押下モーダル+ダークモード)を追加。既存データ(app-progress/work-queue/execution-runs)から状態/放置日数/次の1手/収益を自動算出、手入力増やさず既存Queue/ExecutionRun/集中作業モード非変更。tsc/lint/build全OK。本番3010目視はpm2 restart要(ユーザー判断)
  - result: 
- [ ] [[2026-05-17_chatgpt-zip-verify-and-vault-reflect]]
  - createdAt: 2026-05-17 19:42
  - app: company-meta
  - project: chatgpt-import / obsidian
  - priority: high
  - summary: obsidian-vault ToDo実行。ZIP正常性6項目検証(586M/unzip -t No errors/conversations-000〜006在)=完全正常。前ターン索引化成果をobsidian-vaultへミラーしpush(e159f99)。sync-vaultに集約3ファイル(アプリ別/収益化/運用改善)追加。元ZIP非削除非移動(ToDo禁止事項遵守)
  - result: 
- [ ] [[2026-05-17_chatgpt-history-indexed]]
  - createdAt: 2026-05-17 18:51
  - app: company-meta
  - project: chatgpt-import / obsidian
  - priority: high
  - summary: 健全化したChatGPT履歴ZIP(613MB/661会話/2023-04-05〜2026-05-16)を一時dirに会話JSONのみ展開し索引化。全文Markdown化せず索引6+重複統合要約8を00_inbox/chatgpt-import/に生成(116KB)。high63/medium153/low445、機密14は値非記載。613MB ZIPはVault外移動推奨(非削除)
  - result: 
- [ ] [[2026-05-17_chatgpt-import-corrupt-zip]]
  - createdAt: 2026-05-17 17:26
  - app: company-meta
  - project: chatgpt-import / obsidian
  - priority: medium
  - summary: ChatGPT履歴ZIPが不完全アップロードで破損(実245MB/欠損351MB/期待613MB)。会話本体復旧不能、救出は末尾メタ3件のみ(会話タイトル無)。機密候補(確定申告書類5件/明細CSV/user.json PII)を検出し展開せず名称のみ記録。ナレッジ6種は捏造回避で未作成、概要mdに失敗根拠と再UP手順を記録。元ZIP非削除
  - result: 
- [ ] [[2026-05-17_progress-stagnation-indicator]]
  - createdAt: 2026-05-17 14:56
  - app: progress
  - project: progress-visibility / vault-todo
  - priority: high
  - summary: Vault inbox high ToDo「progress可視化ダッシュボード改善」に着手。停滞検知を最小改修で実装（getStagnation/sortByAttention純関数追加・◷N日放置バッジ・要注意ソート）。tsc/lint/build全OK、正本JSON非変更。本格改修(ガント/今日の1件)は別ToDo化、残2 ToDoはtriage
  - result: 
- [ ] [[2026-05-17_vault-multiple-todo-review]]
  - createdAt: 2026-05-17 12:43
  - app: company-meta
  - project: workflow / vault-todo
  - priority: high
  - summary: Vault横断の複数ToDo（00_inbox投函箱 / 途中プロジェクト一覧 / progress改善候補 / idle-backlog）を再読み取りし優先順位1本化。high=progress操作盤化2件、medium=連携CLI/ポータルLP/市場調査メモ、投函APIは消化済でclose提案。前回成果物が痕跡ゼロだったため推測せず再算出
  - result: 
- [ ] [[2026-05-16_vault-inbox-token-setup]]
  - createdAt: 2026-05-16 19:36
  - app: progress
  - project: workflow
  - priority: high
  - summary: VAULT_INBOX_TOKEN本番設定+pm2 restart+疎通確認(tokenなし401/ありで保存成功)。token値非露出・正本JSON無変更・再起動でvault-inbox等が本番反映
  - result: 
- [ ] [[2026-05-16_vault-inbox-hardening]]
  - createdAt: 2026-05-16 19:03
  - app: progress
  - project: workflow
  - priority: high
  - summary: vault-inbox API強化(VAULT_ROOT/VAULT_GIT_BRANCH環境変数化・main固定撤廃・投函ログndjson)。隔離検証で秘密非記録/git失敗時保存成功/本物vault非汚染を実証。ny01コードのみpush 7ab08fe
  - result: 
- [ ] [[2026-05-16_vault-inbox-api]]
  - createdAt: 2026-05-16 18:24
  - app: progress
  - project: workflow
  - priority: high
  - summary: POST /api/vault-inbox 追加(Bearer認証→obsidian-vault/00_inboxへMD保存+git push試行)。ny01(PUBLIC)へコードのみpush(338e5f1)・正本JSON無変更実証・検証テストMD掃除
  - result: 
- [ ] [[2026-05-16_chatgpt-inbox-import]]
  - createdAt: 2026-05-16 17:11
  - app: progress
  - project: workflow
  - summary: ChatGPT投函箱→progress取り込み機能を実装 (00_inbox走査→ToDo化でpending_approval追加 / 内容ハッシュ冪等 / 隔離検証済・本番無影響・要再起動)
  - priority: medium
  - result: 
- [ ] [[2026-05-16_service-map]]
  - createdAt: 2026-05-16 11:35
  - app: company-meta
  - project: workflow
  - priority: medium
  - summary: company 全体を7サービスに分割した俯瞰資料 docs/service-map.{html,json} を新規作成 (読み解きコスト削減 / 実装変更なし)
  - result: 
- [ ] [[2026-05-16_codex-sdk-experiment]]
  - createdAt: 2026-05-16 02:01
  - app: progress
  - project: workflow
  - priority: medium
  - summary: Codex SDK 読み取り専用最小実験 (CLI利用可/Python公式SDK無→subprocess方式 / execution-runs抽出→codexレビュー / 本体無影響・書込なし検証済)
  - result: 
- [ ] [[2026-05-16_scrape-lab-v2-postproc]]
  - createdAt: 2026-05-16 01:50
  - app: scrape-lab-v2
  - project: app-strategy
  - priority: medium
  - summary: scrape-lab-v2 v1 後処理 (private GitHub管理化 commit 2e3e761 / 20_reviews追記 / 02_apps新設 / v2候補整理)。新機能なし
  - result: 
- [ ] [[2026-05-15_progress-codex-experiment]]
  - createdAt: 2026-05-15 22:17
  - app: progress
  - project: workflow
  - priority: high
  - summary: progress に Codex CLI 連携の最小実験機能を追加 (read-only / 1件ずつ / 確認ダイアログ / status・実行・一覧API / /codex 画面 / codex-runs.json 保存)
  - result: 
- [ ] [[2026-05-15_scrape-lab-v2-init]]
  - createdAt: 2026-05-15 12:36
  - app: scrape-lab-v2
  - project: app-strategy
  - priority: high
  - summary: scrape-lab-v2 新規作成 v1 (Next.js 14 + Cheerio / SSRF ブロック / 差分検知 / JSON・Markdown export / Claude・Codex レビューコピー欄 / iPhone 対応)
  - result: 
- [ ] [[2026-05-15_idle-work-backlog]]
  - createdAt: 2026-05-15 00:06
  - app: company-meta
  - project: app-strategy
  - priority: high
  - summary: Claude/Codex 余剰リソース消化用 作業バックログ（既存棚卸し / 新規 30 案 / Shorts / Play・Web / ASO / ToDo / ランキング 4 種）
  - result: 
- [ ] [[2026-05-14_local-first-app-ideas]]
  - createdAt: 2026-05-14 23:47
  - app: company-meta
  - project: app-strategy
  - priority: high
  - summary: ローカル完結・高継続 Google Play アプリ案 30 件 + ランキング 4 種 + 本命 3 件（打牌セルフ採点 / 朝散歩カレンダー / 庭タイムラプス）
  - result: 
- [ ] [[2026-05-14_claude-task-template-update]]
  - createdAt: 2026-05-14 23:21
  - app: company-meta
  - project: obsidian-workflow
  - priority: medium
  - summary: claude-task-template を最新運用へ更新（完了マーカー 7 項目 / Vault 同期チェックリスト / 20_reviews・_review_queue 連動）
  - result: 
- [x] [[2026-05-14_push-required-rule]]
  - createdAt: 2026-05-14 09:09
  - app: company-meta
  - project: obsidian-workflow
  - priority: high
  - summary: commit 後の push 必須化ルール追加 + global git push deny 解除（push 完了 = 同期完了の運用統一）
  - result: 
- [x] [[2026-05-14_add-review-queue-createdat]]
  - createdAt: 2026-05-14 08:13
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: medium
  - summary: _review_queue.md に createdAt フィールド追加（既存 4 件補完 + README / template / CLAUDE.local.md ルール反映）
  - result: 
- [ ] [[2026-05-14_clarify-commit-hashes]]
  - createdAt: 2026-05-14 01:36
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: medium
  - summary: 20_reviews commitHashes 運用整理（意味明確化 + reviewFileCommit 任意フィールド追加・既存遡及なし）
  - result: 
- [x] [[2026-05-14_review-queue-setup]]
  - createdAt: 2026-05-14 01:26
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: medium
  - summary: 20_reviews レビューキュー運用追加（_review_queue.md 新設・運用ルール反映）
  - result: 
- [ ] [[2026-05-14_fix-commit-hashes]]
  - createdAt: 2026-05-14 01:13
  - app: obsidian-vault
  - project: obsidian-workflow
  - priority: low
  - summary: 20_reviews 自己参照修正（commitHashes に push commit 72c5f3f 反映）
  - result: 
- [ ] [[2026-05-14_review-file-rule]]
  - createdAt: 2026-05-14 00:56
  - app: company-meta
  - project: obsidian-workflow
  - priority: high
  - summary: 20_reviews 自動生成ルール追加（8 セクション + ChatGPT 依頼文 + 完了マーカー 7 項目化）
  - result: 

## レビュー済み

- [x] [[サンプル]]
  - app: 
  - project: 
  - priority: 
  - summary: 
  - result: ok

## 今後の運用ルール

Claude / Codex / Claude Code が `20_reviews/YYYY-MM-DD_<slug>.md` を生成したら、**必ず同時にこの `_review_queue.md` の「未レビュー」へ 1 件追加**する。

追加形式:

```markdown
- [ ] [[YYYY-MM-DD_<slug>]]
  - createdAt: YYYY-MM-DD HH:mm
  - app: <対象アプリ / company-meta 等>
  - project: <プロジェクト名 / 略称>
  - priority: high | medium | low
  - summary: <1 行要約>
  - result: 
```

`createdAt` はレビュー Markdown を生成した時刻（VPS のローカル時刻 = JST）を `YYYY-MM-DD HH:mm` で記入する。runId のタイムスタンプ部、または対象作業の commit 時刻から逆算してもよい。`reviewedAt` は導入しない（`[x]` の有無をレビュー済み判定の正本とする方針を維持）。

## ChatGPT レビュー後の運用

ユーザーが iPhone Obsidian で対象行を以下のように変更する:

変更前:
```
- [ ] [[YYYY-MM-DD_<slug>]]
```

変更後:
```
- [x] [[YYYY-MM-DD_<slug>]]
```

このチェック状態をレビュー済み判定の**正本**として扱う。各レビュー Markdown の frontmatter `status:` は必須更新にしない。

要対応は `result: needs_followup` と書く。

## ChatGPT 側の想定運用

依頼例（ユーザーから）:

> 「レビューキュー見て、未チェックのものをいつもの観点でレビューして」

ChatGPT 側の処理順:

1. GitHub の `kaeru07/vault` を見る
2. `20_reviews/_review_queue.md` を読む
3. `[ ]` 未チェック項目をレビュー対象候補にする
4. `app` / `project` / `priority` / `summary` を見て対象を絞る
5. リンク先 `20_reviews/YYYY-MM-DD_<slug>.md` を読む
6. いつもの観点でレビューする

## レビュー観点（ChatGPT 共通）

- 抜け漏れ
- 進め方の妥当性
- 危険な変更
- 既存運用との矛盾
- 未承認 ToDo に着手していないか
- pending_approval / approved / queued の混在
- progress.md や正本を壊していないか
- build / TypeScript / lint / 主要画面確認が未実施なのに OK 扱いされていないか
- 追加で ToDo 化すべき問題
- 次に進めるべき作業

## 重要ルール

- `_review_queue.md` のチェック状態をレビュー状態の**正本**にする
- iPhone で軽く運用できることを最優先
- `04_reviews/` は重要レビューの**手動キュレーション**用として維持
- 既存レビュー本文は削除しない
- GitHub 公開前に秘密情報チェックを行う
- `.env` / `token` / `cookie` / API キー / 本番 DB 接続文字列 は絶対に commit しない

## 関連

- [[README]]
- [[../03_prompts/Claude-Code標準運用]]
- [[../90_templates/session-review-template]]
- `/root/company/CLAUDE.local.md` § レビューキュー（_review_queue.md）連動
