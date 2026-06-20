# レビューキュー

> ChatGPTレビュー用の入口ファイル。
> iPhone Obsidianでは、このチェック状態をレビュー状態の正本として扱う。

## 未レビュー

- [ ] [[2026-06-20_mahjong-codemagic-yaml-fix]]
  - createdAt: 2026-06-20 23:11
  - app: mahjong
  - project: codemagic.yaml更新(workflow ios-capacitor-test)＋No configuration file found解消
  - priority: high
  - summary: Codemagicが Branch:main で「No configuration file found」になる原因=codemagic.yamlがios-codemagic-testブランチのみでmainに無い。ユーザー新仕様に合わせcodemagic.yaml刷新: workflow id ios-capacitor-test/name iOS Capacitor Test、mac_mini_m2/xcode latest/node latest/cocoapods default、integrations.app_store_connect:ASC_API_KEY追加、vars(BUNDLE_ID/XCODE_PROJECT ios/App/App.xcodeproj/XCODE_SCHEME App)、scripts(npm ci||install→build→cap sync→keychain initialize/app-store-connect fetch-signing-files $BUNDLE_ID --type IOS_APP_STORE --create/keychain add-certificates/xcode-project use-profiles→build-ipa --project --scheme App)、artifacts ipa+xcodebuild log、publishing auth:integration submit_to_testflight:true。重要差異=Capacitor8はSwiftPMでApp.xcworkspace無し→ユーザーテンプレの--workspaceを--projectに変更。秘密情報非記載。検証=build OK/cap sync OK/yamlルート/workflow ios-capacitor-test/integrations有/build-ipa --project/秘密スキャンclean。commit Add Codemagic iOS build configuration→push origin ios-codemagic-test(219c1a2)・origin確認。未対応=実ビルド/署名/TestFlightはCodemagic・main未反映(意図的)。ユーザー次手=CodemagicでBranchをios-codemagic-testに変更→Check for configuration files→integration名一致→Start build。確認観点=SPM構成の--projectビルド妥当性/fetch-signing-files --create+integrationでTestFlight到達十分か/main非配置運用の是非。
  - result: 

- [ ] [[2026-06-20_mahjong-ios-codemagic]]
  - createdAt: 2026-06-20 21:54
  - app: mahjong
  - project: 麻雀アプリをCapacitorでiOS化＋Codemagic(TestFlight)ビルド準備
  - priority: high
  - summary: レンタルMac無しでCodemagicのクラウドMacでiOS(TestFlight)ビルドする準備。/root/mahjong/mahjong(Next.js16/React19/App Router・API route無し全8ページStatic)をbranch ios-codemagic-testで対応。next.config.tsにoutput:export/images.unoptimized/trailingSlash統合→out/8html生成。Capacitor8(core/cli/ios 8.4.1)導入＋capacitor.config.ts(appId com.kaeru07.mahjong/appName Mahjong/webDir out)。npx cap add ios/sync成功。重要=Capacitor8はCocoaPodsでなくSwiftPM構成(Podfile/.xcworkspace無し・ios/App/App.xcodeproj＋CapApp-SPM/Package.swift)。共有スキーム不在→App.xcscheme(BlueprintId 504EC3031FED79650016851F)を新規作成しCIでscheme App解決可能に。codemagic.yaml新規(mac_mini_m2/xcode latest/node22/cocoapods default(SPMのため実使用なし)、ios_signing app_store/bundle com.kaeru07.mahjong、npm ci→build→cap sync→xcode-project use-profiles→build-ipa --project ios/App/App.xcodeproj --scheme App、artifacts ipa+xcodebuild log、publishing app_store_connect auth:integration submit_to_testflight:true)。秘密情報非記載・ASC integrationはUI登録前提。検証=npm ci/build/cap sync全OK・out8html・ios/App.xcodeproj・webDir out・public同期・秘密スキャン実値なし。自分の変更25ファイルのみcommit(BoardView/AGENTS/CLAUDE除外・ios配下public/生成configはgitignore)。git push -u origin ios-codemagic-test成功(kaeru07/mahjong)。未対応=実iOSビルド/署名/IPA/TestFlightはCodemagic・Apple Developer/ASC API Key登録はユーザー手動。確認観点=SPM構成のbuild-ipa妥当性/cocoapods:default残置の無害性/auth:integrationでTestFlight到達十分か/Next16 output:exportのWebView注意点。
  - result: 

- [ ] [[2026-06-20_integration-map-button]]
  - createdAt: 2026-06-20 19:25
  - app: progress
  - project: 司令塔トップに対応表ページへの一時ボタンを配置
  - priority: low
  - summary: ユーザー要望「progressアプリから遷移できるように一時ボタンも配置」に対応。app/page.tsxのReviewCopyButton直後に/integration-mapへのLink(インディゴ枠・🗺旧Vault→今のゴール運用 対応表・「一時 →」バッジ)を追加。統合完了後撤去予定とコメント明記。☰メニュー掲載と併用。検証tsc0/build0/lint0・/ 200でボタン文言と/integration-map href描画確認。commit fd38e09 push済。未対応=統合完了後にボタン/ページ撤去。確認観点=導線の置き場所・一時バッジで意図が伝わるか。
  - result: 

- [ ] [[2026-06-20_integration-map-page]]
  - createdAt: 2026-06-20 18:11
  - app: progress
  - project: 旧Vault→今のゴール運用 対応表ページ(一時)をprogressアプリに追加
  - priority: medium
  - summary: ユーザー指示「対応表作って、一時的にprogressアプリから見れるようにして」に対応(統合ゴール①の成果物)。app/integration-map/page.tsx新設(server component): 一時ページ説明＋対応表10行(旧Vault→今のprogress→所見)＋正本の寄せ先(実行/キュー/ToDo/進捗/完了=progress、Vault=判断ログ/レビュー入口)＋統合の進め方①〜⑥をgoals.jsonとタイトル先頭丸数字で照合し状態バッジ(承認待ち/実行中/後で/完了)を動的表示＋ゴール承認リンク＋承認待ち件数(試した方がいい系/アプリ系)。iPhone向け横スクロールテーブル＋簡潔レイアウト。lib/nav-menu.ts「計画・候補」に/integration-map追加(☰・/legacyから到達・孤立防止・一時ページ明記)。検証tsc0/build0/lint0・/integration-map 200で全セクション描画・/legacy掲載確認。commit 5bd5d69 push済。判断=一時ページのため/guide・TERMS・operating-modelフルセット更新スキップ(撤去前提・nav掲載のみ)・正本資料は06_research点検doc。未対応=②〜⑥は承認後・統合完了後にページ撤去予定。確認観点=対応表の粒度/所見の抜け漏れ・運用ドキュメント更新スキップの是非・②から着手すべきか。
  - result: 

- [ ] [[2026-06-20_priority-code-to-human]]
  - createdAt: 2026-06-20 16:35
  - app: progress
  - project: 統合ゴールを平易な名前に改名＋アプリ全体のP0/P1/P2表記を高/中/低へ統一
  - priority: medium
  - summary: ユーザー指示「progressに寄せる方針でよい/P1P2の用語は分かりにくいのでアプリ上もこの言葉を使わないように変換」に対応。(1)ゴール改名: enrich-proposals APIにnewTitle/summary/cons編集を追加し【運用統合P1〜P6】を①旧Vaultと今のゴール運用を見比べてまとめ方を決める〜⑥移行後の古い運用メモを片付ける の平易な日本語へ改名(P番号除去updated6)。(2)アプリ全体のP0/P1/P2廃止: lib/epic-priority-label.ts新設(P0→高/P1→中/P2→低・値据え置き)。司令塔/自動実行キュー(フィルタ/チップ/カード/最優先で次回実行ボタン/スコア要因reasonFactors)/Epic詳細/おすすめEpic/承認パネル/Epic作成フォーム/JSON取込プレビュー/legacy/guide FAQの優先度表示を高/中/低へ。reasonFactorsの生P0はauto-queue-score.ts:139とauto-queue.ts:279の2経路修正。検証tsc0/build0・/ /queue /decide /legacy /recommended-epics 200でP0/P1/P2リーク0・高/中/低描画・ゴール承認①〜⑥表示。commit a5fc103 push済。残置(意図的)=JSON記入例placeholder/コピー用プロンプト文のP表記(AI向け)/EpicPriority型自体。方針=正本progress集約を承認(06_research点検doc追記)。未対応=統合ゴール①の実移行は承認後・task優先度英語表示統一は別軸。確認観点=P表記の見落とし有無/コピー文のP残置の是非/task優先度の日本語化要否。
  - result: 

- [ ] [[2026-06-20_legacy-ops-integration]]
  - createdAt: 2026-06-20 14:42
  - app: progress / Vault運用
  - project: 旧Vault運用と現ゴール運用の統合点検＋段階別統合ゴール6件を承認へ追加
  - priority: high
  - summary: ユーザー要望「旧運用のvault等を再点検し最新のゴール運用へ統合・段階に分けてゴール承認へ追加・今点検しきってもよい」に対応し点検をやり切り。旧Vault運用(05_monetizationのcandidate-001〜007/Epic A-D/収益化ロードマップ＋20_reviewsのChatGPT承認待ち/vloop_queue/案件別ToDo一覧/案件別ゴール進捗/完了ToDoログ＋vloopスキル＋GitHub Issue＋ChatGPT承認)と現progressゴール運用(Goal/ゴール承認/auto-queue/factory-runner/ExecutionRun)が『候補→承認→キュー→実行→完了→進捗』で並行2系統(旧は〜2026-05-25 vloop11で停滞)であることを確認。概念対応表を作成し正本寄せ先を確定(実行/キュー/ToDo/進捗/完了=progress、Vault=判断ログ/ナレッジ/ChatGPTレビュー入口)。点検doc=06_research/2026-06-20_旧Vault運用と現ゴール運用の統合点検.md。段階別統合ゴール6件をゴール承認(アプリ系)へ追加(P1対応表確定/P2 candidate→Goal移行/P3 ToDo・キュー一本化/P4 承認フロー一本化/P5 vloop位置づけ確定/P6 旧ドキュメントarchive・索引現行化、各enables/pros/cons付)。検証=/api/goals/propose created6/skipped0・/decide?tab=goalApproval 200で統合P1〜P6描画。proposed43(試した方がいい系16+アプリ系27)。未対応=P1〜P6実移行は承認後。確認観点=正本をprogressに寄せる方針の妥当性/P順序・粒度/vloop廃止orprogress連携/candidate移行の取捨軸。
  - result: 

- [ ] [[2026-06-20_try-goals-enrich]]
  - createdAt: 2026-06-20 14:15
  - app: progress
  - project: 試した方がいい系(残16件)に「試すと何ができる/どんな良い影響か」を詳細追記＋enrichエンドポイント新設
  - priority: medium
  - summary: ユーザー要望「残した試した方がいい系に、もっと試したらどういい影響があるか・どんなことができるかを詳細に追記」に対応。対象16件はenables/prosが空(normalizeGoal脱落バグで消失)で承認カードが薄かった。app/api/goals/enrich-proposals/route.ts新設(items[{title,enables,pros[],detail}]をproposedへタイトル一致で適用・detailはnotes先頭へ🔍試す価値マーカー付き追記)。16ツール個別に詳細作成し適用(updated16/notFound0)。各々ユーザー文脈に接続(Claude Code/Agent SDK=工場自律度・changedFiles根治、Codex SDK/Goal mode/Appshots=Fallback自動化・ゴール自走・UI自動検証、Codex Sites/Google AI Studio=収益化LP・プロト高速化、GitHub MCP secret scanning=push前機密チェック自動化、Figma MCP=iPhone UI改善、Remote MCP=モバイル↔VPS、MCPセキュリティ=連携安全土台、ADK/AI Studio=新規収益アプリの種)。検証tsc0/build0/lint0・/decide?tab=goalApproval 200で「できるようになること/メリット/🔍試す価値/収益化ロードマップに直結」描画確認。commit 0bc6394 push済。未対応=アプリ系27件への同様肉付け・文面の質確認。確認観点=各ツールの記述が承認判断に役立つか/環境紐づけの的確さ/アプリ系にも詳述すべきか。
  - result: 

- [ ] [[2026-06-20_research-goals-triage]]
  - createdAt: 2026-06-20 14:00
  - app: progress
  - project: 試した方がいい系ゴールのトリアージ（★5に理由追記・★4以下を後でへ）＋triageエンドポイント新設
  - priority: medium
  - summary: ユーザー指示「試した方がいい系で特に試した方がよさそうなものは理由追記、あとででも良さそうなものは後で扱いに変更」に対応。app/api/goals/triage-research/route.ts新設。research系proposedを対象に★5(priority high)=承認待ち維持+notesへ理由追記(⭐特に試した方がいい:調査で最高評価★5・cue…Claude/Codex/MCP中心の開発に直結…重複追記なし)、★4以下=status=paused(後で・保留)へ変更、剥がれたproposalSource=research修復。実行結果enriched=16/movedLater=45/repairedSource=61。後で(paused)は削除でなく目標タブで再有効化可。検証tsc0/build0/lint0・/decide?tab=goalApproval 200で試した方がいい系16件・理由描画確認。commit 3dbe800 push済。未対応=後で(paused)を承認画面内『あとで』タブで可視化する案・★5/★4基準の妥当性確認。確認観点=★5/★4境界の妥当性/後で=paused運用の是非/理由文(テンプレ寄り)の質。
  - result: 

- [ ] [[2026-06-20_goal-approval-categories]]
  - createdAt: 2026-06-20 13:59
  - app: progress
  - project: ゴール承認のカテゴリサブタブ分割＋progress改善ゴール27件追加＋proposalSource保持バグ修正
  - priority: high
  - summary: ユーザー要望「progressの運用/機能/操作フローを深く見直し改善ゴールを多数追加・ゴール承認を試した方がいい系/アプリ系でサブタブ分割」に対応。command-center proposalCategoryOf(research=試した方がいい系/他=アプリ系)+InboxCard.proposalCategory付与、InboxTabs goalApprovalにサブタブ(すべて/試した方がいい系/アプリ系・client state goalCat)追加。コード精査で見えた実在の改善点27件を/api/goals/propose(source=app_improvement)で一括追加(自動実行RunのchangedFiles10/74・selection5/74・nextActions1/74記録不足解消、ゼロダウンタイムデプロイ=build中_error.jsクラッシュ事故、一括承認/検索UI、stale自動修復、doneCriteria精度点検、iPhone表示点検、成功率/Executor統計、危険語ゲート点検等)。さらにnormalizeGoalがproposalSource/proposedAt/approvedAt/proposalEnables/Pros/Consを返さずread→writeのたびに提案元が脱落するバグを発見・修正(27件追加時のreadで既存61件のresearch source消失で発覚)。検証tsc0/build0/lint0・/decide?tab=goalApproval 200でサブタブ・アプリ系ゴール描画・proposalSource分布app27/research61(修復後)確認。commit 3dbe800 push済。未対応=一括承認UI・iPhone実機。確認観点=try/app2分類で十分か/27改善ゴールの粒度優先度/normalize他フィールド脱落の有無。
  - result: 

- [ ] [[2026-06-20_research-goals-bulk]]
  - createdAt: 2026-06-20 12:55
  - app: progress
  - project: 日々調査のゴール承認候補を一括追加（58件）＋一括追加エンドポイント新設
  - priority: medium
  - summary: ユーザー要望「日々調査のゴール承認をできるだけ多く追加」に対応。既存自動提案は承認待ち上限3・maxDays3・max3で絞られ★4以上ユニーク61件中3件しか入らなかった。app/api/goals/propose-research/route.ts新設(buildResearchGoalCandidates+proposeGoalsを上限無視で呼ぶ手動トリガ・既定maxDays60/max100・既存同名除外)。実行しcreatedCount=58(proposed 3→61)。候補はAIツール「試す/調査する」系(Claude Codeを試す/OpenAI Codex SDKを試す等)。検証: tsc0/build0/lint0・/decide?tab=goalApproval 200で新候補描画確認。commit 1f2c612 push済。注意=proposed61で自動の調査/アイドル提案(上限3)は承認で減るまで停止(設計通り)。未対応=大量承認候補の一括承認/絞り込みUI。確認観点=proposed61運用の妥当性/自動上限3据え置き+手動大量追加の切り分け/一括承認UIの要否。
  - result: 

- [ ] [[2026-06-20_autoexec-report-content]]
  - createdAt: 2026-06-20 12:55
  - app: progress
  - project: 自動実行レポートの中身を充実（何をしたかが分かるよう結果/対象/やったことを常時表示）
  - priority: medium
  - summary: ユーザー指摘「自動実行レポートが内容書いてなさすぎる・できたことに『ファイル変更履歴なし』とあるが何をしたか分かるようにして」に対応。実データ74runでchangedFilesは10件だけ、summary74全件/stopReason72/selectionありを未活用だった。AutoExecReport.tsxにdescribeOutcome(stopReason→「何をしてなぜ終わったか」の人間語訳・blocked/no_candidate/epic_done/continue/approval_required/max_runs_reached/rate_limited/run_failed等)とdidRealWorkを追加し、記事を①結果(outcome+summary常時)②対象と選定理由(selection)③やったこと(changedFiles列挙orsummary/outcome説明・コード変更なしの回も理由付き明示)④課題⑤検証⑥次⑦詳細に刷新。cleanRawReportで[factory-runner]/[factory-schedule]/executor=機械プレフィックス除去。検証: tsc0/build0/lint0・/guide?tab=report 200で新セクション(やったこと×25記事相当/結果/「コードを変更する作業はしていません」/「起動条件を満たさず」)描画確認。commit 1f2c612 push済。未対応=factory-runner側のchangedFiles記録不足は別途・iPhone実機確認。確認観点=各記事で何をしたか掴めるか/stopReason人間語訳の自然さ/changedFiles薄さの根本を直すべきか説明側で十分か。
  - result: 

- [ ] [[2026-06-20_system-spec-autoexec-flow]]
  - createdAt: 2026-06-20 09:26
  - app: progress
  - project: システム仕様ページを自動実行フロー中心に再構成＋調査/ゴール生成モードを明記（iPhone向け折りたたみ）
  - priority: medium
  - summary: ユーザー要望「自動実行時の調査もシステム仕様にまとめて/システム仕様ページが縦長で分かりづらい・iPhone意識で縦長にしすぎないで/一番知りたいのは自動実行の処理フローだからその方針でまとめ直して」に対応。SystemSpecification.tsxを全面再構成: 旧16セクション縦積み(344行)→(A)ヘッダ (B)主役「自動実行で行われる処理(11/14/16/23時)」を全体像+12ステップ番号リストで先頭固定(②調査/③-bゴール生成モードをタグで明示) (C)参照系(安全条件/Runner・Executor/キュー並び順/状態モデル/正本データ/API/画面/用語/設計原則・互換・変更時チェック)をdetailsアコーディオンで既定折りたたみ→iPhone初期スクロール短縮。実フローをrunScheduledFactory→runFactory実装どおり正確化(lock→ON確認→危険判断スキップ→①Review Fix→②調査ゴール提案proposeGoalsFromResearchIfNeeded→③Epic選定→③-a次の一歩ensureNextGoalStepEpic→③-bゴール生成モードproposeImprovementGoalsIfIdle→④Claude実行(上限→Codex)→⑤検証+ExecutionRun→⑥doneCriteria→⑦Prompt Queue→⑧Envelope Run)。guide systemタブ説明をフロー中心に更新+FAQ「作業が無くなったら」追加、command-center TERMS.proposedGoalを調査(外)/ゴール生成モード(中)2系統に更新、current-operating-model.md frontmatter+パイプライン節更新(CLAUDE運用ドキュメント4点セット更新)。検証: tsc0/build0/lint0/pm2 restart後 /guide?tab=system 200で新フロー・調査・ゴール生成モード・折りたたみ描画と旧16セクション削除を実描画確認・他タブ200回帰なし。commit b4cf4ca push済。未対応=iPhone実機での折りたたみ操作感・折返し最終確認。確認観点=12ステップの過不足/参照系アコーディオン既定折りたたみの妥当性(重要情報が隠れすぎないか)/調査・ゴール生成モード2系統整理の分かりやすさ。
  - result: 

- [ ] [[2026-06-20_factory-idle-improvement-goals]]
  - createdAt: 2026-06-20 07:35
  - app: progress
  - project: 自動実行アイドル時のゴール生成モード（progress優先で承認対象を補充）
  - priority: medium
  - summary: ユーザー要望「自動実行対象が無かった時、ゴール生成モードに変えてprogress優先で改善事項や試した方がいいことをゴール承認対象へ追加し、次の自動実行が空にならないように」に対応。lib/improvement-goals.ts新設(proposeImprovementGoalsIfIdle): アイドル時(!scan.picked)に優先順=(1)失敗/エラーRunの解消[改善事項・後に成功Run無しのみ]→(2)未消化nextActionsのゴール化[試した方がいいこと]→(3)progress改善seed[空回避の保険3件]でstatus=proposed候補を実生成(上限3・全status重複除外・progress優先・source=factory_idle_improvement)。factory-runnerのアイドル分岐に配線し実登録、従来のプロンプトのみrequestGoalProposalIfIdleは補助フォールバック化。GoalStatusは既存proposed再利用(VALID配列変更なし)。検証: tsc0/next build0/lint0。現在proposed=3(上限)で「上限尊重で新規提案せず」が正しく動作・失敗Run1件/223runsのnextActionsが補充ソースとして存在を確認。実生成は実データ汚染回避のため未実行。commit c650726 push済。未対応=proposed枠<3での実機候補生成確認・seed3件内容精査(ユーザー判断)・/guide反映。確認観点=ソース設計(失敗→nextActions→seed)と優先順の妥当性/空回避保証とノイズ提案のバランス(上限3で十分か)/nextActionsゴール化の粒度問題。
  - result: 

- [ ] [[2026-06-19_autoexec-report-filter-format]]
  - createdAt: 2026-06-19 23:21
  - app: progress
  - project: 自動実行レポートに期間/状態フィルタ＋詳細レポートの整形
  - priority: low
  - summary: ユーザー指示「フィルタと整形やって」に対応。AutoExecReportに期間フィルタ(7日/30日=既定/全期間)＋状態フィルタ(すべて/完了/一部完了/失敗)をチップ(Link)で追加、/guide経由で?range=?status=をURL同期・該当件数表示・0件時ガイド文。詳細レポート全文はcleanRawReport()でrawReportの「progressレビュー用」ブロック以降とmonetizationImpact/theme/obsidianSummary/obsidianSaveTarget/reviewStatus/runId/changedFiles等の機械メタ行を除去し、連続改行で段落分割して<p>表示(旧<pre>ベタ貼り廃止)。検証: tsc0/next build0/guide?tab=report フィルタ描画(期間7/30/全・状態すべて/完了/一部/失敗・該当件数)/range=7・status=failed 200/詳細レポートにprogressレビュー用・monetizationImpact・obsidianSaveTarget残らないこと(0件)確認。commit 196035d push済。未対応=Goal/Epic別フィルタ未・cleanRawReportは固定キー除去(網羅的でない可能性)・実機確認未。確認観点=既定30日の妥当性/cleanRawReport除去対象の網羅性(消しすぎ残りすぎ)/フィルタ軸(期間状態)で十分か。
  - result: 

- [ ] [[2026-06-19_autoexec-report-per-run]]
  - createdAt: 2026-06-19 22:57
  - app: progress
  - project: 自動実行レポートを「1実行＝1記事」形式に（できたこと/できなかったこと/詳細全文）
  - priority: low
  - summary: ユーザー指示「もっと詳細に、一実行につき1ページぐらいの量で記事のようにできたこと・できなかったことを深く」に対応(日次から1実行=1記事へ変更)。components/operations/AutoExecReport.tsxをRunArticle(1自動実行=1記事)に再構成、新しい順・最大25件。各記事: ヘッダ(#連番/タイトル/状態バッジ/日時/Epic/経路source/実行者executorUsed)・概要(summary)・✅できたこと(完了表示or変更ファイル列挙file—change最大12)・⚠️できなかったこと課題(errors/warnings、未完了はreviewMemo/errorsを理由に)・🔍検証結果(checks build/tsc/lint/手動)・→次にやること(nextActions)・詳細レポート全文(rawReportをdetailsで全文max-h-96)。全体リード(稼働状態/累計/成功率/承認待ち・進行中)は冒頭維持。検証: tsc0/next build0/guide?tab=report 200・各記事に全セクション描画(25記事)。commit 4c320a1 push済。未対応=rawReport機械情報の整形未・個別実行ページ(/guide/report/[runId])化未・アーカイブ済み古い実行は対象外・実機確認未。確認観点=1記事の情報設計(できたこと/できなかったことの切出し妥当性)/rawReport全文表示の是非(整形要否)/25件連続表示でよいか(個別ページ・ページング要否)。
  - result: 

- [ ] [[2026-06-19_autoexec-report-detail]]
  - createdAt: 2026-06-19 22:19
  - app: progress
  - project: 自動実行レポートを記事スタイルの詳細1ページに刷新
  - priority: low
  - summary: ユーザー指示「自動実行レポートをもっと記事みたいに一ページ使って細かく詳細に残して」に対応。components/operations/AutoExecReport.tsxを拡張: ①リード文(稼働状態/累計実行回数/成功率/ゴール提案・次の一歩回数/承認待ち・進行中件数/記録期間を文章で)②数値サマリー(計/完了/一部完了/失敗/成功率/提案・次の一歩)③日次タイムライン(直近40件を日付ごとにグループ化し各実行をRunDetailカードで 概要/変更ファイル(file—change最大8)/検証(checks:build/tsc/lint/手動)/エラー・警告/次のアクション/時刻・epicId・source を詳細表示)④自動化ログ20件。ExecutionRunのsummary/changedFiles/checks/errors/warnings/nextActions/epicId/sourceを活用。検証: tsc0/next build0/guide?tab=report 200・サマリー/日次タイムライン/変更ファイル(12)/成功率/自動化の動き描画。commit c93b43e push済。未対応=期間フィルタ(7/30/全)未(直近40件固定)・実機の読みやすさ確認未。確認観点=記事スタイルの情報設計(読み物として把握できるか/冗長すぎないか)/直近40件固定でよいか期間フィルタ要否/各実行の詳細項目の粒度。
  - result: 

- [ ] [[2026-06-19_bottomnav-6tabs]]
  - createdAt: 2026-06-19 22:09
  - app: progress
  - project: モバイル下タブを主要6タブのみに整理（合計6・均等配置、moreItemsは☰へ集約）
  - priority: low
  - summary: ユーザー指示「全体のタブに加えて。6つにして合計」に対応。components/navigation/BottomNav.tsxから旧moreItems(横スクロール全画面25件列挙)を廃止し、主要6タブ(ホーム/ToDo/Project/目標/自動実行/運用)をflex-1 min-w-0で均等配置(横スクロール撤廃)。それ以外の全画面は右上☰メニュー(HamburgerMenu/正本lib/nav-menu.ts)に集約。安全確認: 削除前にmoreItems25件すべてが☰メニュー掲載済みを照合(孤立0)、削除後も到達照合スクリプトで全33実ページが6タブ+☰+リダイレクト2(/pending,/operations)で到達可能と確認。検証: tsc0/next build0/到達照合PASS/pm2再起動後ホーム200・下タブに6ラベル描画・旧moreItemsラベル(作業予約/工場Epic/おすすめEpic/旧ダッシュ)非表示(0件)。commit 235d43a push済。未対応=実機での6タブ収まり(文字切れ)・☰からの全画面到達の通し確認は未(curl+照合で代替)。確認観点=下タブ6絞り＋残り☰集約の妥当性(よく使う画面が隠れないか)/端末幅での6タブ収まり(ラベル短縮要否)。
  - result: 

- [ ] [[2026-06-19_ops-autoexec-report-tab]]
  - createdAt: 2026-06-19 21:48
  - app: progress
  - project: 運用ページをタブ化し「自動実行レポート」タブを追加
  - priority: medium
  - summary: ユーザー指示「運用タブをメイン画面に追加して、その中に自動実行内容の報告タブを作って」に対応。運用は既に下タブ/上タブの主要タブに追加済み。app/guide/page.tsxを?tab=reportで分岐するタブUI(GuideTabBar: 使い方ガイド/自動実行レポート)に。新規components/operations/AutoExecReport.tsx(server)でAI工場の状態(稼働/停止)・次回予定(11/14/16/23 JST)・自動実行サマリー(完了/一部完了/失敗/計)・直近の自動実行12件・自動化の動き(factory_goal_proposal_requested/factory_goal_step_epic_created/factory_dispatch等の最近ログを人間語ラベルで)・承認待ちゴール候補件数→/decide?tab=goalApprovalリンクを表示(readExecutionRuns/getAutomationLog/readGoals/getAutomationConfig使用)。guide FAQ追加。検証: tsc0/next build0/guide?tab=report 200・全セクション描画・白画面なし。commit c914da5 push済。未対応=次回実際に動くEpic(選定候補)の表示は未・実機最終確認は未。確認観点=自動実行レポートの情報設計(社長がひと目で把握できるか)/次回選定候補を出すべきか/ログイベント→人間語ラベルの網羅性。
  - result: 

- [ ] [[2026-06-19_proposed-fix-bulk-import]]
  - createdAt: 2026-06-19 13:00
  - app: progress
  - project: 【重大修正】proposed読込時active矯正バグ修正＋ChatGPT一括取込＋手動追加に任意プロンプト
  - priority: high
  - summary: ユーザー報告「ゴール承認にみつからない」「openclew等を承認前に戻して」の根本原因を特定・修正。原因=lib/goal-reader.tsのnormalizeGoalがVALID_STATUSES('proposed'未含有)でstatus検証し、読込時にproposed→activeへ矯正していた→ゴール承認タブ(status===proposedでフィルタ)が常に空・承認ゲート素通り。VALID_STATUSESに'proposed'追加で修正(修正前goalProgress=7=全active扱い→修正後4=proposed3除外、/decide?tab=goalApproval approveURL=6・できること/メリット/デメリット描画)。あわせてChatGPT一括取込を実装: validateGoalImport/importGoal(goal-writer)でprojectId任意化(未存在は警告)・phases未指定時に既定フェーズ自動生成、/api/goals route を{goals:[...]}/配列/単一すべて受ける複数取込に拡張(集約レスポンス)、GoalPlannerFormに「📋ChatGPT用プロンプトをコピー」ボタン＋ゴール+ToDo生成テンプレ(既存案件埋め込み)・複数取込結果(createdCount)対応、手動直接追加の欄を「プロンプト・説明(任意)」に。検証: tsc0/next build0/複数ゴール一括E2E({goals:[A,B]}→createdCount2/todo3/projectId任意/phases自動/未存在projectIdは警告のみ、テスト削除)/gp decide 200。commit 53c6dc8 push済。未対応=JSON一括は直active(手動直接追加はproposed経由)・承認カード実機最終確認は未。確認観点=VALID_STATUSES漏れの再発防止(GoalStatus定義とreader/writerのVALID配列の単一ソース化)/一括取込を承認経由に統一すべきか/import緩和の妥当性/ChatGPTテンプレ出力安定性。
  - result: 

- [ ] [[2026-06-19_goal-approval-unify]]
  - createdAt: 2026-06-19 08:45
  - app: progress
  - project: ゴール追加を「追加→承認」に統一＋承認カードを詳細化(できること/メリット/デメリット)
  - priority: medium
  - summary: ユーザー指示「追加したら基本的にゴール承認タブで承認ボタンを押したら追加されるように／承認タブにできるようになることやメリット・デメリットを詳細に」に対応。GoalPlannerFormの直接追加をupsertSingle(即active)からPOST /api/goals/propose(source=manual・status=proposed)へ変更=手動追加もゴール承認タブ経由で承認して初めてactiveになるよう統一(追加=即active廃止)。Goalにproposal Enables/Pros/Cons追加(types/goal.ts)、ProposeGoalInput+proposeGoals(goal-writer)で反映、buildResearchGoalCandidates(research-goals)が研究候補にenables/pros/cons自動付与。buildInbox(command-center)のproposedGoalsカードを✅できるようになること/👍メリット/👎デメリット・注意/承認すると/やめるとの詳細表示に拡張(無ければ既定文)。InboxActionsのrow ddにwhitespace-pre-lineで箇条書き改行保持。ボタン文言「承認して追加する」。検証: tsc0/next build0/手動propose API E2E(status=proposed・source=manual・proposalEnables反映を実測)/.next/serverに新カード文字列(できるようになること/デメリット・注意/承認して追加する/ゴール承認へ追加)含有確認/goal-planner decide 200/テストゴール削除。commit ed43306 push済。注意=goalApprovalタブはclient描画でSSR grepはemoji付きラベル検出しづらく実機headless最終確認は未(build文字列+E2E代替)。未対応=JSON一括追加は直active(承認経由未)・既存active化済み候補(OpenClaw等)は遡及変更なし・候補メリデメ文の質改善。確認観点=追加=即activeを追加→承認に変えた設計の妥当性/承認カードの情報設計と自動生成文の質/JSON一括追加も承認経由にすべきか/proposed activeの扱いとqueue非表示の安全性。
  - result: 

- [ ] [[2026-06-19_goal-form-simplify-guide-tab]]
  - createdAt: 2026-06-19 07:57
  - app: progress
  - project: ゴール直接追加フォーム簡素化(タイトルのみ必須)＋運用ページを主要タブへ昇格
  - priority: medium
  - summary: ユーザー指示「タイトルだけ必須／プロジェクトは未設定でも良い／status・decisionPolicy・riskFlag・指標は消す／プロンプトは任意。あと全体のタブに運用ページを追加」に対応。components/goals/GoalPlannerForm.tsxの「ゴールを直接追加」をタイトル(必須)/案件(任意)/説明=prompt(任意)の3項目に簡素化(priority/status/decisionPolicy/riskFlags/metric/今(current)/目標(target)/notesの入力欄・state・submit body・リセットを削除、未指定はupsertSingleGoal既定=status active/priority medium/autonomous/target100・current0で補完)。BottomNav.tsxに運用(/guide)を主要アイコンタブとして追加(6タブ化・moreItems重複除外)、TopNav.tsxの新ナビに運用追加＋/guideをNEW_ROUTESに追加。検証: tsc0/next build0/pm2再起動後 /goal-planner 200で「タイトル *」「案件(任意)」「説明(任意)」のみ描画・decisionPolicy/riskFlags/指標は非表示/guide・ホーム200で運用タブ描画/タイトルのみ直接追加API E2E(upsertSingle({title})→status=active・priority=medium既定補完を実測、テストゴール削除)。commit 4e1a3c8 push済。未対応=進捗(今/目標)は既定0/100で作成され後から編集する導線は未整備・実機通し確認は未(API代替)。確認観点=直接追加を3項目に絞った設計と既定値補完の妥当性/進捗を後から編集する導線の要否/運用を主要タブ昇格(6タブの収まり)。
  - result: 

- [ ] [[2026-06-19_goal-approval-tab]]
  - createdAt: 2026-06-19 00:44
  - app: progress
  - project: 今日の判断(Inbox)に「ゴール承認」タブを新設＋初回候補を生成
  - priority: medium
  - summary: ユーザー指示「todoにgoal承認タブを追加／自動実行時に追加したゴール候補の承認依頼を入れて／初回として候補出して」に対応。components/newux/InboxTabs.tsxのTabKeyにgoalApprovalを追加し、tabFromQuery/タブ配列(ゴール承認・count=承認待ち件数・alert付き)/quickFilters/tabCounts/filteredProposedGoalsを追加。提案ゴール(inbox.proposedGoals)の描画を「今日の判断」タブ上部のセクションから専用タブ(?tab=goalApproval)へ移設、スコープ/検索フィルタ対応・空状態ガイド付き。自動実行履歴(autoRuns)は今日の判断に残置。初回としてproposeGoalsFromResearchIfNeeded(PROGRESS_DATA_PATH=data/real)を実行し調査結果から3件をproposed登録(MCPセキュリティ対策カテゴリを試す[P0]/OpenClawを試す/MCP Appsを調査する・source=research)。承認すると既存フロー(active+autonomous→ensureNextGoalStepEpic)で自動実行対象。検証: tsc0/next build0/pm2再起動後 /decide?tab=goalApproval 200・「ゴール承認」タブ＋「自動実行が提案した目標」見出し＋候補3件描画。commit b6b730a push済(コード/ドキュメントのみ・data/realは実行時データのため除外、候補は稼働アプリに反映済み)。未対応=定時実行冒頭の候補補充ライブ確認未(手動生成で代替)・候補タイトルの冗長語・モバイル下タブからの直接導線未。確認観点=ゴール承認を専用タブに分離した設計(今日の判断との役割分担)/初回候補の質・タイトル整形要否/data/realに候補直接生成し保持した判断/モバイル到達性。
  - result: 

- [ ] [[2026-06-19_manual-goal-add]]
  - createdAt: 2026-06-19 00:08
  - app: progress
  - project: ゴール手動追加を直接追加/JSON一括追加に整理・分解プロンプト生成UIを廃止
  - priority: medium
  - summary: ユーザー指示「ゴールを完全に手動で追加できるように(直接追加とjson追加)、プロンプト生成はいらない」に対応。components/goals/GoalPlannerForm.tsxから分解プロンプト生成(目標を入力する→プロンプト生成→コピーのStep1/2)と関連state/関数(goalText/monetizationFirst/phaseHint/generatedPrompt/promptCopied/buildPrompt/handleCopyPrompt/copy/fallbackText)・未使用useMemoを削除。残すのは①ゴールを直接追加(項目入力1件・upsertSingle)②JSONで一括追加(validate→import)。直接追加にmetric/今(current)/目標(target)入力を追加し、SingleGoalInput+upsertSingleGoal(lib/goal-writer.ts)にmetric/target/currentを反映=進捗(=current/target)を手動設定可能に。単体promptラベルは説明(任意)に変更、見出しを「ゴールを直接追加」「JSONで一括追加」に整理。検証: tsc0(未使用localなし)/next build0/pm2再起動後 /goal-planner 200で直接追加・JSON追加・今(current)・目標(target)描画、分解プロンプト/プロンプトをコピーは非表示(0件)/guide 200/直接追加API E2E(upsertSingle current3 target12→goals.json反映→/api/auto-queueで3/12=25%実測、テストゴール削除)/機密scan clean。commit d73816b push済。未対応=直接追加は初期todo自動生成あり(抑制オプション未)・実ブラウザ通し操作未(API代替)。確認観点=プロンプト生成廃止と2方式整理の妥当性/target&current手動設定→進捗反映の設計/初期todo自動生成を残すか抑制するか/JSON一括追加スキーマ説明の十分性。
  - result: 

- [ ] [[2026-06-18_research-goal-proposal]]
  - createdAt: 2026-06-18 22:13
  - app: progress
  - project: ゴール提案の抽出元を日々の調査結果に強化（優先順処理＋達成後に次提案）
  - priority: high
  - summary: ユーザー指示「自動実行はゴール優先順で処理／ゴールが終わったら次のゴールを提案／自動実行の最初にnewsアプリ等の日々の調査結果を参照し効果がありそうなものからゴール作成(例Fableを試す)」に対応。lib/research-goals.tsを新設し、news-app/content/research/daily-ai-tools/YYYY-MM-DD.md(直近3日・RESEARCH_CONTENT_PATHで変更可)の「## 導入価値評価」をパース→★4以上(即試したい/即調査/必須/PoC向き)を効果がありそうなこととして抽出→○○を試す/調査するゴール候補(★5→P0/★4→P1)を生成(buildResearchGoalCandidates)。proposeGoalsFromResearchIfNeededが承認待ち<3件のときproposeGoals(source=research)で登録(既存ゴール全status同名除外=承認済/却下済を蒸し返さない)。runFactory(lib/factory-runner.ts)のauto+confirm冒頭で呼ぶ=自動実行の最初に調査からゴール提案。優先順処理は既存rankGoals/ensureNextGoalStepEpic、優先順消化で承認待ちが減ると次回また補充=ゴール達成後に次提案。承認は今日の判断(Inbox)🎯ゴール承認→active+autonomousで自動実行対象。検証: tsc0/next build0/実データ抽出ドライラン(OpenClawを試す/MCP Appsを調査する/MCPセキュリティ★5/OpenAI Codex SDK★5等生成)/E2E(PROGRESS_DATA_PATH=data/realでproposeGoalsFromResearchIfNeeded→goals.json proposed(source=research)3件→/decide🎯ゴール承認描画→両データパスから後始末削除proposed残0)/decide guide 200/機密scan clean。commit 7705246 push済。注意=tsx素実行はdata/sample既定(本番data/real)。未対応=抽出はdaily-ai-tools中心(market-research/ai-news未拡張)・定時実行ライブ確認未・タイトルに冗長語混じる場合あり。確認観点=抽出ロジック(導入価値評価★4+)の妥当性と拡張元/達成後提案を承認待ち<3補充で実現する設計vs明示的goal-done検知/タイトル品質/承認=active+autonomousの安全性/収益化medium妥当性。
  - result: 

- [ ] [[2026-06-18_goal-proposal-approval]]
  - createdAt: 2026-06-18 12:58
  - app: progress
  - project: ゴール提案→承認→自動実行フロー新設（自動実行中AI提案＋Inbox承認＋自動実行履歴）
  - priority: high
  - summary: ユーザー指示(段階的明確化)に対応。抽出元=自動実行中にAIがその場で考えて提案、承認=今日の判断(Inbox)を選択。Factoryがauto+confirmでアイドル時にrequestGoalProposalIfIdle(lib/goal-proposal.ts)が提案依頼プロンプトを生成し自動化ログfactory_goal_proposal_requestedに記録(承認待ち3件で打ち止め)。実候補生成はexecutor(Claude/Codex)がPOST /api/goals/proposeを呼ぶ前提(FactoryはLLM非搭載=依頼発行まで)。proposeGoals(lib/goal-writer.ts)がstatus=proposed/decisionPolicyDefault=autonomousで登録(同名skip)。buildInbox().proposedGoalsが今日の判断(Inbox)上部「🎯ゴール承認」カード(cap無し)、承認→POST /api/goals/[id]/approve→setGoalApprovalでactive(=自動実行対象)、却下→dropped。active+autonomousになると既存ensureNextGoalStepEpicが次の一歩Epicを自動生成して達成まで進める。buildInbox().autoRuns(factoryRun/source直近10件)をInbox下部「🤖自動実行の履歴」に表示。proposedはbuildGoalProgress/goal-planner一覧から除外。Inboxは既存判断パイプライン非変更の追加方式・承認カードは既存InboxCardItem汎用action再利用。GoalStatusにproposed追加。検証: tsc0/next build0(propose/approve API生成)/E2E(POST propose→goals.json proposed登録→/decideに🎯ゴール承認カード→approve→status=active+autonomous実測、テストゴール削除)/Inbox🤖自動実行の履歴描画/decide 200・白画面なし/機密scan clean。commit c91e5ab push済。未対応=実候補生成はexecutorがpropose呼ぶ前提(自動呼出し連携は未実装)・定時実行のライブ確認は未(API直叩き代替)。確認観点=Factory依頼発行とexecutor実生成の分担の妥当性/自動propose連携の実装方法/承認=active+autonomousで即対象化する安全性(危険フラグ)/proposedをqueue目標タブ除外の設計/Inbox追加方式の判断/収益化medium妥当性。
  - result: 

- [ ] [[2026-06-18_goal-progress-fix]]
  - createdAt: 2026-06-18 08:58
  - app: progress
  - project: ゴール進捗0%問題の修正（進捗率を「今/目標」target/currentに統一）
  - priority: medium
  - summary: 調査(runId 20260618-012023)で特定した0%問題の修正。原因=buildGoalProgress が ratio=done/total(todos＋キューアイテム数のみ)で算出し goal.target/current を無視→全ゴール todos=0・数値指標管理のため done≒0→全ゴール0%。ユーザー選択「一番単純: 今/目標の数字で出す」に基づき、進捗率の正本をgoalAchievement(target>0→round(min(100,current/target*100))、target無→Todo完了率)に統一。buildGoalProgress(lib/auto-queue.ts・/queue/目標タブ)とbuildGoalProgressCards(lib/command-center.ts・司令塔/現状未使用)を変更し、表示done/totalもtargetありならcurrent/target(％と一致)。旧フォールバック(Todo→紐付Epic平均→数値指標)廃止＝作業の進み具合は進捗率に使わない。検証: tsc0/next build0/pm2再起動後 /api/auto-queueで実測 100%(60/15)/13%(10/80)/33%(30/90)/0%(0/5)(旧 全0%)・/queue /goal-planner 実描画200・白画面なし。commit 851b7c5 push済。未対応=goal-ai-factory-os current60>target15(上限100%)のスケール是正(データ運用判断)。確認観点=進捗の正本を今/目標に固定する設計の妥当性(作業進捗を使わない判断)/target未設定ゴールのTodoフォールバック/done&total表示とratioの整合/進捗計算の単一ヘルパ集約の要否。
  - result: 

- [ ] [[2026-06-18_goal-progress-zero-investigation]]
  - createdAt: 2026-06-18 01:20
  - app: progress
  - project: 調査 — 自動実行のゴール進捗が0%になる原因解析
  - priority: medium
  - summary: ユーザー依頼「なぜ自動実行のゴール進捗が0%か原因解析」。根本原因=表示進捗 buildGoalProgress().ratio(lib/auto-queue.ts:328-371)が done/total(todos＋キューアイテム数のみ)で算出し goal.target/current(数値指標)を一切使わない。4ゴールは全て todos=0・target/current管理(15/60,80/10,90/30,5/0)、開いてるEpicはstatus=doneにならず完了Epicはitemsから除外→done≒0→全ゴール常に0%(/api/auto-queueで実測 ratio:0×4)。target/currentを使うgoalAchievement(goal-reader.ts:99)は存在するが表示に未使用。表示経路 goal-planner/page.tsx:195・GoalListItem.tsx:23 が queueProgress?.ratio??calcGoalProgress().ratio で両方とも数値指標不使用。運用docの「todo→Epic平均→数値指標」フォールバックの数値指標が未実装。すべきこと=①buildGoalProgressをdoc通りのフォールバックに(todo完了率→Epic doneCriteria完了率平均→goalAchievement) ②Epic進捗はdone件数でなくdoneCriteria完了率平均で算出 ③進捗計算を単一関数に集約し二系統の不整合解消 ④データ是正(goal-ai-factory-os current60>target15→cap100%) ⑤進捗の正本(数値指標 vs 作業完了)の方針確定。調査のみ・ファイル変更なし。確認観点=進捗の正本選定/フォールバック設計/単一化の設計/target&currentの意味づけ。
  - result: 

- [ ] [[2026-06-18_hamburger-nav]]
  - createdAt: 2026-06-18 00:58
  - app: progress
  - project: 全画面ハンバーガーメニュー(☰)新設・全ページ到達保証
  - priority: medium
  - summary: ユーザー指示「存在するページはタブかハンバーガーから遷移できるように／遷移しづらい画面は困る」に対応。全ページをカテゴリ別に一覧する☰ドロワー(HamburgerMenu.tsx)をヘッダー右(モバイルfixed/デスクトップflex両方)に常時設置。現在地ハイライト・画面遷移で自動クローズ・背面スクロールロック。メニュー項目の正本lib/nav-menu.ts(NAV_GROUPS/ALL_NAV_LINKS)を新設し、画面一覧/legacyのインライン定義を削除して同一正本を参照(二重管理解消)。到達照合スクリプトで全33実ページ中31がメニュー/タブ掲載・残2(/operations→/automation,/pending→/tasks)はリダイレクトのみ＝孤立0を機械確認。BottomNav/TopNavの既存タブは近道として残置。検証: tsc0/next build0/到達照合PASS/pm2再起動後 / /legacy /usage /queue 実描画200・☰両クラスタ描画・/legacy正本参照で全カテゴリ描画・白画面なし・チャンク200。commit 98c10a5 push済。確認観点=単一正本nav-menu.tsによる到達保証設計の妥当性/モバイル・デスクトップ両対応のハンバーガーUX/既存タブ(BottomNav横スクロール約30項目)との役割重複整理/新ページ孤立を防ぐ運用ルール・自動チェックの組み込み方。実機通し確認は未(curl/スクリプト代替)、BottomNav横スクロール項目削減は残置。
  - result: 

- [ ] [[2026-06-17_usage-tracking]]
  - createdAt: 2026-06-17 22:13
  - app: progress
  - project: 使用状況ページ(/usage)新設 — 画面別アクセス/ボタン操作TOP/最終使用/放置検知
  - priority: medium
  - summary: ユーザー要望「progressの使用を細かく記述して把握したい」→選択肢で「実際の使用状況を記録・可視化」を選択。横断キャプチャ方式(既存画面・ボタンの改修ゼロ)で実装。layout常駐UsageTracker(client)がpage_view(usePathname変化)とaction(button/[role=button]のdocument委譲click・文言80字)を記録し、navigator.sendBeacon→POST /api/usage→usage-store.tsでusage-log.ndjson(PROGRESS_DATA_PATH配下・既存ログと独立)へ追記。/usage(server/force-dynamic)はbuildUsageSummary(7)で 画面別アクセス回数(直近7日・棒グラフ)/よく使うボタン操作TOP(最大20)/画面別最終使用日時(全期間)/放置画面(直近7日未アクセス) を表示。画面レジストリ正本lib/usage-screens.ts(ルート→人間語・詳細ページは親に丸め)。導線=BottomNav/TopNav「使用状況」。data-usage-ignore/data-usage-labelで個別制御可。表示専用で判定・スコア・実行に非干渉。検証: tsc0(Map iterator spread→Array.from修正)/next build0(/usage・/api/usage生成)/pm2再起動後 /usage実描画200・全セクション描画・チャンク200・白画面なし。/api/usageにpage_view×2/action×1 POST→usage-log.ndjson 3行追記→/usage集計反映(自動実行画面回数・最優先ボタン操作)確認。機密scan clean。commit c6108ab push済。確認観点=document委譲click横断キャプチャの妥当性と副作用(他clickハンドラ干渉/性能)/sendBeacon記録の取りこぼし/ログ肥大対策(ローテーション・上限)要否/全button記録のノイズ絞り込み/プライバシー(UI文言記録)。実ブラウザのsendBeacon自動記録はcurl POSTで代替検証(headless実click未実施)。
  - result: 

- [ ] [[2026-06-17_goal-auto-advance-resume]]
  - createdAt: 2026-06-17 13:43
  - app: progress
  - project: ToDo/Epic無しの未達成Goalを「達成まで自動で進める」(type=goal + 次の一歩Epic自動生成)
  - priority: medium
  - summary: 中断作業の再開・完了。前セッションで未コミット・未検証だった goal-resume 機能を検証・確定。AutoQueueItem.type に 'goal' を追加し、buildAutoQueue で「ToDo も open Epic も無い未達成 active Goal(達成率<100%)」を type='goal'「○○(達成まで自動で進める)」としてキュー投入。runFactory が auto+confirm かつ実行可能 Epic 0 件のとき新規 ensureNextGoalStepEpic(lib/goal-step-epic.ts)が Goal順最上位の対象 Goal に「次の一歩」Epic(epic-goalstep-<id>)を1つだけ idempotent に自動生成→再scan、達成まで繰り返す。承認要/手動/危険 riskFlags の Goal は type=goal で出すが自動実行せず解消手順(Inbox承認/Goal詳細)を表示。read経路/dry_run/manualでは生成しない。/queue は type=goal でも pin/hold 許可。運用ドキュメント4点(guide FAQ/TERMS goalStepEpic/operating-model本文+用語+frontmatter+変更履歴)更新。検証: tsc0/next build0/pm2再起動後 /queue /guide 実描画200・goalアイテム「達成まで自動で進める」6件ライブ表示・チャンク200・白画面なし・/guide新FAQ/用語描画。機密scan clean。commit 13766cb push済。無関係な未コミット残置(verify-todos等)は本作業では触らず残置。確認観点=step-epicの無限増殖防止(idempotent=1Goal同時1つ)/承認要・手動・危険Goalの自動実行されない安全設計/「実行可能Epic0件時だけ生成」トリガの飢餓・暴走両リスク/既存Goal順・自走化アンカーとの優先順位整合。step-epic自動生成はライブ未観測(現状実行可能Epicありfallback未到達)。
  - result: 

- [ ] [[2026-06-17_queue-goal-ordering]]
  - createdAt: 2026-06-17 01:06
  - app: progress
  - project: 自動実行キューをGoal順で並べる（表示＋実行）
  - priority: medium
  - summary: ユーザー要望「自動実行キューにGoalを表示・Goal順でキュー順が決まる」。スコープ確認で「表示＋実行の両方」を選択。共有ヘルパrankGoals(pin>boost>優先度>goals.json安定順)を新設し、compareItems(auto-queue・表示)とscanFactoryDispatch(factory・実行next選択)の両ソートに同一階層を適用: ①明示pin ②安全枠(要修正/自走化アンカー) ③Goal順 ④Goal内(手動順→score→優先度)。/queueをGoalグループ表示化(Goalヘッダに進捗/boost/このGoalだけ/Goal詳細導線、未紐付けは末尾「Goal未設定」)。自走化アンカー(goal-ai-factory-os)/pin/要修正はGoal順より上位据置で飢餓回避(既存boost値据置)。データスキーマ変更なし(読み取り順のみ)。guide/operating-model更新。検証: tsc0/build0/pm2再起動後 /queue /guide 実描画200+チャンク200/rankGoals順序規則 独立テストPASS。現状キュー0件のためグループ表示はライブ未観測(空状態は正常描画)。commit 4bd93f9 push済。確認観点=表示順と実行next選択順の一致/自走化アンカーの飢餓有無/Goal順規則がユーザーの並べ替え期待に合うか/既存pin・hold・manualOrderとの整合。
  - result: 

- [ ] [[2026-06-16_goal-todo-queue-integration-full]]
  - createdAt: 2026-06-16 22:50
  - app: progress
  - project: Goal/Todo/Queue/JSON取込のデータ統合（フル・8項目）中断実装の検証＋確定
  - priority: high
  - summary: 実装前監査(20260616-184355)でスコープ判断待ちだったGoal/Todo/Queue統合が、未コミット作業ツリーに着手済み・未検証で中断していた実体を特定。フル(8項目)スコープをユーザー承認のうえ検証→選択コミット→push完了。Task/NewTaskInputにgoalId/phaseId、GoalTodoにsource/queueControl/decisionPolicy/riskFlags、AutoQueueItemにtodoId/source enum、controlにprioritize/complete追加。lib(progress-writer/goal-reader/goal-writer/auto-queue/command-center)・api(tasks/goals/auto-queue control)・UI(TodoManager/JsonImportManager/GoalPlannerForm/queue/tasks/import)を統合フィールド対応。Todoを選択Goalに紐づけ二重Todo系統(GoalTodo埋込 vs project-tasks)を解消。変更は加算的で後方互換。検証: tsc0 OK/next build OK/pm2 restart後 /queue /tasks/import /goal-planner 実描画200+参照チャンク200(白画面なし)/機密scan clean(ヒットはauth_secret enumのみ)。ランタイムデータ蓄積(execution-runs.json等)・別アプリ(ai-trend-sns/news-app)の未コミット変更は対象外で残置。残: ny01ドキュメントセット(guide/page.tsx・current-operating-model.md)追補・API機能スモーク。commit 452f1eb push済。確認観点=二重Todo系統解消の設計妥当性/後方互換(既存project-tasks Todo)/Goal-linked Todoの保存先(goals.json vs project-tasks.json)/既存機能非破壊。
  - result: 

- [ ] [[2026-06-16_progress-filter-ui]]
  - createdAt: 2026-06-16 09:06
  - app: progress
  - project: 全画面 表示専用URL同期フィルターUI追加（Phase1 Codex／Phase2 Fable close_ok）
  - priority: medium
  - summary: queue/Inbox(decide)/tasks/goal-planner/portfolio に「画面操作でき・URLに同期し・再読込/共有で復元できる」表示専用フィルター(チップ+ドロワー+検索+件数表示+0件空状態ガイド)を追加。判定/スコア/Factory実行順は不変(表示層のみ)。Phase2 Fable独立レビュー=close_ok・実装是正不要。検証: tsc0/lint0/build成功・判定/スコア系(auto-queue-score/auto-queue/factory-dispatch/factory-runner/factory-schedule/review-fix/prompt-queue)git diff無変更=表示専用担保・deep link9件全200(?tab/goalId/focusRunId/filter=needs_followup/reviewStatus互換維持)・フィルタ実効性(全1件中: /queue=1, excluded(候補外)=0+空状態, filter=executable=1+選択中chip, q=nomatch=0+空状態)・件数「全N件中M件表示」/URL復元/×クリア動作確認・機密scan clean。leftover/data/realはcommit除外。commit cb56917 push済。確認観点=表示専用に閉じているか/deep link後方互換/URL同期設計の取りこぼし/0件導線の実用性/iPhone操作性。
  - result: 

- [ ] [[2026-06-16_autonomy-anchor-impl-and-review]]
  - createdAt: 2026-06-16 00:20
  - app: progress
  - project: 自走化Goal最優先アンカー化（Phase1 Codex実装／Phase2 Fableレビュー close_ok）
  - priority: high
  - summary: 設計レビュー(20260615-212004)に基づくEpic実装＋実装後レビュー。既存goal-ai-factory-os(AI工場OS自走化/North Star)を正本に、epic-91をP0/factoryEligible=true/doneCriteria7件の実行アンカーEpic化し『pin済みだが候補外』を解消。buildAutoQueue(表示)とscanFactoryDispatch/runFactory(実行)を共通の自走化アンカー判定で最優先化(順 pin>review-fix>autonomy>通常P0)し表示=実行一致。ExecutionRun.selection記録(Goalは最小ポインタ)・/api/operations/goal-ai-factory-os-next-run-selection-test(実関数read-only)・completed時メール通知土台(noop/log・実送信未導入)。Phase2 Fableレビュー結論close_ok＋1修正: no-op通知が実送信フラグautonomyNotifiedAtを消費し将来実メールが永久に飛ばないfootgunを、autonomyNotifyNoopAt分離＋実送信はAUTONOMY_EMAIL_PROVIDER構成時に成功後のみnotifiedAtに是正。検証PASS: tsc0/lint0/build成功・next=epic-91/candidateEligible=true/pinnedExcluded=[]・dry-runでbuildAutoQueueとscanFactoryDispatch一致・Running時wouldRunNext=true/Blocked時false。安全(危険/判断要/二重送信)維持。epic-91契約はruntime data(commit対象外)。commit bc31630 push済。確認観点=epic-91 ID固定アンカーの堅牢性・永久ピン飢餓対策・表示=実行boost規則一致・通知idempotency再設計の穴・epic-91 doneCriteria自己参照でcompletion成立するか。
  - result: 

- [ ] [[2026-06-15_autonomy-goal-priority-design-review]]
  - createdAt: 2026-06-15 21:20
  - app: progress
  - project: 設計レビュー 自走化ゴール最優先＋達成メール通知＋dry-run（実装前）
  - priority: high
  - summary: Fableが設計レビュー(実装なし)。自走化を次回自動実行の最優先固定＋達成時メール(toku106ma@yahoo.co.jp)＋dry-run検知 のCodex実装前レビュー。結論=条件付きGo。重大懸念3: (1)Goal単体は実行されない→doneCriteria付きP0 Epic必須(epic-91と同じ対象外化) (2)終わらないゴールの永久ピンで他作業飢餓 (3)新規factory-autonomyは重複正本→既存goal-ai-factory-os『AI工場OS自走化』(active/main)再利用。抜け漏れ=buildAutoQueue(表示)とscanFactoryDispatch/runFactory(実行)の2系統に両方反映必須/completion判定主体未定義/factory現在Blocked(危険承認5件)でdry-runはwouldRunNext=false。データ=selectionはExecutionRunに集約・Goalは最小ポインタ(二重管理回避)。メール=email依存なし新規/done永続化後・未通知時のみ・autonomyNotifiedAt不可逆で二重送信防止・try/catch隔離・秘密.env(.env.exampleは変数名)・dry-runから送らない・リトライ上限3。dry-run=実選定関数read-onlyでドリフト防止。Codex完成版プロンプト(目的/前提/OK禁止/role/手順10/検証/報告)提示。確認観点=Goalでなく実行Epic必須の妥当性・永久ピン飢餓防止の十分性・2系統反映漏れ・メールidempotencyと順序・completion定義の妥当性・プロンプト過不足。ユーザー判断待ち=自走化の具体doneCriteria定義/メールプロバイダ選定/factory Blocked解除。
  - result: 

- [ ] [[2026-06-15_queue-review-pending-nonblocking]]
  - createdAt: 2026-06-15 20:47
  - app: progress
  - project: 自動実行キュー「レビュー待ちで止まる」判定見直し
  - priority: high
  - summary: Codex実装→Fableレビュー(整合性バグ修正含む)。ただのレビュー待ち(not_reviewed)で工場が止まる問題を是正。deriveWorkItemStatus再設計=危険riskFlags(billing/production_db/auth_secret/external_publish/destructive/migration)/failed/blocker→blocked、pendingApproval/approval_required/needs_human→waiting_user、それ以外は止めない。not_reviewed/copied→executable継続＋reviewPending=true(レビュー未確認あり・継続)。P0/P1エスカレーションと『レビュー待ちのため候補外』表記廃止・review_waitingはnot_reviewedから非生成。needs_followup/fixPrompt→executable＋fixRequested=true＋queueScore+700 boost。AutoQueueItemにreviewPending/fixRequested追加・/queue/司令塔にバッジ。Fable修正: reviewPending/fixRequestedがfactoryEligible無視でexecutable返し対象外Epicが全リスト漏れ(counts executable1/next None)の不整合を、factoryEligibleに従いexecutable/else manual・flagはstatus=executable時のみ付与に修正。検証PASS: tsc0/lint0/build成功・review_waiting1→0・対象外epic-91はmanualで可視・候補可否整合。安全(危険/判断要停止)維持。commit fbc3e5a push済。確認観点=未レビューでも継続する方針の是非・deployをblock非対象の妥当性・fixRequested +700 boostバランス・status/候補可否整合。
  - result: 

- [ ] [[2026-06-15_review-copy-reviewing-state]]
  - createdAt: 2026-06-15 01:03
  - app: progress
  - project: レビュー用コピー「レビュー中」表示
  - priority: low
  - summary: ユーザー要望「レビュー用コピー押したらレビュー中と表示」。ReviewCopyButton.tsxで『全体をコピー』成功時に主ボタンを🔍レビュー中(amber)へ切替、localStorage(reviewCopy:reviewing)保持で画面遷移/再読込でも維持、モーダルにバナー＋『レビューを終了』で解除。operating-model更新。検証: tsc0/lint0/build成功・/ 200・idleはレビュー用コピー表示。commit fd8fc8b push済。確認観点=localStorage保持のUX(端末/タブ間非共有)・自動解除条件(時間経過)の要否・レビュー中対象/時刻の可視化要否。
  - result: 

- [ ] [[2026-06-15_goalplanner-queue-integration]]
  - createdAt: 2026-06-15 00:14
  - app: progress
  - project: Goal Planner×自動実行キュー データ統合
  - priority: high
  - summary: ユーザー要望「Goal Plannerと自動実行キューを同じ文脈に統合」を実装(Codex実装→Fableレビュー)。ズレ原因=Goal Plannerはgoals.json(Todo/phase 0%)、/queueはbuildAutoQueue派生を別々に参照。統合=集計源をbuildAutoQueue().goalProgressに統一(別正本作らない)。GoalProgressRowにnextCandidateCount/manual/latestWorkTitle/nextActionTitle追加。Goal Plannerの各Goalカード＋メイン目標にキュー要約(次回候補/実行可能/判断待ち/レビュー待ち/候補外/最新作業/次にやること)を表示し、自動実行キューを見る(/queue?goalId)・ToDoを見る(/decide?goalId)・作業予約 導線追加。/queueはgoalIdでカウンタ/フィルタ/一覧をGoal絞り込み(全体に戻る/Goalバナー)。GoalPlannerFormのrole選択UI撤去(保存は内部既定addToQueueRoles=[claude])。GoalとGoal進捗非分離維持・旧phases/todos互換残置。検証PASS: tsc0/lint0/build成功・/goal-planner /queue /decide 200・カード要約描画・goalProgress件数と/queue?goalIdアイテムstatusクロスチェック一致(reviewWaiting=1)・role UI撤去/対象案件select存続。commit 36a35c8 push済。確認観点=同一source統合の件数整合の抜け・旧phases/todos併存の是非と撤去時期・role撤去の影響(goal import)・目標カード指標の過不足。
  - result: 

- [ ] [[2026-06-14_review-fix-auto-priority]]
  - createdAt: 2026-06-14 17:53
  - app: progress
  - project: レビュー「修正する」fixPromptの最優先自動実行（承認不要）
  - priority: high
  - summary: ユーザー選択オプション3=レビュー修正プロンプトを承認なしで定時自動実行の最優先で作業する運用を実装(Codex実装→Fableレビュー)。新規runReviewFixDispatch(lib/review-fix-runner.ts)をrunScheduledFactory先頭(Epic factory/Prompt Queueより前)に接続=最優先。対象=needs_followup＋fixPrompt非空(未消化)。安全ゲート=classifyCodexEligibility＋hard-deny(課金/billing/deploy/本番/production/secret/.env/認証/migration/削除/destructive/force)。危険該当は実行せずneeds_followupのままreviewMemoに理由。dry_run=記録のみ/auto+confirm(定時のみ)=getAdapter実起動・成功時のみ元runをreviewedに消化(source=review_fix/followupOfRunIdで重複防止)。cap1〜2。検証PASS: tsc0/lint0/build成功・呼び出し順review-fix→Epic→PromptQueue・pickTargets/安全ゲート読み取り検証。重要発見=ユーザー2件目fixPrompt(Goal Planner統合)は本文に『削除』含みブロック→自動実行されず人手対応、1件目(inboxフィルター)は次回定時で最優先auto実行。auto実adapterはテスト未実施(安全方針)。commit b823fc1 push済。確認観点=承認なし最優先自動実行の暴走防止・hard-denyに一般語『削除』を含む過剰ブロックの是非・消化マーキングの二重実行リスク・実行順とcap。
  - result: 

- [ ] [[2026-06-14_bottomnav-all-pages]]
  - createdAt: 2026-06-14 13:29
  - app: progress
  - project: モバイル下タブ＝横スクロール全画面タブ化
  - priority: high
  - summary: ユーザー指示「下タブにない主要画面はなくして／リンクで飛べるものは全て下タブに追加」を実装。BottomNavを横スクロール化し、先頭5=アイコン付き主要タブ(ホーム/ToDo/Project/目標/自動実行)＋moreItemsで残り主要画面24(作業予約/Revenue/運用/実行履歴/ToDo管理/JSON取込/動作確認/おすすめEpic/収益化/承認/自動化/工場Epic/Codex/朝会/日別/AI自走/レーダー/案件/旧Inbox/決定事項/工場候補/URL/旧キュー/旧ダッシュ/画面一覧)を全てテキストタブで列挙。legacy catch-all/NEW_ROUTES撤去。/legacyは『下タブにない主要画面』表記撤廃し全画面カテゴリ別一覧(画面一覧)として存続。リンクで飛べる主要画面計29が全て下タブ直接到達=『下タブにない主要画面』撤廃。guide/operating-model更新。検証PASS: tsc0/lint0/build成功・全ルート200・下タブ描画確認。commit e82e014 push済。確認観点=約29項目横スクロール下タブのiPhone実用性(主要5固定＋すべてシート方式の是非)・主要5アイコン＋残テキストの視覚階層・旧画面まで全部出すノイズvs到達性・/legacy存続要否。
  - result: 

- [ ] [[2026-06-14_progress-verify-todos]]
  - createdAt: 2026-06-14 11:05
  - app: progress
  - project: 動作確認Todoページ（/verify-todos）実装確認＋検証（Factory Dispatch epic-progress-todo）
  - priority: medium
  - summary: Factory Dispatch（epic-progress-todo / goal-ai-factory-os）。Claude Code/Codexの作業やEpic完了後に人間が確認すべき画面・URL・手順をprogress内で一元管理する動作確認Todoページ /verify-todos。前回(Jun 13)実装一式（page / API GET,POST,PATCH,DELETE / lib verify-todos / VerifyTodosBoard / seed 1件 / ナビ導線TopNav・legacy・guide section9・command-center TERMS）が存在することを確認し、未実施だった検証を本セッションで完了。DoneCriteria全6項目充足を照合: ①ページ追加 ②アプリ名/Epic名/確認URL/確認手順/期待結果/状態の登録 ③未確認/確認済/NG/保留の4状態管理 ④アプリ/Epic/状態の3軸絞り込み ⑤運用メモ(operationMemo＋guide section9) ⑥確認URLを開く(a href target=_blank、url-normalizeでhttps補完)。検証PASS: tsc0/lint0(対象5ファイル No warnings)/build成功(/verify-todos ルート3.42kB)。本セッションはコード変更ゼロ(既存実装の検証のみ)のためprogress側commitなし。本番反映(Vercel)未実行・iPhone実機E2Eは人間側seed Todo(vt-seed-progress-verify)で実施想定。確認観点=app-urls.json(URL台帳)との責務分担/確認URL公開URL推奨運用のiPhone消化十分性/4状態・3軸絞り込み設計/本番反映要否。
  - result: 

- [ ] [[2026-06-14_bottomnav-restructure]]
  - createdAt: 2026-06-14 10:51
  - app: progress
  - project: モバイル下タブ再編・自動実行(/queue)主要タブ昇格
  - priority: high
  - summary: ルート棚卸しレビュー(20260614-101321)の最重要指摘=自動実行キュー(/queue)がモバイル下タブに無くiPhone未到達、を実装で解消(ユーザー指示「2をいますぐ」)。BottomNavを ホーム/ToDo(/decide)/Project(/portfolio)/目標(/goal-planner)/自動実行(/queue)/その他(/legacy) の6タブへ再編。orphan回避でRevenue/運用(/guide)/作業予約(/prompt-queue)/動作確認を/legacyハブの新グループ『よく使う(下タブにない主要画面)』へ集約＋旧キュー非正本明記＋ToDo JSON取り込みリンク追加。/queueを自動実行ハブ化(作業予約/実行履歴logs導線)。guide/operating-model更新。登録済み実装タスクpq-mqd3acmp-k0tp3rをcompletedにして定時Factory重複防止。検証PASS: tsc0/lint0/build成功・新下タブ描画・全主要ルート200・/legacyにRevenue/運用/作業予約・/queueハブ導線を確認。commit f4fa2f9 push済。確認観点=6タブ構成とその他ハブ集約のiPhone導線・Revenue/運用を主要から外した是非・自動実行タブのハブ画面化・旧画面廃止優先度。
  - result: 

- [ ] [[2026-06-14_progress-route-audit]]
  - createdAt: 2026-06-14 10:13
  - app: progress
  - project: 全ルート棚卸し・旧画面/下タブ未掲載ページのナビ整合性レビュー
  - priority: high
  - summary: Fableがprogress全39ルートを棚卸し。最重要発見=モバイルBottomNav(BottomNav.tsx)は6タブ(司令塔/Inbox=decide/Projects=portfolio/Revenue/運用=guide/Legacy)で、自動実行キュー(/queue)とPrompt Queue(/prompt-queue)が含まれずiPhoneから辿れない(desktop TopNav/司令塔リンク/legacyハブのみ)。Queue整理: /queue=Epic派生のAI工場自動実行キュー(正本・最重要)、/legacy/queue=旧work-queue並べ替え(廃止候補)、/prompt-queue=作業プロンプト貯蔵庫(定時Factory接続済)。重複=/inbox↔/decide、/projects↔/portfolio、/recommended-epics↔Inbox Epic候補、/monetization↔Revenue。/operations→/automation・/pending→/tasksはリダイレクト。提案=BottomNavに自動実行タブ追加(/queue+/prompt-queue+/logs統合)、5タブ案(ホーム/ToDo/Project/Goal進捗/自動実行)、即時最小修正=/queueをBottomNavに追加。実装タスクをPrompt Queueに登録(pq-mqd3acmp-k0tp3r/queued→次回定時候補)。コード変更なし(レビュー＋登録)。確認観点=BottomNav再編の5タブ案是非・即時/queue追加・旧inbox/projects/legacy queue廃止可否・recommended-epics↔Inbox統合・登録タスクを定時Factory任せか手動か。
  - result: 

- [ ] [[2026-06-14_prompt-queue-factory-connect]]
  - createdAt: 2026-06-14 10:04
  - app: progress
  - project: Prompt Queueを実Factory自動実行に接続（安全ゲート付き）
  - priority: high
  - summary: Codex実装→Fableレビュー。新規runPromptQueueDispatch()を定時起動runScheduledFactory(11/14/16/23)のlock内・Epic Factory実行後にmaxItems:1で接続。対象は次回やる候補。安全ゲート=classifyCodexEligibility＋hard-deny正規表現(課金/billing/deploy/本番/production/secret/.env/認証/migration/削除/destructive/force)で危険プロンプトは実行せずneeds_user_prompt_fixへ隔離。mode別=dry_run(既定)は実起動せずreserved/auto+confirm(定時のみ)は既存adapter実起動、結果でcompleted/failed/needs_retry。ExecutionRun source=prompt_queue/factoryRun/dispatchMode/promptUsed記録・item.executionRunIdに戻す。factoryEnabled=false/Blockedは何もしない。Epic dispatch不変更。検証PASS: tsc0/lint0/build成功、dry_run実機テスト(一時endpoint作成→検証→削除)でconsidered2/reserved1/blocked1/executed0・安全→reserved+runId(adapter未起動)・危険「本番DBを削除 force」→needs_user_prompt_fix(未実行)・既存回帰なしを確認(テストデータ消去)。commit 3dcc134 push済。リスク=定時autoで安全分は実adapter起動するため多層防御の堅牢性が要。確認観点=安全ゲートの十分性(すり抜け/過剰ブロック)・承認なし実行の線引き・mode分岐とstatus遷移の穴・同一lock順次実行の負荷。
  - result: 

- [ ] [[2026-06-14_prompt-queue]]
  - createdAt: 2026-06-14 09:29
  - app: progress
  - project: Prompt Queue（作業プロンプト貯蔵庫）新設・入力簡素化
  - priority: high
  - summary: Codex実装→Fableレビュー。新機能/prompt-queue。入力をタスク名/プロンプト/Project/Goal進捗の4項目に簡素化、実行対象AI(codex/claude/fable_review/auto)・優先度(P0/P1/P2)はUI非表示(内部既定auto/未指定)。GoalとGoal進捗を分けず単一goalProgress(既存Goal参照)。正本data/real/prompt-queue.json(物理削除なし・DELETE→archived)。API GET/POST/PATCH/DELETE/import。JSON一括取り込みpromptQueue/todos両形式・goal→goalProgress正規化・status未指定→queued・priority/assignee/preferredExecutor無視(警告)・title/prompt無しエラー・project不一致は未紐付け警告。次回やる候補は未完了(queued/reserved/not_started/failed/needs_retry/needs_user_prompt_fix)をProject/Goal進捗状態順で理由付き表示・completed/canceled/archived/snoozed除外。ナビ(Legacy)作業予約。検証PASS: tsc0/lint0/build成功、フォーム4項目・import両形式と正規化/無視/バリデーション・次回候補と理由・snooze除外(3→2)・DELETE→archived・既存回帰なしをe2e確認(テストデータ消去)。未接続=実Factory自動実行(貯蔵庫＋候補表示まで)。commit dd560a7 push済。確認観点=優先度/実行AI非保持の運用耐性・候補並び順ロジック・取り込みバリデーションの穴・実Factory接続方針。
  - result: 

- [ ] [[2026-06-14_fixprompt-dispatch-reflect]]
  - createdAt: 2026-06-14 08:52
  - app: progress
  - project: 修正依頼(fixPrompt)が次回自動実行に反映されるか確認テスト＋修正
  - priority: high
  - summary: ユーザー依頼「保存した修正依頼が本当に自動実行で再修正に使われるか確認テスト」。テスト結果=反映されていなかった(欠落特定)→修正→再テストで反映確認。原因: fixPromptはrun→followow候補→(承認で)epic.remainingWorkまで流れるが、Factory Dispatchプロンプト(generateClaudeFactoryPrompt/generateCodexPrompt)はepic.goal/doneCriteria/ExecutionRun由来nextActionsのみ参照しremainingWork/fixPromptを読まないため次回自動実行に乗らなかった。修正: buildDispatchPlanにhumanFixInstructions(当該Epicのneeds_followup Run fixPrompt＋承認済みremainingWork修正指示)追加、Claude[3-1]/Codex[4-1]に『人間の修正指示(最優先で対応)』出力、FactoryDispatchPlan型拡張。検証: tsc0/lint0/build成功。修正前=プロンプトに修正指示無し(False)→修正後: (a)GET dispatch?epicId=epic-91でhumanFixInstructions取得 (b)epic-91を一時dispatch可化しclaudeプロンプト[3-1]に修正指示が出ることを確認(True)→epic原状復帰。commit 1a5d275 push済。注意=doneCriteria/priority無Epicはdispatch自体blockedのため修正依頼だけでは自動実行されない(契約必要)。確認観点=収集元の十分性/重複・古い指示混入/承認時doneCriteria掲載の要否/[3-1][4-1]位置文言/プロンプト肥大化。
  - result: 

- [ ] [[2026-06-14_inbox-goal-scope-deeplink]]
  - createdAt: 2026-06-14 02:12
  - app: progress
  - project: Inboxゴール紐づけ＋次回実行予定→該当レビュー直接遷移
  - priority: high
  - summary: Codex実装→Fableレビュー。司令塔トップ「Inboxでレビューする」が/decide固定でInboxTabsがURLクエリ未対応・今日の判断0件に飛んで詰まるバグを修正。InboxTabsをuseSearchParams対応(tab/reviewFilter/filter=needs_followup/focusRunId/goalId)、focusRunIdで該当カードへスクロール+ハイライト+「次回実行予定から移動しました」+状態に応じフィルタ自動切替。goalIdを変換レイヤーで付与(run.epicId→Epic.goalId→targetApp→unassigned・未紐づけは消さず表示/絞り込み可)。ゴール件数をInboxと同一ソースで集計。deriveResolution遷移先を/decide?tab=...&goalId=...&focusRunId=...に修正(0件today禁止)。次回自動実行予定カードにGoal名/候補外理由/こうすれば動きます/該当レビュー・Goalレビュー一覧・キュー調整。0件タブで同Goal他タブ件数あれば案内。検証PASS: tsc0/lint0/build成功、resolution href=review tab確認、/decide?tab=reviewでレビュータブ35件・goalId絞り込み35→17・focusRunIdハイライト案内を確認、goalIdはgoal-ai-factory-osに正割当。reviewed/snoozed/needs_followup回帰なし。commit d582a50 push済。確認観点=goalId推定順の妥当性/誤割当リスク・SSRとクライアント初期表示ズレ・focus対象の可視保証・ゴール件数とInbox件数一致・0件相互案内UX。
  - result: 

- [ ] [[2026-06-13_review-fix-prompt]]
  - createdAt: 2026-06-13 23:48
  - app: progress
  - project: Inboxレビュー「修正する」修正指示プロンプト→次回自動実行反映
  - priority: high
  - summary: Codex実装→Fableレビュー。「修正する」を修正依頼ボックス化。押下時カード内textarea展開→保存でreviewStatus=needs_followup＋ExecutionRunにfixPrompt/fixRequestedAt/fixRequestedBy=human保存(reviewMemoにも反映)、空欄は警告。要修正カードに修正指示表示。buildFollowupRecommendationでfixPromptをreason冒頭(人間の修正指示:)・doneCriteria先頭(人間の修正指示を満たす:)・notesに反映、既存候補も更新パスで反映→承認後Epic/Factory実行時に人間指示が作業指示に。問題なし/あとで/レビュー済み・物理削除なしは不変。検証PASS: tsc0/lint0/build成功(50)/全ページ200、実needs_followup run(20260608-140533)へのPATCHでfixPrompt保存＋followow候補反映をe2e確認し検証後テストデータ消去。commit f5214fb push済。確認観点=保存先をrunにした判断/recommendation経由(承認必須)で『優先的に拾う』要件充足か・dispatch本文直接注入の要否/空欄ブロック・キャンセル状態不変の回帰/後編集UIの要否。
  - result: 

- [ ] [[2026-06-13_monetization-daily-ingestion]]
  - createdAt: 2026-06-13 22:57
  - app: progress
  - project: 収益化候補 定期取り込み・調査元可視化（既存実装調査＋日次フォルダ走査拡張）
  - priority: medium
  - summary: 事前調査で依頼機能の約9割が既存実装済みと判明（sourceRefs/researchLogs/evidenceLinks/history等のデータ構造、syncCandidatesFromVaultのFactory定時取り込み、重複判定、ExecutionRun記録、/monetization一覧の調査元数・最終調査、/monetization/[id]の調査元一覧・調査履歴・根拠リンク・スコア変更履歴）。「既存に近い機能は新規作成せず拡張」に従い、未走査だった日次調査サブフォルダ(06_research/daily-market-research・daily-ai-news・daily-ai-tools)を走査対象に追加・daily種別分類・上限300→400に拡張のみ実施。検証: tsc0/build成功/手動同期2回でscannedFiles=302・added0updated1→added0updated0(冪等・二重追加なし)・mc-birdlog-001で調査元一覧43件/調査履歴/根拠リンク描画確認。安全ルール順守(候補追加のみ・Epic化/課金/deploy非接触・Vault読み取りのみ)。commit 42692d4 push済。確認観点=新規作成せず既存流用の判断/日次メモが候補名照合しないと付かない限界(エイリアス要否)/302ファイル毎定時走査の性能/daily種別分類の妥当性。
  - result: 

- [ ] [[2026-06-13_auto-queue-resolution-howto]]
  - createdAt: 2026-06-13 17:47
  - app: progress
  - project: 自動実行キュー 候補外の解消手順表示
  - priority: medium
  - summary: ユーザー指摘「候補外と出るがどう解消するか分かりにくすぎる」を改善。AutoQueueItem.resolution(how/actionLabel/actionHref)追加、deriveResolution()でstatus別の次アクション案内(review_waiting/waiting_user→Inboxでレビュー/承認(/decide)、blocked→Epic詳細、ai_hold→保留解除、manual対象外→対象に戻す)。control APIにinclude(対象に戻す)追加、/queueに対象に戻すボタン。司令塔トップ「最優先指定中だが候補外」枠と/queueカードに「👉こうすれば動きます」手順＋ボタン表示。運用ページも更新。tsc0/lint0/build(49)/全ページ200。実データでpin済みAI工場オペレーションセンター(review_waiting)に「Inboxでレビューする→/decide」確認。pinはgating非上書き維持。commit f265460 push済。確認観点=status別文言/遷移先の適切さ・includeでfactoryEligible=true復帰の安全性・解消導線の分かりやすさ・カードの情報過多。
  - result: 

- [ ] [[2026-06-13_auto-queue-pin-reflect-fix]]
  - createdAt: 2026-06-13 16:01
  - app: progress
  - project: 自動実行キュー pin反映不具合修正＋運用説明（Codex実装→Fableレビュー）
  - priority: high
  - summary: /queueでpinしてもトップの次に反映されない件をCodex修正→Fableレビュー。原因=複合: pin保存は機能していたが(1)pin済みでもreview_waitingで候補外なのにUIが未説明(2)司令塔トップに旧factoryOutlook由来の次/その次が残り食い違い(3)force-dynamic/revalidate不足。修正=トップと/queueをgetAutoQueueView()由来に統一・旧表示撤去、control/goals priority APIでrevalidatePath(/)(/queue)+force-dynamic、AutoQueueViewにpinnedExcluded・各itemにcandidateEligible/candidateBlockedReason追加、トップに「最優先指定中だが候補外」枠・/queueに候補入否/理由/queueScore/pin済み候補外警告、保留をレビューより先にai_hold導出、最優先ボタンを自動実行最優先/復帰時最優先に分岐(pinはgating非上書き)、運用ページにキューの使い方/status/操作意味/安全注意追加。レビュー結論PASS: tsc0/lint0/build(49)/全ページ200。実データでpin済みAI工場オペレーションセンターがreview_waiting・candidateEligible=falseで「最優先指定中だが候補外(レビュー待ち)」表示を確認。Codexのcontrol APIテストで実epics.jsonにhold=true副作用(BirdLog/progress-todo)→Fableがunhold復旧。commit 3a4ff67 push済。確認観点=pin非上書き仕様で不満解消十分か/保留先行導出の副作用/revalidate範囲/テストが実データ汚染した運用問題。
  - result: 

- [ ] [[2026-06-13_factory-schedule-add-16]]
  - createdAt: 2026-06-13 15:36
  - app: progress
  - project: Factory定時自動実行に16:00 JST追加
  - priority: medium
  - summary: ユーザー依頼でFactory定時自動実行に16:00 JSTを追加(11/14/16/23)。/etc/systemd/system/factory-schedule.timerのOnCalendarに16:00を1行追加→daemon-reload+timer再起動→list-timersで次回Sat 16:00発火・active+enabled確認。UIの定時表示が読むrepo正本docs/factory-schedule/factory-schedule.timerも11/14/16/23へ同期(従来docsは11/23のみで14欠落も補正)。README定時表記・operating-model変更履歴更新。各定時はrunScheduledFactoryの安全ゲート(factoryEnabled/Blocked/二重起動lock)経由で無条件起動ではない。commit 1eb1ddf push済。確認観点=4回/日の頻度妥当性/安全ゲート経由確認/repo docsと/etc実体の二重管理。
  - result: 

- [ ] [[2026-06-13_auto-execution-queue-mvp-codex]]
  - createdAt: 2026-06-13 15:01
  - app: progress
  - project: 自動実行キュー(/queue) MVP（Codex実装→Fableレビュー）
  - priority: high
  - summary: 設計(20260613-125025)の§11 MVPをCodex(gpt-5.2)が実装、Fableが独立レビュー・検証。buildAutoQueue()がEpic/Goal/ExecutionRun/Approval/Inboxから派生(work-queue.json不使用・新正本なし)。WorkItemStatus導出＋queueScore(pin>Goal pin>P0/P1/P2+boost>freshness)＋reason機械生成。司令塔トップの次回自動実行予定を旧work-queueからbuildAutoQueueへ差し替え。/queue(最優先/上下/保留/対象外)＋API3本。旧UIは/legacy/queueへ退避。レビュー結論PASS: tsc0/lint0/build成功(49)/全ページ200。実データ検証で野鳥BirdLog(P1)はwaiting_userに分類され次回候補に出ない=二重正本(野鳥)問題を解消。pin round-tripでpinはgating非上書き(安全)。指摘(非ブロッキング)=manualOrder stickiness(一度並べ替えると新規高優先が沈む)/factoryEligible未設定の分類/goal_todoの危険gating迂回。運用ドキュメント4点セット更新。commit 89fc8b0 push済。残=iPhone実機確認。確認観点=manualOrder採番設計/fe未設定の扱い/goal_todo安全性/pin非上書き/executable=0の空表示UX/MVP範囲。
  - result: 

- [ ] [[2026-06-13_inbox-review-full-list]]
  - createdAt: 2026-06-13 13:46
  - app: progress
  - project: Inboxレビュー待ちUI改修（全件表示・消し込み運用）
  - summary: Fable5が実装＋検証。レビュー待ちの隠れ表示（ほか◯件/処理すると次が出ます・5件截断）と「10件まとめて確認」固定を廃止。レビュー待ちを未消込リストとして全件表示し、各カードに完了日時(YYYY/MM/DD HH:mm)を表示・completedAt(finishedAt→startedAtフォールバック)降順で最新を上に。未確認/要修正/あとで/レビュー済みの件数サマリー＋フィルタ＋50件明示ページング。状態遷移整理=問題なし→reviewed消込(レビュー済みタブに残置・物理削除なし)/あとで→新ReviewStatus snoozed(後回し残置)/修正する→needs_followup(要修正残置)。AI一括整理は未確認全件(サーバ安全上限200件)。guide/operating-model運用ドキュメントもセット更新。tsc0/build成功(48ページ)/lint0/全ページ200/RSCにcompletedAtText89件・reviewCounts・reviewedHistory反映を確認。commit 2dc7134 push済み。残=iPhone実機でフィルタ/ページング/バッジの崩れ確認。確認観点=状態遷移の袋小路有無/snooze追加の影響漏れ/reviewed履歴200件制限の妥当性/AI一括整理全件化の大量書き込みリスク。
  - priority: medium
  - result: 

- [ ] [[2026-06-13_auto-execution-queue-design]]
  - createdAt: 2026-06-13 12:50
  - app: progress
  - project: 自動実行キュー管理（Auto Execution Queue 設計・実装なし）
  - priority: high
  - summary: Fable5が設計のみ実施。司令塔トップが未整理/低関連案件（野鳥観察系）を先頭に出す問題の根本原因を「work-queue.json と Epic Contract の二重正本」と特定。確定方針=Epic(実行正本)/Goal(優先度の親・配下へboost伝播)/Project(表示軸)/ExecutionRun(履歴正本)、自動実行キューは新正本を作らず buildAutoQueue() の派生ビュー、ユーザー手動操作のみ Epic.queueControl/Goal.priorityBoost に書き戻す。Goal vs item優先度の矛盾=加点(floor boost)方式（pin>Goal pin>priority+boost）、gating=item単位で全体を止めない、review_waitingはP2・危険なしなら工場継続、Inbox未整理はキュー非投入、waiting_user(人間が動かないと永久停止)とai_hold(AI再開可)を分離。15成果物+Codex仕様書(§14)+プロンプト(§15)+受け入れテスト1〜9。MVP=buildAutoQueue/トップ表示元差し替え/queue画面4操作/reason生成/Inbox除外。確認観点=Epicを実行正本にする移行リスク/加点方式の穴/item単位gatingで永久未着手itemが出ないか/ai_hold自動再開条件の曖昧さ/Codex仕様の実装可能粒度。
  - result: 

- [ ] [[2026-06-13_progress-review-copy-impl]]
  - createdAt: 2026-06-13 01:33
  - app: progress
  - project: レビュー用コピー機能（Codex実装→Fable検証・本番反映）
  - priority: high
  - summary: 設計（20260613-001054）通りにCodexが実装、Fableが検証して本番反映。lib/review-copy.ts（14セクションMarkdown・12,000字バジェット・機密マスキング）/ GET /api/operations/review-copy / 2タップ式モーダル（iOS Safari対応・手動コピーfallback）/ 司令塔ホーム設置 / 運用ドキュメント4点セット更新。tsc・lint・build OK、pm2再起動後ライブAPI 200・実データ3,840字・ホーム表示OK、commit b77c4b7 push済み。残=iPhone実機コピー確認のみ。確認観点=3,840字の情報量過不足/危険・注意が直近10run依存で見逃しないか/2タップUX改善余地。
  - result: 

- [ ] [[2026-06-13_progress-review-copy-design]]
  - createdAt: 2026-06-13 00:11
  - app: progress
  - project: レビュー用コピー機能（設計レビュー・実装なし）
  - priority: high
  - summary: Fable5が設計レビュー実施。実装すべき・優先度高と判定。必要データの9割がbuildCommandCenter()に集約済みのため低コスト低リスク。確定設計=司令塔ホームに2タップ式コピーモーダル（iOS Safariクリップボード制約対応）/Markdown単独・12,000字バジェット・直近7日窓/既存ビルダー再利用必須/読み取り専用でInboxカード化なし/Legacy取り込み系・AdhocReviewCopyPanelは共存。Codex実装5タスク分解+投入プロンプト作成済み。確認観点=2タップの手数許容/字数バジェット妥当性/レビュー後の戻り道（人間手動起票）で閉ループ十分か。
  - result: 

- [ ] [[2026-06-12_ai-factory-os-review-top10]]
  - createdAt: 2026-06-12 21:24
  - app: progress
  - project: AI工場OS v2（運用設計レビュー+Codex実装TOP10）
  - priority: high
  - summary: Fable5が10観点レビュー(総合72点)、不足TOP10をCodexが全件実装し本番反映完了。①修正依頼閉ループ(needs_followup→followupOfRunId候補生成、6件backfill) ②実行中running表示(30分stale回復) ③Epic候補30日expired ④収益ロードマップrevenue-config.json駆動化(BirdLogハードコード除去) ⑤承認時Goal同時指定 ⑥停止経過日数バンド ⑦アーカイブ閾値300 ⑧request cache ⑨Goal根拠表示 ⑩data-health。tsc/lint/build成功・データ件数保全・pm2再起動後全ページ200・commit 9b85d79 push済。確認観点=followupリンクのEpic notes経由伝搬の脆さ/running stale回復のエッジ/expired30日・archive300の妥当性。
  - result: 

- [ ] [[2026-06-12_factory-dashboard-redesign]]
  - createdAt: 2026-06-12 09:28
  - app: progress
  - project: AI工場OS v2（計器盤化 再設計・実装なし）
  - priority: high
  - summary: 「判断アプリ」から「工場の計器盤」への再設計。12観点評価(◎=判断整理/停止原因、✗=Now・Next・Later/今夜の予定(timer11・14・23時JST非表示)/Goal実測/修正閉ループ/半年持続性)。理想画面=ホーム1画面5分把握。TOP10をROI×難易度で再評価し実装順確定: 第1波(Factory自走可)=Now/Next/Later+今夜の予定・稼働サマリー・内部語人間語化・AIレビュー夜間自動化・メタ候補発生源ガード、第2波(人間確認)=修正閉ループ・進捗%・収益化ラベル、第3波=Goal自動計測・スヌーズ統一。自動実行組込判定付き。確認観点=修正閉ループを第2波に回した判断/Now・Next・Laterの粒度/夜間AIレビューの位置付け。
  - result: 

- [ ] [[2026-06-12_ai-factory-os-design-review]]
  - createdAt: 2026-06-12 08:57
  - app: progress
  - project: AI工場OS v2（全体設計レビュー・実装なし）
  - priority: high
  - summary: 社長OSとして成立しているかを10観点でレビュー(実装なし・実データ根拠付き)。総合58点(操作単純化80点台/状況把握40点台)。最大欠落=①Now/Next/Later可視化なし(稼働情報は内部語1行のみ) ②「修正する」押下後の閉ループ欠損(needs_followup6件宙吊り) ③Goal metric手動固定で北極星形骸化 ④メタ候補再帰生成の発生源未対策。レビュー滞留で止めない方針は妥当(前提=AI一次レビュー定期自動実行)。Inbox4分類は適切(欠け=修正依頼の行き先/人間作業の復活条件)。実装TOP10提示(1位=Now/Next/Later、2位=修正依頼閉ループ、3位=AIレビュー自動化)。確認観点=58点の妥当性/TOP10順位/判断空振りリスク評価。
  - result: 

- [ ] [[2026-06-12_exclude-premature-human-tasks]]
  - createdAt: 2026-06-12 08:32
  - app: progress
  - project: AI工場OS v2（今日の判断の精度改善）
  - priority: low
  - summary: ストア公開申請・課金/サブスク・AdMob・アカウント登録系の自動生成候補を「今日の判断」から除外しAI保留へ(全アプリ未公開で時期尚早・ユーザー指示)。今日の判断0件=🎉工場稼働継続に。必要時はRevenueロードマップで案内。tsc0/lint0/build成功/スクショ確認。commit b4d65b9 push済。確認観点=人間作業の表示経路喪失リスク/milestone連動の復活条件設計。
  - result: 

- [ ] [[2026-06-12_inbox-tabs]]
  - createdAt: 2026-06-12 01:33
  - app: progress
  - project: AI工場OS v2（Inbox UI改善）
  - priority: low
  - summary: Inboxの4区分(今日の判断/レビュー/Epic候補/AI保留)を縦積みからタブ切り替えへ変更。件数バッジ付き(今日の判断のみ赤字)。「今日の判断」タブだけ処理すれば工場は止まらない構成。tsc0/lint0/build成功/各タブ切替スクショ確認。commit 7c8994c push済。確認観点=タブ化で②③④に気づきにくくなるリスク/デフォルトタブの妥当性。
  - result: 

- [ ] [[2026-06-12_factory-stop-policy-change]]
  - createdAt: 2026-06-12 00:57
  - app: progress
  - project: AI工場OS v2（工場停止条件の運用方針変更）
  - priority: high
  - summary: 工場停止条件を変更。旧=not_reviewed>10で減速・>20で停止 → 新=レビュー件数では止めない(レビュー32件で稼働継続を実測)。停止条件は危険判断待ち(全体停止)/Goal未設定Epic(該当Epicスキップ・全部未設定なら停止)/人間作業(AI対象外・他Epic稼働継続)のみ。Inboxを4セクション化(①今日の判断=停止要因のみ最大3件約3分 ②レビュー=検収・放置可 ③Epic候補=実行許可・放置可 ④AI保留=件数のみ)。ホームは「今日の判断 残りN件」主表示・レビューは参考情報化。入れ子メタ候補(レビュー起点×5重)の内部語漏れ修正+AI保留自動退避。guide/TERMS/current-operating-model.mdセット更新。tsc0/lint0/build成功/禁止語0件/スクショ3枚。commit e9d6ce7 push済。確認観点=レビュー無制限滞留のKnowledgeループ停滞リスク/人間作業=項目待ち解釈/検収系をexternal_publishでも停止要因除外する安全性/Goal未設定スキップの静かな放置リスク/メタ候補発生源対策。
  - result: 

- [ ] [[2026-06-11_inbox-six-decision-types]]
  - createdAt: 2026-06-11 21:15
  - app: progress
  - project: AI工場OS v2（社長向け意思決定アプリ・Inbox 6分類）
  - priority: high
  - summary: Inboxの分類軸を「タスクの種類」→「人間が何を判断するか」へ再設計。6分類=検収[問題なし/修正する/あとで]/実行許可[進める/あとで/やめる]/方針選択[目標名ボタン/不要]/人間作業[完了した/あとで/不要]/危険判断[許可する/許可しない]/AI保留(カード非表示・「AI保留55件 AIが整理中です」のみ)。優先順=危険判断→検収→方針選択→実行許可→人間作業で最大3件約3分。AI保留自動退避(定期実行/内容不足/重複/同テーマ大量候補は各テーマ1件のみ)。カード本文はラベル付き説明行(AIがやったこと/人間がやること/放置すると/影響/選ばないと)。Goal紐付けは目標名ボタン直接選択。ホーム①②③に分類ラベル。fs依存moduleのクライアントimportによるビルド失敗をinbox-labels.ts分離で解消。guide/TERMS/current-operating-model.mdセット更新。tsc0/lint0/build成功/禁止語13種0件/Before・Afterスクショ。commit 97a9741 push済。確認観点=6分類の網羅性/優先順/AI保留退避で重要候補が沈むリスク/「あとで」ローカル動作/完了した=rejected記録の是非。
  - result: 

- [ ] [[2026-06-11_inbox-decision-app-v2]]
  - createdAt: 2026-06-11 20:39
  - app: progress
  - project: AI工場OS v2（社長向け意思決定アプリ・今日の判断v2）
  - priority: high
  - summary: 今日の判断をさらに社長向けへ強化。タイトルをタスク名→状況文に自動変換(🚨市場調査ビューの自動更新が止まっています 等)、全カードに「放置するとどうなるか」影響1行必須、分類を🚨問題/📈改善/✅確認へ変更、表示3件制限(優先順=問題→確認→Goal未設定→改善)+AI保留40件は件数のみ、上部サマリー(残り3件約3分/AI保留)、ホーム今日やることに①②③見出しリスト+約3分+Inboxを開く。内部ID(epic-xxx)の見出し漏れバグ修正(humanizeTitle強化)。完了済み作業の誤「止まっています」防止(confirm優先判定)。guide/TERMS/current-operating-model.mdセット更新。tsc0/lint0/build成功/禁止語13種0件/Before・Afterスクショ。commit 37f5e55 push済。確認観点=ルールベース変換vsデータ側AI生成headline/impact/優先順の妥当性/AI保留とholdの並存リスク/影響推定が外れた時の害/26文字省略。
  - result: 

- [ ] [[2026-06-11_inbox-president-redesign]]
  - createdAt: 2026-06-11 19:47
  - app: progress
  - project: AI工場OS v2（社長向けUX・今日の判断）
  - priority: high
  - summary: Inboxを「今日の判断」(社長向け承認アプリ)に全面リデザイン。カード3分類(作業結果の確認[問題なし/修正する]/次の作業[はい/あとで/やめる]/Goal紐付け[目標選択/不要])に統一、内部概念(ExecutionRun/runId/reviewed/Knowledge/candidate/suggested等)はカード本文表示禁止・「詳細を見る」内のみ。humanizeTitle/humanizeOptionLabelで内部命名除去。ホームは「今日の判断 残りN件 [Inboxを開く]」カードへ。運用ページ・TERMS・current-operating-model.mdセット更新。検証=tsc0/lint0/build成功/可視テキスト禁止語0件/Before・Afterスクショ6枚(attachments/screenshots/)。commit 705f43a push済。確認観点=3分類畳み込みヒューリスティックの頑健性/「あとで」挙動の種別差/詳細内の内部ID開示/はい連打の誤操作対策/humanizeパターンの保守性。
  - result: 

- [ ] [[2026-06-11_progress-operations-guide]]
  - createdAt: 2026-06-11 12:47
  - app: progress
  - project: AI工場OS v2（運用理解・自己説明UX）
  - priority: high
  - summary: 📖運用タブ(/guide)新設。初見5分理解を目標に8セクション(このアプリとは/今日の流れ朝夜図解/AI工場の流れ+ボトルネック動的表示/今日やること動的生成+予想時間/用語辞典TERMS連動/現在の工場状態/収益化ロードマップ/FAQ5問)。最終更新フッターはdocs/operations/current-operating-model.md frontmatterから動的表示。BottomNav6タブ化。セット更新ルール(機能追加/UI変更/運用変更→運用ページ・用語・図・doc同時更新)をdoc+CLAUDE.local.mdに明文化。tsc0/lint0/build成功/iPhoneビューポートスクショ3枚(attachments/screenshots/)。実測=レビュー待ち51件ボトルネック警告・今日やること4件約4分。commit 164c736 push済。確認観点=8セクションの分量/ボトルネック閾値/予想時間1件1分の妥当性/セット更新ルール形骸化防止/TERMS正本一元化。
  - result: 

- [ ] [[2026-06-11_progress-newux-resume]]
  - createdAt: 2026-06-11 12:08
  - app: progress
  - project: AI工場OS v2（新UX・人間用司令塔）
  - priority: high
  - summary: 09:13頃に中断した新UX実装を再開・完成。ホーム=司令塔(今日やること/AI工場状態/収益マイルストーン/直近の成果)、/decide=Inbox統合(承認/orphan Epic目標紐付け/おすすめ承認/needs_human確認+AI一括確認)、/portfolio・/revenue新設、BottomNav5タブ化、旧ホームは/legacy/homeへ無削除退避。lib/command-center.tsで既存データから都度算出(新正本なし・用語を人間語翻訳)。Goal Mapping移行(goals.json 4 Goal/North Star=goal-ai-factory-os/Epic10件goalId付与)とKnowledge loop基盤も同梱。tsc0/lint0/build成功/全新ルート200/pm2反映。commit 679467f push済。確認観点=司令塔の情報設計(5〜15分に絞る取捨選択)/Inbox4種別の十分性/Legacy退避移行のリスク/収益マイルストーンのハードコード判定/1コミット同梱判断の是非。
  - result: 

- [ ] [[2026-06-11_review-backlog-pipeline]]
  - createdAt: 2026-06-11 02:25
  - app: progress
  - project: AI工場OS v2（Review滞留解消パイプライン）
  - priority: high
  - summary: Review滞留65件の解消実装。ルールベースAI一次レビュー(8ルール→reviewed/needs_human/partial/failed 4分類・理由をreviewMemo+aiReviewに保存)、直近10件一括処理API+ホームUI、Factoryバックプレッシャー(not_reviewed>10で減速maxRuns=1/>20で起動スキップ・factoryEnabled不変・automation-log記録)、Metrics8項目(closed_loop_rate等)ホーム表示、Decision Log拡充(approve/reject/assignGoal/changePriority/markReviewed/pause/drop/factory pause・resume/goal_adjust)、Human/AI Queue分離。needs_human承認決定はRunのreviewStatusへ反映しループ閉鎖。実測: 2バッチ20件処理でclosed_loop_rate 1.5%→23.5%・Knowledge 1→15件・needs_human 1件(Vercel本番反映)がHuman Queue表示。tsc0/lint0/build成功・既存68run保持。commit 562623f push済。確認観点=clean_completed誤判定リスクとサンプリング頻度/閾値10・20の妥当性/reviewed毎のNext Epic候補自動生成の是非(suggested 20→34件増)/危険キーワード5系統の抜け/Decision Log粒度。
  - result: 

- [ ] [[2026-06-11_ai-factory-os-v2-ops-review]]
  - createdAt: 2026-06-11 01:33
  - app: progress / company-meta
  - project: AI工場OS v2（運用レビュー・v2実装後フォロー）
  - priority: high
  - summary: v2実装完了後の運用レビュー10項目（ボトルネック/自走率95%/Goal運用/Next Epic生成/収益化/ホーム情報/人間介在/factoryEnabled/90日後/v3設計）。実データで最大ボトルネック=Review工程を特定（67run中65件not_reviewed・Knowledge1件→Review→Knowledge→Next Epicループが実質1回しか発火していない）。候補20件suggested滞留・意思決定ログ0行・Goal metric計測値なし・収益流入経路なしも確認。提案=AI一次レビュー導入(人間はneeds_humanのみ)/未レビュー滞留によるFactory WIP制限/人間タスクレーン分離+BirdLog収益貫通テスト最優先/metric自動計測/North Starを収益側へ移す再考/v3=計測レイヤ(アプリ別P/L・reviewer agent正式化)。調査中にprogress API全404(6/11 01:17不完全ビルド)を発見しFactory停止状態を再ビルド+restartで復旧。確認観点=AI一次レビューの信頼条件・WIP閾値・貫通優先vs幅出しのバランス・North Star変更の副作用。
  - result: 

- [ ] [[2026-06-11_ai-factory-os-v2-strategy]]
  - createdAt: 2026-06-11 00:10
  - app: progress / company-meta
  - project: AI工場OS v2（運用モデル戦略レビュー）
  - priority: high
  - summary: CTO/PMO/AI工場OS設計者としての運用モデル戦略レビュー（コード実装なし）。実データ確認でgoals.jsonが空(0 Goal/mainGoal null)・Epic.goalが外部キーでなく自由文・候補3ストリーム(recommended20/app5/monetization)未統合・factoryEnabled=false(自走停止)を特定。根本原因=Goal層が空で全Epicが横並び→人間が毎セッション事実上のプライオリタイザ=同期ブロッカー化。提案: Goal層を埋める(3〜5+北極星)/Epic.goalId必須化/承認をインライン→毎日5〜15分の非同期バッチキューへ/候補をGoal-scored単一backlogに統合+WIP制限/完了→Knowledge→次Epic生成ループの閉鎖/factory段階ON。Progressホーム表示TOP10・OS v2中核データモデル(Goal-Epic-Project-ExecutionRun-Review-Knowledge)・Codex実装TOP10・最優先TOP3・30日計画を整理。本体=06_research/2026-06-11_ai-factory-os-v2.md。確認観点=Goal個数/寿命/達成判定の妥当性・承認非同期バッチで本当に5〜15分に収まるか・候補統合スコアリング・WIP制限の機会損失・factory段階ONの前提十分性。
  - result: 

- [ ] [[2026-06-10_yomi-source-validation]]
  - createdAt: 2026-06-10 22:01
  - app: mahjong
  - project: mahjong / /yomi 原本再現性の検証(sourceValidation)
  - priority: high
  - summary: /yomi が問題データを表示しているだけで原本再現性の保証が無い問題に対し、原本(牌譜JSON/原本画像書き起こし)とアプリ表示の差異を14項目(プレイヤー位置/自風/場風/河枚数/河順序/手牌枚数/鳴き内容/鳴き位置/ドラ/巡目/点数/リーチ有無/放銃者/和了者)で突き合わせるsourceValidation層を追加。各項目をmatch/mismatch/missing/unknown判定し一致率とstatus(exact/partial/failed)を算出。failed=致命項目(和了者・放銃者・巡目)不一致or一致率<0.5。比較エンジンyomi-source-validate.mjs(compareToOriginal/formatDiffReport)、取り込み統合(ingest-yomi --original で差分failedのS/Aを隔離・差分レポートをdata/source-validation/に生成)、単体CLI yomi-source-report.mjs、検証ゲート(validate-yomiがfailed+S/Aを弾く)、/yomi/reviewに一致率バッジ+差分パネル、docs追記。差分例(上家河12→8/下家鳴き欠落/自風不一致)を実テストで再現。tsc0/build成功/validate-yomi(正本10問)PASS。commit 89dd84e push。確認観点=14項目の粒度と抜け(役/打点/供託は対象外で良いか)/status閾値設計/missingとunknownの切り分け/手牌枚数検証設計/原本JSON運用の現実性。
  - result: 

- [ ] [[2026-06-10_app-urls-canonical-url-normalize]]
  - createdAt: 2026-06-10 21:21
  - app: progress
  - project: progress / アプリURL一覧 入力URLをcanonical URLに正規化して表示（中断作業の仕上げ）
  - priority: medium
  - summary: 前回(commit 3a33325)でURL編集UIを入れた直後に途中で落ちていた「URL正規化」対応を仕上げ。lib/url-normalize.ts新規(normalizeUrlString=スキーム無し→ドメインhttps/IP・localhost http、protocol-relative→https、new URL().hrefでcanonical化、client/server両用純粋関数)。app-urls.tsのnormalizeUrlInputがこれを使用。AppUrlsBoardの入力欄をtype=url→text化しonBlurで正規化、placeholderに自動補完明示。tsc0/lint0/build成功、正規化8ケース単体OK、PATCH実機で160.251.143.146:3010/app-urls→http補完を確認。commit 0d2d730 push。注意: PATCH実機テストを本番data/realに投げ置換セマンティクスでprogressエントリurls配列を上書き→観測事実(pm2/ss/curl)から3件復元(バックアップ機構なし、evidenceDetail細部は要UI確認)。確認観点=スキーム推定の妥当性/onBlur正規化UX/data/real直書き+バックアップ無しの運用リスク。
  - result: 

- [ ] [[2026-06-10_progress-app-urls-user-edit]]
  - createdAt: 2026-06-10 12:31
  - app: progress
  - project: progress / アプリURL一覧をユーザー編集可能にする
  - priority: medium
  - summary: progressのアプリURL一覧(/app-urls)が読み取り専用だったため、画面からURLを手入力・追加・削除・保存できるようにした。lib/app-urls.tsにwriteAppUrls/normalizeUrlInput/applyAppUrlEdit追加(手入力URLはconfidence=documented・evidence=ユーザー入力で正規化、kind/confidenceは許可値のみ、url/label空行は破棄、既存メタは保持)。app/api/app-urls/[appId]/route.tsにPATCH新設(urls配列置換、404=app不在/400=不正body)。AppUrlsBoardのAppCardに「✏️ URL編集」UI(kind選択/ラベル/URL入力/行追加削除/保存PATCH→router.refresh/キャンセル/エラー表示)、保存後iPhone到達性を自動再判定。tsc0/lint0/build成功。一時データ複製(port3099)でE2E確認(編集ボタン/PATCH success/既存メタ保持/新規ユーザー入力既定/total12不変/不在404)、実データ未変更。pm2 restart progressで本番3010反映確認。commit 3a33325 push。確認観点=urls丸ごと置換設計/手入力正規化既定値/data/real直書き安全性/URL形式バリデーションをtype=url依存にした点。
  - result: 

- [ ] [[2026-06-09_news-app-vault-sync-fix]]
  - createdAt: 2026-06-09 20:47
  - app: news-app
  - project: news-app / 市場調査ビューのVault更新停止を調査・修正
  - priority: high
  - summary: news-appの市場調査ビューがVault(06-09まで更新済)を反映せず2026-05-27で停止して見える問題を調査・修正。停止箇所2点=(1)研究ビュー機能一式(force-dynamic/ステータスバー/RescanButton/vault.tsライブルート参照/topic・tag・todoページ/structured parser/summary.ts)が前回セッションで実装されたまま未コミット→Vercel未反映 (2)同梱content/researchが05-27で凍結しVault本体を持たないVercelはfallbackで古い日付しか出せない。修正=scripts/sync-research-content.mjs追加(破壊削除なし/dry-run)で55ファイル同期(05-27→06-09)、package.jsonにsync:research/prebuild追加で再凍結防止、未コミット機能含めcommit 7328f42 push。検証=tsc0/build成功/next start実画面でrootKind=Vault本体・最新06-09・status=最新を確認。Phase4可視化は既存実装で充足済み・今回コミットで本番反映。確認観点=参照先解決の優先順とVercel同梱コピー依存の構造妥当性/prebuildがVercelでexit0する設計(定期コミット前提)/同梱孤立ファイル(--prune未実装)リスク/研究ビュー1コミットpushのスコープ判断。
  - result: 

- [ ] [[2026-06-09_app-urls-vps-public-url]]
  - createdAt: 2026-06-09 01:30
  - app: progress
  - project: progress / URL一覧をiPhone確認用のVPS公開URL形式に修正（Epic完了）
  - priority: medium
  - summary: /vloopでEpic epic-url-iphone-vps-urlを完了まで消化。直前作業(20260608-235115)はlocalhostを一律blockedにしただけで「VPS稼働アプリを公開IP160.251.143.146:<port>/<path>形式で登録」が未達だった点を補完。ss/curl/pm2で実測: 公開IP=160.251.143.146、0.0.0.0 LISTEN=3000(NetScope)/3010(progress)/8080/8888(NetScope docker)、3030(Scrape Lab)未起動、公開IP直curlで全200応答確認。lib/app-urls.tsにvps_internal種別追加(公開IPはok/localhostはvps_internalでblocked)、AppUrlsBoardにVPS内部URLラベル追加・vps=VPS公開URLにリネーム。data/real/app-urls.jsonにprogress/NetScopeの公開IP主URL登録+localhost内部格下げ+memo更新、epics.jsonでepic-url-iphone-vps-urlをdone/100。tsc0/lint0/build成功。pm2 restart progressでlive /app-urls反映確認(公開IP URL7件・iPhone確認URL/VPS内部URL/iPhone直接不可バッジ)。分類=iPhone確認OK2(progress/NetScope)/直接不可1/未確認9。commit c50faa9 push済(d5a8977..c50faa9)。data/realは運用データでコミット対象外(ライブ反映済)。確認観点=公開IP直載せ方針(HTTPS/リバースプロキシ要否)/confirmed(バインド)とremainingWork(外部FW)の粒度/vps・vps_internal分離設計/data/realコミット対象外運用。
  - result: 

- [ ] [[2026-06-08_app-urls-iphone-view]]
  - createdAt: 2026-06-08 23:51
  - app: progress
  - project: progress / アプリURL一覧をiPhone確認用に再設計
  - priority: medium
  - summary: URL一覧がiPhoneから確認するための一覧なのに多くのアプリがlocalhostを主要URL表示しiPhoneで開けなかった問題を修正。lib/app-urls.tsにiPhone到達性分類(classifyRecordAccess: host=localhost/127.0.0.1/0.0.0.0/::1/プライベートIP・kind=ssh_port_forward→blocked, url未確認→unknown, 公開host→ok)とenrichApp(公開URL選好vercel>vps>apiでiphonePrimary決定・actionHint=要Vercel化/要公開URL確認生成)を追加。AppUrlsBoard.tsx新規(iPhone確認URLを緑枠で最優先表示+iPhoneで開くボタン、localhost/内部ポート/未確認枠はdetails折りたたみ+iPhone直接不可バッジ、iPhone確認可否バッジ、すべて/見れる/直接不可/未確認フィルタ+件数、ok→blocked→unknown順ソート)。page.tsxをenrich+board委譲の薄いサーバーコンポーネントに作り直し。tsc0err/build成功(/app-urls 2.78kB)/eslint0err。実データ12件分類確認=NetScope(localhost:3000/8080/8888)・progress(localhost:3010)=blockedで確認URL非表示、news-app=unknown、iPhone確認OK公開URL所持は現状0件(推測URL不作成のため正直に0)。commit d5a8977 push済(9e6af71..d5a8977 origin main)。pm2本番3010は要restartで新UI未反映。確認観点=host基準分類(kind=vpsでもlocalhostはblocked)の妥当性/公開URL選好順とactionHint文言/未確認を推測で埋めない徹底/既存機能非破壊。
  - result: 

- [ ] [[2026-06-08_progress-factory-fix-deploy-to-prod]]
  - createdAt: 2026-06-08 18:47
  - app: progress
  - project: progress / Factory判定修正の本番反映
  - priority: high
  - summary: 3セッション分のFactory判定修正(対象外誤判定/承認過多解消/deployプロンプト注意化)を本番反映。孤立commitはapp/epic/page.tsx→未追跡FactoryGuideModal/FactoryStatusBarの依存連鎖で不可能のため、coherent最小単位としてprogressソース95ファイル(他アプリ・data/・.next・tsbuildinfo除外)を機密スキャン後にcommit 9e6af71→push origin main(d30959e..9e6af71)。npm run build成功(41/41)→pm2 restart progress(restarts=1/online)→GET /epic=200。本番(live 3010)検証: /epicバッジ=⚙Factory対象/✅自律実行可/⚠デプロイ注意/🛡要承認0/🚫対象外(構造不完全のみ)。factory-dispatch scan=factoryEnabled true・candidates[epic-birdlog,epic-progress-url,epic-progress-todo]・blocked[epic-91:doneCriteria/priority未設定]。URL一覧/動作確認Todoとも個別plan safetyStatus=ok/executor=claude/promptType=claude_factory/blockedReasonなし。dispatchプロンプトにdeploy禁止文言なし・デプロイ注意あり。factory-run dry_run=200。data非破壊(コミット外・未変更)。実auto実行は未実施(autonomousコード改変のため候補確認・プロンプト生成・dry_runに留めた)。確認観点=progressソース一式commit判断の妥当性/data実行時JSON扱い/実auto見送り判断/rebuild+restartとpushの責務分離。
  - result: 

- [ ] [[2026-06-08_progress-deploy-prompt-guard-fix]]
  - createdAt: 2026-06-08 17:36
  - app: progress
  - project: progress / 実行プロンプトのdeploy禁止文言を注意扱いへ
  - priority: high
  - summary: Factory判定はdeployを自動実行対象にしたが、Claude/Codexへ渡す実行プロンプトの禁止事項にdeployがハードコード残存(factory-dispatch.ts:178の[6]禁止事項『課金/本番DB/認証/deploy/migration/destructive/secret・token・.env操作をしない』とoperations-store CODEX_SAFETY_GUARDの『Codexで実行してはいけない作業…deploy…』)。判定とプロンプトが不一致だった。修正: lib/epic-contract.tsにbuildExecutionGuard(riskFlags)を新設しforbidden(課金/本番DB/認証/migration/destructive/外部公開/.env等)とcautions(deploy=注意)を分離する単一ソース化。factory-dispatchの[6]禁止事項をhelper駆動にしdeployを除去・deploy時[6-1]注意事項『デプロイ注意(実装/build/lint/typecheck/必要なpushまで可・本番反映は事後報告)』追加。CODEX_SAFETY_GUARDをcodexSafetyGuard(riskFlags)関数化しdeployを禁止から除外。CODEX_DENY_SIGNALSのタイトル語deployは不変(Codex非委譲で安全側維持)。検証(data/real実測): deployのみtest-deploy-only→candidates入り＆picked＆プロンプトにdeploy禁止系出ない([6-1]デプロイ注意のみ)。本物epic-progress-url/epic-progress-todoもcandidates入り・deploy禁止系出ない。billing→safetyStatus=blocked(要承認)維持。tsc0err/build41-41/eslint0err。テストEpicは検証後削除しreal(diff一致)/sample完全復元。URL一覧/動作確認Todoは実行可能(キュー入り＋Claude実行プロンプト上もdeployで止まらない)。git commit/pushは無関係WIP混在で保留(累計18ファイル)。pm2本番3010は要restart。確認観点=deploy注意化後も本番破壊/.env/課金/DBが禁止維持か/両プロンプトの単一ソース一致/CODEX_DENY_SIGNALS残置判断/本番反映=事後報告の運用整合。
  - result: 

- [ ] [[2026-06-08_progress-factory-approval-overflow-fix]]
  - createdAt: 2026-06-08 14:40
  - app: progress
  - project: progress / Factory承認過多の解消（deploy=注意扱い）
  - priority: high
  - summary: 「承認待ち/要承認」過多を解消。原因は前段修正(runId 20260608-133645)でriskFlagsを1つでも持てば一律「要承認」にしていたこと(deploy含む)。riskFlagsを2分割: APPROVAL_RISK_FLAGS(billing/production_db/auth_secret/migration/destructive/external_publish=要承認)とCAUTION_RISK_FLAGS(deploy=承認不要・注意のみ)。evaluateFactoryEligibilityを4概念に分離(factoryManaged=工場処理可/approvalRequired=人間承認要/riskLevel=none|caution|approval/displayBadge=describeFactory)。deploy単独→eligible=true(auto)+⚠デプロイ注意。危険6フラグ→要承認。autonomous+factoryEligible:true+危険なし→auto。manual→対象外。eligible(実行ゲート)はautoのみtrueで危険フラグはブロック維持。変更6ファイル(型/判定/一覧/詳細/作成フォーム/インポート)。tsc0err/build成功41-41/eslint0err/dev3019でHTTP200・/epic要承認0件・対象2件が⚙Factory対象+✅自律実行可+⚠デプロイ注意・詳細対象外0要承認0。合成11ケースで全分岐確認。既存データ補正なし(2件は元から正しい・誤判定はロジック由来)。AI工場オペセンEpicはdoneCriteria/priority未設定で正しく対象外(捏造補正せず)。git commit/pushは無関係WIP混在のため保留(前タスク9+本6=15ファイル選択commit推奨)。pm2本番3010は要restart。確認観点=deploy格上げの安全性/危険6フラグの自動実行ブロック保証/フラグ分類の線引き妥当性/riskLevel3段の十分性/未知フラグのフェイルセーフ/commit保留判断。
  - result: 

- [ ] [[2026-06-08_progress-factory-eligibility-fix]]
  - createdAt: 2026-06-08 13:36
  - app: progress
  - project: progress / EpicカードのFactory対象外誤判定 修正
  - priority: high
  - summary: URL一覧Epic・動作確認TodoページEpicが画面で「Factory対象外」になる原因を特定し修正。実データ(epics.jsonのfactoryEligible:true/autonomous/riskFlags:[deploy])は正しく、evaluateFactoryEligibility(lib/epic-contract.ts)がriskFlags(deploy)/approval_required/pending/blocked/構造不備を全てeligible=false=「対象外」に畳み込む表示ロジックが原因(deployはFACTORY_BLOCKING_RISK_FLAGSのハード除外)。判定を3区分(excluded=factoryEligible false/manual/構造不備, approval=riskFlags/approval_required/pending/blocked, auto=他)に再設計。riskFlagsは「要承認」としてFactory管理対象に残す。eligible(実行ゲート)はautoのみtrueでfactory-dispatch/runner/auto-resumeの自動実行ガードは厳格維持=安全性不変。表示は主バッジ(Factory対象/対象外)+副バッジ(要承認/自律実行可)、describeFactoryで文言統一。変更9ファイル(型/判定/一覧/詳細/おすすめ一覧/詳細/インポート/store/preview型)。tsc0err/build成功/eslint0err/dev3019でHTTP200・対象2件が⚙Factory対象+🛡要承認・詳細対象外表示0を確認。データ補正不要(既にtrue・データ未変更)。git commit/pushは作業ツリーに無関係WIP多数のため保留(ユーザー判断・9ファイル選択commitコマンドをレビューファイルに記載)。pm2本番3010は要restart。確認観点=表示緩和後もdeploy系が承認なし自動実行されない経路保証/区分定義(manual=excluded,approval_required=approval)妥当性/preview後方互換fallback/commit保留判断の妥当性。
  - result: 

- [ ] [[2026-06-08_yomi-ai-human-review-ui]]
  - createdAt: 2026-06-08 10:53
  - app: mahjong
  - project: mahjong / AI良問判定×人間評価すり合わせレビュー画面
  - priority: high
  - summary: AIのS/A良問判定が人間感覚と一致するか検証する画面を/yomi/reviewに新規追加。AIがS/A判定した問題(現状10問 y001-y012)を表示し、問題ID/盤面(YomiBoardView revealed)/河/当たり牌/AIスコア/AI判定理由(読み要素の加点内訳)を提示。人間がS/A/B/Cを採点(localStorage保存)。集計=一致率/過大評価トップ5/過小評価トップ5/AI:A以上×人間:C件数/重み修正案(過大評価に頻出する読み要素を配点引き下げ候補として提示)/コピー用プレーンテキスト。採点ロジックyomi-score.mjsをlib/yomiScore.tsへTS移植しtsxで全10問の出力一致を検証。yomiトップに導線追加。tsc0err/build成功(/yomi/review静的生成)/eslint0err/dev HTTP200で全要素描画確認。mahjong 23b5aed push済。注意=yomi-score.mjsとlib/yomiScore.tsの配点二重管理(変更時は両方同時更新)。確認観点=AI vs 人間一致率で良問判定の質を測る設計の妥当性/delta算出の歪み/修正案ヒューリスティックの妥当性/S/A10問での配点調整は早計でないか/二重管理リスク/採点ガイドUXの要否。
  - result: 

- [ ] [[2026-06-08_yomi-quality-scoring]]
  - createdAt: 2026-06-08 00:44
  - app: mahjong
  - project: mahjong / /yomi 候補品質 自動採点(B→A判定)
  - priority: high
  - summary: 天鳳候補が全件B止まりの問題を解消。scripts/lib/yomi-score.mjsで和了者の河+盤面から読み要素を検出し加点→S/A/B/C判定(スジ引っ掛け+3/無スジ+2/字牌+2/対子落とし+2/リャンメン落とし+2/染め手+2/壁+1/ワンチャンス+1/現物+1/リーチ+1、コア要素なしC、score>=7S/>=5A/>=3B)。reasonsで「なぜA」出力しapplyScoreでqualityRank/dangerLevel/readingBasis/explanationに反映。CLI yomi-quality-score.mjsで候補数/S/A/B/C・A判定サンプル・不採用理由上位をレポート。tenhou-to-yomi.mjsへ採点統合。試験(実データ公開取得0/log/?ref・鳳凰卓4鳳半荘3戦12候補): S3/A5/B4/C0、採用相当8/不採用4、A判定サンプル3件(例 ron7筒 無スジ+ワンチャンス+現物+染め手=score6)、不採用理由上位=4件「コア要素はあるが加点不足」。graded validate全PASS/正本10問PASS/tsc0err/build成功/secret OK/生牌譜削除。canonicalは未変更(自動S/Aは人手レビュー前提)。mahjong 7639773..5fc9fe5 push。注意=配点閾値v1要調整・無スジ×染め手の重複加点。確認観点=配点閾値妥当性/A率8-12は高すぎないか/重複加点補正/検出誤りリスク/自動S/A採用の前提。
  - result: 

- [ ] [[2026-06-07_yomi-tenhou-ingest-trial]]
  - createdAt: 2026-06-07 23:45
  - app: mahjong
  - project: mahjong / 天鳳公開牌譜 取り込み試験
  - priority: high
  - summary: 天鳳の公開鳳凰卓牌譜URLを少数取得し/yomi取り込みパイプラインを実データ試験。公開アーカイブsc/raw(list.cgi→scc{YYYYMMDDHH}.html.gz)から四鳳南喰赤(4人鳳凰半荘)の?log=URLを3件特定。取得経路実証: find.cgi?log=は非会員ERROR(認証突破しない)、tenhou.net/0/log/?{ref}が生mjlog XMLを認証/Cookie無しで返す→1件のみ一時取得(13KB)。scripts/tenhou-to-yomi.mjs新規(XML解析: seed/draws TUVW/discards DEFGでtedashi/tsumogiri判定/REACH/AGARI who-fromWho-machi、牌id 0-135デコード、放銃者=self相対席、当たり牌=ロン牌、distractor=和了者の現物、idハッシュ化でgameId非保持、qualityRank未設定でB保留)。1牌譜→ロン局5件抽出(turn>=10・4人河)→validate全件PASS→ingest投入→採用0/保留5(自動生成=読み筋未検証=B、S/Aのみ採用で不採用=設計通り)→--rawで生牌譜削除確認→正本yomi-questions.json 10問不変。tsc0err/build成功/secret OK/diffにref混入なし。mahjong 4993af4..7639773 push(変換器+docsのみ。生牌譜/候補/refは非コミット)。報告: 探したページ=sc/raw・list.cgi・scc・nnkr(参考)/候補URL3/取得1/採用0/不採用理由=自動生成で読み筋未検証(B)/生牌譜削除=確認済。確認観点=公開URL限定取得の妥当性/採用0(B止まり)の品質判断/gameId非保持で再配布リスク低減十分か/読み筋ヒューリスティックの優先順位。
  - result: 

- [ ] [[2026-06-07_yomi-ingest-no-raw-retention]]
  - createdAt: 2026-06-07 22:48
  - app: mahjong
  - project: mahjong / /yomi 取り込み 生牌譜非保管・処理後削除
  - priority: high
  - summary: 牌譜取り込みを「生牌譜を保管・公開せず問題データのみ保存し処理後に元牌譜削除」へ改修(利用規約=牌譜再配布禁止対応)。scripts/ingest-yomi.mjs新規(候補JSON→整合検証→品質判定S/A採用・B保留・C/D隔離→重複除外(id+盤面signature)→正本マージ→採用後再検証→--rawの元牌譜削除→集計、--dry-run対応)。scripts/lib/yomi-validate.mjs新規(検証共有化・source.gameId/mjlog/xml/mjai/raw等の牌譜ポインタ保持をエラー化)。scripts/yomi-stats.mjs新規(sourceRank/sourceType別=鳳凰卓/魂天/王座問題数集計)。types/yomi.tsのYomiSourceをsourceType/sourceRank/importedAtへ整理(gameId等廃止)、lib/yomi.tsにgetYomiSourceStats追加、.gitignoreでdata/rejected配下も無視しコミットはyomi-questions.jsonのみ。保持=問題/解説/読み筋/危険度/カテゴリ/出典種別、非保持=生牌譜/mjlog/xml/mjai/gameId。検証=ingest E2E(/tmp合成候補5→採用2(houou,ouza)/保留1/隔離2(整合エラー+gameId保持禁止)・raw削除確認・dry-runで削除なし・正本10件不変)/validate全10問PASS/yomi-stats動作/tsc0err/next build成功/secret OK/意図変更のみstaged。mahjong cb802fc..4993af4 push済。旧『元牌譜削除禁止』とingestの削除の矛盾を解消(一時生牌譜削除は正規動作・唯一原本の独断削除は禁止)。牌譜由来問題の公開可否は人間の法務判断(承認必須)。確認観点=再配布リスク低減の十分性/ingestの安全性(誤削除取りこぼし)/ポインタ禁止と追跡性のトレードオフ/mjlog→候補変換の残課題。
  - result: 

- [ ] [[2026-06-07_tenhou-paifu-collection-research]]
  - createdAt: 2026-06-07 21:22
  - app: mahjong
  - project: mahjong / 天鳳 公開牌譜 収集方法の調査（/yomi用）
  - priority: high
  - summary: /yomi用に天鳳の公開牌譜を認証不要・Cookie不要で集める方法を調査。結論=公開フル牌譜は鳳凰卓(scc)のみ(sc/raw年次zip scrawYYYY.zip+list.cgi約5分毎・認証不要)。特上卓上位の公開フル牌譜は本人DLのみで入手不可だが、鳳凰卓=七段+/特上点到達の最上位公開帯なので高品質目的は達成。推奨ツール=Apricot-S/houou-logs(Python/MIT・phoenix-logs後継、fetch→download→export・レート制御内蔵)+mjlog2json(XML→JSON)/tenhou-to-mjai(→mjai)。実装難易度=低〜中(収集低・変換低・mjai→yomiアダプタ中)。継続収集=可(list.cgi増分+年次zip+SQLite差分)。自動化=技術的に可(cron・同時1session/20分間隔マナー必須)。最大リスク=天鳳/houou-logs双方が牌譜の再配布を禁止→生牌譜のGitHub pushは規約抵触・/yomi適用もグレー。対策=.gitignoreに生牌譜(*.mjlog/data/imported配下xml/json/raw/tenhou)除外を追加(git check-ignore検証・READMEはtracked維持)、調査をdocs/tenhou-collection.mdに整理。実データ収集は未実行。公開可否は人間の法務判断(承認必須・自走しない)。mahjong cb802fc push済。確認観点=規約リスク評価/鳳凰卓のみで品質達成の妥当性/自動化の線引き/代替ソース。
  - result: 

- [ ] [[2026-06-07_yomi-ingestion-rule]]
  - createdAt: 2026-06-07 19:00
  - app: mahjong
  - project: mahjong / /yomi 牌譜取り込みルール・パイプライン土台
  - priority: high
  - summary: 実戦牌譜を承認待ちせず/yomiへ自走取り込みする運用ルールを恒久化し、アプリ側に土台を整備。docs/yomi-ingestion.md(取り込み先/条件10項/ソース優先度4帯/品質ランクS-D/標準フロー/報告フォーマット)、scripts/validate-yomi.mjs(4人河・ロンツモ・correctTile=hiddenTile∈waits・選択肢一意・リーチ後手出し違反・牌4枚・10巡目・読み根拠の整合を機械検証し終了コードで採用可否)、types/yomi.tsにYomiSource/YomiQualityRankと任意source/qualityRank追加、data/imported(B保留)・data/rejected(C隔離/D破棄・削除でなく理由付き隔離)を新設。方針=問題数より品質優先・上級者帯(魂天/王座/魂の間/鳳凰卓)優先・採用S/Aのみ。禁止=元牌譜削除/既存良問大量削除/force push/課金API/牌譜外部送信/認証情報保存/本番データ削除。CLAUDE.local.md・メモリにも反映。検証=validate全10問PASS/tsc0err/build成功/secret OK。mahjong main 1b98f02..9e64d13 push済(local=origin/clean)。実牌譜は未提供(土台のみ)。確認観点=取り込み条件/品質基準の妥当性・validate検証の抜け・自走の事故ポイント。
  - result: 

- [ ] [[2026-06-07_forward-progress-rule]]
  - createdAt: 2026-06-07 16:02
  - app: company-meta
  - project: 運用ルール / 前進優先ルール（承認待ちで停止しない）
  - priority: high
  - summary: ユーザー指示で『承認待ちで停止しない』前進優先ルールを恒久化。承認必須を6点(本番データ削除/DBスキーマ破壊変更/外部課金発生/認証権限変更/不可逆操作=force push・履歴改変・復元不能削除/高セキュリティリスク)に限定し、通常のcommit&push含むそれ以外は実装→検証→GitHub反映まで事後報告で自動実施。CLAUDE.local.mdに§前進優先ルール(2026-06-07)を追記し旧『push/deploy明示指示まで保留』と§commit後push必須化/§GitHub反映ルールのpush待ち保留を上書き。機密スキャン・push前後チェックは継続必須・本番デプロイは6点該当時のみ確認。メモリfeedback-execution-confirmation-policyも同期。適用として前タスク(/yomi品質改善)の未pushを2コミット(804d12e卓レイアウト/1b98f02品質改善)に分けmahjong mainへpush成功(582fdd7..1b98f02 FF/local=origin/clean)。確認観点=承認必須6点の線引き妥当性・安全策の十分性・旧ルール上書きの漏れ矛盾。
  - result: 

- [ ] [[2026-06-07_mahjong-yomi-quality-improve]]
  - createdAt: 2026-06-07 14:17
  - app: mahjong
  - project: mahjong / 当たり牌読み 問題品質改善（実戦読み訓練化）
  - priority: medium
  - summary: /yomiを暗記問題から実戦読み訓練へ改善。型にdangerLevel(危険度1-5)とreadingBasis(読み根拠)を追加。問題を12→10問に厳選(良問優先で残置)し河を巡目相当(13-17巡)に拡張。旧y005のリーチ後手出し(対子落とし=ルール違反)をダマに修正、材料不足/重複の3問(旧y003/旧y010/y011)を削除。全10問に無スジ/スジ/スジ引っ掛け/現物/壁(ノーチャンス)/字牌処理(生牌)/対子落とし/リャンメン落とし/ツモ切り手出し読み/親リーチ打点読み/字牌単騎変則待ち の読み根拠を付与。当たり牌の無スジ/スジ判定が対面河と整合するよう盤面を再構成。回答後UIに危険度★★★★★と読み根拠セクションを追加。検証=tsc0err/next build成功/自作nodeスクリプトで牌4枚制限・correctTile=hiddenTile=waits整合・選択肢に当たり牌混入なし・hiddenWinTileが放銃者に1枚・リーチ後tedashi無し を全10問PASS。既存「何切る」quiz/result無変更。変更=types/yomi.ts, data/yomi-questions.json, app/yomi/quiz/page.tsx。未commit/push(mahjongリポジトリ・ユーザー指示待ち)。
  - result: 

- [ ] [[2026-06-07_mahjong-yomi-table-layout]]
  - createdAt: 2026-06-07 11:17
  - app: mahjong
  - project: mahjong / 当たり牌読み 盤面を麻雀卓レイアウト化
  - priority: medium
  - summary: /yomi盤面を縦4段スタックから麻雀卓配置(雀魂風: 対面上/上家左/下家右/自分下)へ全面刷新。牌を席別回転(self0/toimen180/kamicha270/shimocha90)、河は6枚折返し、上家下家は縦積み(下家90度は最新chunk中央寄りで逆順)。リーチ宣言牌は(席回転+90)で横向き、ツモ切り半透明+青点、副露はプレイヤー脇、当たり牌のみ？枠維持→回答後に実牌+amber ringでreveal。中央パネル=局/本場/供託/巡目/ドラ、各家=自風/座席/点数/リーチ/和了/放銃。解説部分(quiz/result)は無変更。TileDisplayに非破壊のplaceholderプロップ追加(？枠が牌footprintを回転込みで保持)。検証: tsc0err/build成功(全9ルート静的・API非依存)、headless Chromium 390pxで横スクロール0・卓box358x450収まる・？描画→reveal・読み筋表示。既存何切るBoardView無変更。変更=components/YomiBoardView.tsx, components/TileDisplay.tsx。未commit(本タスクではcommit/push未指示)。
  - result: 

- [ ] [[2026-06-06_mahjong-yomi-verify-deploy]]
  - createdAt: 2026-06-06 23:27
  - app: mahjong
  - project: mahjong / 当たり牌読みモード 仕上げ確認・本番反映
  - priority: medium
  - summary: 実装済み/yomi当たり牌読みモードの仕上げ確認とGitHub反映。git差分=HomeContent導線ボタン追加(additive)+yomi新規のみ・デバッグ残りなし。headless Chromium(iPhone幅390px)で/yomi・/yomi/quiz・/yomi/resultを確認: 横スクロール0px・河4ゾーン1行6枚見切れなし・牌選択肢タップ領域84x59px・当たり牌のみ？で伏せ→回答後にreveal・読み筋/危険理由/待ち形/役/解説/他選択肢否定/復習モード全表示。データ12問整合(correctTile∈choices,==result.hiddenTile,∈waits,ron時loser最終打牌==hiddenTile,tsumo時河伏せ0,河4人非空,turn>=10)pass。tsc0err/build成功(全9ルート静的・API非依存)。commit 582fdd7をorigin/mainへpush成功(221eec4..582fdd7 FF)。未対応=Vercel本番URL確認・実機Safari目視。実装run=20260606-220319/本run=20260606-232717。
  - result: 

- [ ] [[2026-06-06_mahjong-atari-yomi-mode]]
  - createdAt: 2026-06-06 22:03
  - app: mahjong
  - project: mahjong / 当たり牌読み(捨て牌読み)練習モード新設
  - priority: medium
  - summary: 既存「何切る」クイズ(mahjong)を非破壊で流用し、当たり牌だけを「？」で伏せて予想する捨て牌読み練習モードを別ルート/yomiに新設。4人河(1行6枚)+手出し(濃)/ツモ切り(薄+青点)+リーチ宣言牌(赤枠)+副露+ドラ+場風自風+点数+巡目を表示し、当たり牌のみ伏せる。回答後に正解牌/ロンツモ/和了者/放銃者/待ち形/役/読み筋/危険理由/他選択肢の否定理由を表示。ホームに難易度/タグ/問題数選択、結果画面に正答率+復習モード(誤答のみ再出題)。問題12問新規(10巡目以降・読み要素あり=リーチ/ダマ/染め手ホンイツ/対子落とし/リャンメン落とし/スジ引っ掛け/壁/字牌/手出しツモ切り/単騎/カンチャン/親リーチ/ツモ)。TileDisplayスプライト再利用。API非依存・全9ルート静的のままtsc0err/build成功。整合スクリプトで全12問(correctTile∈choices, ==result.hiddenTile, ∈waits, loser最終打牌==hiddenTile, 河4人非空, turn>=10)pass。変更=types/yomi.ts,lib/yomi.ts,data/yomi-questions.json,components/YomiBoardView.tsx,app/yomi/{page,quiz/page,result/page}.tsx,components/HomeContent.tsx。未対応=iPhone実機確認・問題拡充・commit/push(未実施)。
  - result: 

- [ ] [[2026-06-04_monetization-vault-sync]]
  - createdAt: 2026-06-04 12:27
  - app: progress
  - project: AI工場 / 収益化候補 定期取り込み・調査元可視化
  - priority: high
  - summary: Factory定期実行(11:00/23:00/boot)時にVault(/obsidian-sync-vault の候補表・06_research・20_reviews)を走査し、未反映の収益化候補をMonetization Hub(monetization-candidates.json=正本)へ取り込む機能を、既存/monetizationを拡張して実装(新規ページ/二重JSON/二重APIなし)。新規→追加、既存→sourceRefs/researchLogs/evidenceLinks/historyに追記のみ(Vaultは読み取り専用・相互上書きなし)。型にSourceRef/EvidenceLink追加・ResearchLog/HistoryEntry拡張。runScheduledFactory先頭にbest-effort同期ステップ追加、POST /api/monetization/sync追加。詳細に調査元一覧/根拠リンク/取込履歴、一覧に調査元数バッジ。重複判定=id/slug/title/targetApp/類似名/既存Epic/既存アプリ/既存candidate。安全=候補追加のみ・Epic化しない・epics.json不変・public/billing/deploy/secret非接触・二重追加なし(冪等)。検証:added6/updated6→2回目0/0/skip12、birdlog sourceRefs6、過剰一致を英語フレーズ条件で解消、ExecutionRun(monetization_sync)記録、tsc/lint/build OK・deploy。
  - result: 

- [ ] [[2026-06-04_factory-scope-rule]]
  - createdAt: 2026-06-04 01:35
  - app: progress
  - project: AI工場 / 作業範囲ルール修正
  - priority: high
  - summary: AI工場の作業範囲をEpic生成ロジックに反映。収益化候補/おすすめ候補のMVP Epicを autonomous/riskFlags:[]/factoryEligible:true 化(ローカル実装・検証のみ=AI工場自走範囲)。Google Play/App Store公開・審査申請(external_publish/manual)と課金/サブスク/AdMob本番・支払い情報(billing/approval_required)を別Epic候補(slug-publish/slug-billing)に分離。external_publishは公開Epicだけ・billingは課金Epicだけに付与。安全ゲートFACTORY_BLOCKING_RISK_FLAGSは不変(緩和なし)。検証: AnglerLog MVP承認→epic-anglerlog-mvp が autonomous/[]/factoryEligible true=自動実行対象(承認Run warnings空)、公開=manual/external_publish/対象外・課金=approval_required/billing/対象外に分離、tsc/lint/build OK。テスト生成物除去・recommended再生成20件。変更=lib/monetization-store.ts, lib/recommended-epics-store.ts。
  - result: 

- [ ] [[2026-06-04_factory-run-history-extend]]
  - createdAt: 2026-06-04 00:10
  - app: progress
  - project: AI工場 / Factory定期実行履歴（二重実装回避・既存拡張）
  - priority: medium
  - summary: 「Factory定期実行履歴ページ」新規作成前に既存調査→『既存に同等機能あり』判定。/logs?mode=history が全ExecutionRunを表示しexecution-runs.jsonが単一正本、別JSON(factory-runs等)も専用APIも無し。よって/factory-runs新設・別JSON複製・二重API・新ナビは行わず、既存/logsを拡張: (1)『🏭定期実行のみ』フィルタ(source=schedule/boot/factory_runner・trigger=systemd/cron/startup・factoryRun=trueで絞る) (2)ExecutionRunCardにFactoryメタ(src/trig/Run#/stop)。検証: 全215→定期実行のみ29に絞込み、factoryページ全カードに🏭メタ、tsc/lint/build OK、pm2 deploy。新規ページ0/JSON0/API0/ナビ0。
  - result: 

- [ ] [[2026-06-03_recommended-epics]]
  - createdAt: 2026-06-03 21:07
  - app: progress
  - project: AI工場 / おすすめ追加Epic
  - priority: high
  - summary: Progressに「おすすめ追加Epic」機能を実装(/recommended-epics 一覧, /recommended-epics/[id] 詳細)。Vault(収益化候補)/active未完Epic/失敗Runから追加Epic候補を抽出→人間承認した候補だけepics.jsonへ追加。新規Epic化と既存EpicへのNext Action追記の2種別。抽出生成API(suggestedのみ)・状態遷移API・承認API(重複チェック→Epic Contract→createEpic→epic_created→ExecutionRun source=recommended_epics)。自動Epic追加禁止・承認は人間のみ・安全ゲート不変(deploy/billing/migration/auth_secret/production_db/destructive自動実行なし・external_publishはApproval必須)。検証:tsc/lint/build OK、API実機(生成8/dedupe/詳細/承認新規Epic化/二重登録409/既存Next Action追記/hold/reject/Factory判定)確認、テスト生成物除去・recommended-epics.json再生成で8件suggested整合。pm2 deploy済。未配線=定例11:00/23:00/起動時の自動抽出(統合点POST用意)。
  - result: 

- [ ] [[2026-06-03_monetization-hub]]
  - createdAt: 2026-06-03 14:41
  - app: progress
  - project: AI工場 / 収益化候補管理（Monetization Hub）
  - priority: high
  - summary: Progressに収益化候補管理機能を新規実装(/monetization 一覧, /monetization/[id] 詳細)。発掘→調査→人間レビュー→Epic化承認→Factory実行の導線。候補CRUD・状態遷移(Draft/Candidate/Review/Hold/Approved/Rejected/EpicCreated/Building/Released)・調査履歴API、Epic化API(重複チェック→Epic Contract生成→epics.json追記→status EpicCreated→操作履歴→ExecutionRun source=monetization_hub)。自動Epic化禁止・人間ボタンのみ。data/real/monetization-candidates.json 新設(発掘済み6件seed)。iPhone優先カードUI(スコア/状態/ブルーオーシャン色分け)。検証:tsc/lint/build OK、API実機(一覧/詳細/追加/更新/調査ログ/Epic化)確認、重複防止(mahjong/progress/二重Epic化)を409でブロック確認、テスト生成物除去。pm2 deploy済。Vault同期方針(正本分離・書き戻し手動)を提案。未配線=11:00/23:00定例の自動候補投入(統合点POST /api/monetizationは用意)。
  - result: 

- [ ] [[2026-06-02_market-discovery-cycle2]]
  - createdAt: 2026-06-02 20:46
  - app: company-meta
  - project: AI工場 / 市場調査・新規アプリ発掘（定時継続業務 第2サイクル）
  - priority: high
  - summary: 定時継続発掘業務の第2サイクル。前回発掘5件(#9-13)をcandidate登録済として除外し、Vault在庫(local-first-app-ideas-2026-05-14 未昇格案)から収益化順に新規5件発掘(1位 野鳥観察ノートBirdLog 83/2位 自家焙煎ジャーナル 76/3位 卓上ゲーム戦績手帳 74/4位 発酵食ジャーナル 70/5位 盆栽育成カレンダー 67)。全件 local-first・API不要・静的JSON・習慣/記録/収集/趣味系・日本+海外両対応。除外: 既存アプリ/Epic/candidate(001・005)/前回5件/ドメイン重複(打牌採点・詰将棋)/飽和(クラフトビア)。収益化候補一覧.mdに第2サイクル表(#14-18)追記。1位 BirdLog のFactory登録候補Epic Contract JSON生成(登録未実施=Epic生成まで/external_publish→approval_required)。次回観点=05-28以降の新規daily取得+各カテゴリ競合ASO実査で再スコア。
  - result: 

- [ ] [[2026-06-02_new-app-epic-discovery]]
  - createdAt: 2026-06-02 20:22
  - app: company-meta
  - project: AI工場 / 新規アプリ候補の横断発掘
  - priority: high
  - summary: Vault/Progress Epic/Research/news-app daily を横断し、既存資産(mahjong/shogi/news/progress/NetScope/Goal Planner/candidate-001・005等)と重複しない新規アプリ候補を収益化順に5件発掘(1位 釣果ログ AnglerLog/2位 部位レスト筋トレ/3位 香水台帳/4位 レコード台帳/5位 AI日めくりパズル)。全件 local-first・API不要・静的JSONで成立、日本+海外両対応・AI開発相性◎。出典 local-first-app-ideas-2026-05-14(idea段階)を2026-05-27市場シグナル(Play Shorts縦動画ASO/汎用AIチャット飽和)で再評価し昇格。収益化候補一覧.mdに新規発掘テーブル追記→push 7435e89。1位のFactory登録候補Epic Contract JSONを生成(登録は未実施=Epic生成まで/external_publish→approval_required)。
  - result: 

- [ ] [[2026-05-31_codex-adapter-fix]]
  - createdAt: 2026-05-31 21:09
  - app: progress
  - project: progress / AI工場統合 / Factory完成・Codex adapter修正
  - priority: high
  - summary: Codex adapterのstdin hang(『Reading additional input from stdin』で300s timeout)を根本修正。真因はstdinではなく、shell helperの if(opts.input) が空文字''をfalsy判定しstdin.end()を呼ばず、stdin pipeが開いたままcodexがEOF待ちしていたこと。修正:input未指定時はspawnのstdinを'ignore'(/dev/null相当)、指定時のみpipe+write+end。codex/claude adapterのinput:''除去。併せてCodex安全判定が他Epic由来のglobal nextActionsの『削除』を誤検知してblockする問題も、意図テキストをEpic固有のgoal+doneCriteriaに限定して修正。codex起動はcodex exec -C <dir> --skip-git-repo-check -s workspace-write。完了条件の実運転(実データ): simulateRateLimit→AutoFallback(auto_fallback記録/canRunOnCodex=true)→Codex実行(completed 174s・hangせず)→runChecks(typescript=OK/lint=OK)→ExecutionRun記録(20260531-210606 executorUsed=codex/source=factory_runner/runnerMode=auto/factoryRun=true/runStatus=completed)→doneCriteria 3/4(continue)まで成功。criterion1(1箇所変更)のみ未達はrunnerのchangedFilesが既存dirtyファイルで空判定のため(本番clean repoでは非発生)。Claude→Codex時系列を報告に収録。検証はprogress repoで実施しcodexがapp/automation/page.tsxを文言修正→検証後tarスナップショットで全復元(epic-91のみ/OFF/factory_runner run 0/git status 51件=自作業intact)。net変更ゼロ。P2-P4は本タスク対象外(次回)。残課題=executor cwdサンドボックス化。
  - result: 

- [ ] [[2026-05-31_done-criteria]]
  - createdAt: 2026-05-31 20:09
  - app: progress
  - project: progress / AI工場統合 / doneCriteria自動判定
  - priority: high
  - summary: Epic ContractのdoneCriteriaをExecutionRunから自動評価しdone/continueを判定するエンジンを実装(executor非依存・Claude/Codex共通)。L1機械判定(build/typecheck/lintをchecks-runnerで実行しExecutionRun.checksへOK/NG構造化保存)/L2 changedFiles一致/L3 summary・rawReportとのbigram曖昧一致(閾値text0.18/files0.25/汎用0.3)/meta(ExecutionRun記録・未承認作業)/L4全達成done・L5一部continue。factory-runnerに組込み(従来のnextActions空停止→runChecks実行→doneCriteria verdict停止に置換、done=epic_done停止/continue=次Run)。API /api/operations/done-criteria。UI=Automation FactoryProgressCardとEpic詳細にdoneCriteria達成状況(✓/✗・level・evidence・達成率N/M・DONE/CONTINUEバッジ)。判定可=build/typecheck/lint(L1高精度)・変更系(L2/L3)・ExecutionRun/未承認(meta)。判定弱=主観品質・外部条件・否定・日本語同義語。誤判定リスク=L3偽陽性/checks未記録偽陰性/件数条件のchangedFiles長依存→Epic done自動マークはせず停止に留め人/Approval判断。検証:build/tsc/lint OK、合成でDONE4/4・CONTINUE2/4、実運転=実claude(174s)→ExecutionRun(runner checks tsc/lint OK+claude自己POST changedFiles=app/epic/page.tsx)→doneCriteria集約4/4→epic_done停止。安全=実行前tarスナップショット+データ復元(epic-91のみ/OFF/runs0)、claude編集はapp/epic/page.tsxのblockedラベル1語(停止中→ブロック中)のみharness判断で保持・tsc clean。新正本なし。
  - result: 

- [ ] [[2026-05-31_factory-live-smoke]]
  - createdAt: 2026-05-31 19:42
  - app: progress
  - project: progress / AI工場統合 / Factory実運転疎通テスト
  - priority: high
  - summary: 実データでFactory実運転を疎通確認(新機能追加なし・実運転のみ)。テストEpic投入(epic-progress/eligible=true)→Factory ON→auto maxRuns=1 confirm→実claude -p起動(146s)→completed→ExecutionRun記録(runId 20260531-192701/source=factory_runner/runnerMode=auto/factoryRun=true/executorUsed=claude/changedFiles=progress/app/page.tsx/nextActions=空)→max_runs_reached停止(nextActions空のdoneCriteria要確認停止も成立)。Claude上限シミュ(claude-limit force)はauto_fallback記録(automation-log5件)+safetyGuard+canRunOnCodex=trueだが、progressスコープのpending_approvalタスクで要承認→安全にblocked(codex_readyは別途クリーンスコープで確認済runId155036)。UI状態Running(ON+dispatchable)/Paused(OFF)確認、Blocked/CodexReadyはロジック実装済。問題点=runner同期実行で146sブロック/実claude -pがprogress repo実編集(cwd分離要)/doneCriteria自動判定未。安全対応=実行前tarスナップショット+Claude編集(app/page.tsx 1行)をgit checkout復元+データ5ファイル復元→net変更ゼロ・git status元の48件・tsc OK。残課題=doneCriteria自動評価/runner background化/cwdサンドボックス/複数Epic(P2)/スケジュール有効化(承認後)/通知。完全自動化の最小ループが実データで疎通成功。
  - result: 

- [ ] [[2026-05-31_factory-runner-mvp]]
  - createdAt: 2026-05-31 19:04
  - app: progress
  - project: progress / AI工場統合 / Factory実行自動化MVP
  - priority: high
  - summary: Dispatch Planを「作るだけ」から「executorへ渡して実行→ExecutionRun記録→Epic内ループ」へ前進。Phase A実機調査でclaude(-p v2.1.158→PONG)/codex(exec v0.135.0)の非対話起動が可能と確認。executor adapter(claude/codex/manual)共通IF(status/stdout/stderr/resultSummary/changedFiles/errorType/rateLimited/needsApproval/nextActions)+getAdapterレジストリ(executor非依存)。factory-runner: scan→pick→Dispatch→adapter Run→ExecutionRun自動記録(factoryRun/source=factory_runner/runnerMode)→Epic内ループ。caps=1起動最大3Run/同一Epic最大3Run(無限ループ防止)。基本Claude→result.rateLimited時にAutoFallback評価(ON&canRunOnCodex)→Codex、不可なら停止記録。Approval発生時createApproval+停止/blocked/fail停止/nextActions空でdoneCriteria要確認停止。モード=dry_run(既定・実起動なし・記録なし)/manual(manual adapter・記録あり・手動実行待ち停止)/auto(confirm必須・claude -p/codex exec実起動)。codex adapterは実行前にclassifyCodexEligibilityで安全判定二重化。スケジュール(systemd service/timer毎朝11時/boot/cron)はdocs/factory-schedule/に未有効化の設計案のみ(enable=ユーザー承認後)。検証:build/tsc/lint OK、claude -p→PONG、T1 OFF→factory_off、T2 dry_run 1step記録なし、T3 manual→ExecutionRun記録、T4 auto confirm無→auto_requires_confirm/実起動なし、検証後全復元(epic-91のみ/OFF)。auto実epicは未実行(ファイル変更回避)。新正本なし・既存安全ゲート不変。次=ユーザー承認後に安全な小Epicでauto最大1Run疎通。
  - result: 

- [ ] [[2026-05-31_factory-on-off]]
  - createdAt: 2026-05-31 18:07
  - app: progress
  - project: progress / AI工場統合 / Factory ON/OFF
  - priority: high
  - summary: Factoryの完全自動運転を開始・停止できるON/OFFを実装。AutomationConfigにfactoryEnabled追加(既定OFF・旧config後方互換でgetAutomationConfigを既定マージ化)。scanFactoryDispatchをfactoryEnabledでゲート→OFF時は一切scanしない(候補空)、ON時のみscan→pick。factory-statusにfactoryRunState(Running/Paused/Blocked/CodexReady)追加(OFF→Paused、ON&上限&Codex可→CodexReady、ON&candidates>0→Running、ON&candidates0→Blocked)。Automation最上位のFactory進行状況カードにON/OFFトグル(automation-config POST factoryEnabled)と状態バッジ。automation-config POSTのwhitelistにfactoryEnabled追加。既存Factory Dispatch(手動・開発者モード)はscan結果に接続済み、OFF時はヒント表示。cron/pm2/systemd/Hermes/Executor追加/完全無人化は未実装。確認4点回答(ON=scan→pick→Plan→プロンプト生成まで自動・実起動は手動/OFF=scan即停止/疎通条件=Factory ON+開発者モードON+対象Epic1件/Loop前不足=ループ本体・サーキットブレーカ・executor抽象・実起動手段・factory_loop source)。レビューにFactory状態遷移図・ON/OFF仕様・疎通テスト手順を収録。検証:build/tsc/lint OK、T1 OFF→Paused/scan0、T2 ON+不完全→Blocked、T3 ON+dispatchable→Running、後方互換確認、検証後復元(OFF/epic-91のみ)。新正本なし・既存ロジック不変。次=Factory ON+開発者モードで手動Dispatch1周の疎通テスト。
  - result: 

- [ ] [[2026-05-31_codex-handoff-policy]]
  - createdAt: 2026-05-31 17:42
  - app: progress
  - project: progress / AI工場統合 / Codex引き継ぎ運用方針変更(Executor非依存)
  - priority: high
  - summary: 「ユーザーはExecutorを意識しない」方針へ変更。手動Codex引き継ぎ(CodexHandoffPanel:Automation/Epic詳細)・Factory Dispatchの手動executor選択・AutoFallback手動上書きをDevModeGateで開発者モード限定化(既定OFFで非表示)。AutoFallbackの自動検知バナーは常時表示の正規パスとして残す。Automation最上位に状態中心のFactoryProgressCardを新設(state優先順位 実行中>承認待ち>停止中>再開待ち>Codex準備完了>アイドル、Factory ON/OFF、対象Epic、停止理由、実行待ち/承認待ち/停止件数、Claude利用状況none/detected/ambiguous、実行Executor参考、最終実行/Fallback日時+理由+status、次回予定)。computeFactoryStatusは既存health/config/claude上限検知/auto resume/dispatch scan/automation log/ExecutionRunの派生ビュー(新正本なし)。確認5点回答:手動引き継ぎは開発者モード限定が推奨(削除/非表示と比較しコード温存+疎通テスト両立)。検証:build/tsc/lint OK、factory-status=停止中(factoryOff)、dev OFFで手動UI非表示・自動検知残る・epic-91 200、データ変更なし。次の一手=開発者モードONで手動Dispatch1周→OFFで状態中心運用の疎通テスト。
  - result: 

- [ ] [[2026-05-31_factory-dispatch]]
  - createdAt: 2026-05-31 16:56
  - app: progress
  - project: progress / AI工場統合 / Factory Dispatch準備フェーズ
  - priority: high
  - summary: Factory本体の前段。選んだEpicをClaude/Codex/manual executorへ渡す準備を実装。scan→pick→Dispatch Plan(生成ビュー・新正本なし)→executor選択(requiresClaude→claude/canRunOnCodex&safety OK→codex/riskFlags・承認待ち・決定待ち・manual→dispatch不可)→Claude用プロンプト(安全判定/Goal/DoneCriteria/RiskFlags/現在地/NextActions/禁止事項/ExecutionRun記録指示)・Codex用プロンプト(既存generateCodexPrompt再利用・安全判定冒頭)→コピー→結果戻し(CodexReportFormにdispatchPlanId付与でExecutionRunにfactoryDispatch/dispatchMode=manual_copy/executorCandidate/promptGenerated/resultReturned記録)。生成時はAutomationLog factory_dispatch記録。UIはAutomationにFactory Dispatch（準備）セクション(候補Epic/Plan詳細/プロンプト生成/コピー/blocked理由/結果戻し)。完全自動実行・Codex/Claude CLI直叩き・cron/pm2/systemd・無限ループなし。新正本なし(正本はExecutionRun)・executor非依存(preferred/fallback両対応)。検証:build/tsc/lint OK、scan(picked=テストEpic/candidates1/blocked2[epic-91契約不足・危険Epic deploy])・claude_factory/codex_handoffプロンプト・危険Epic→null・結果戻し永続化・AutomationLog×3、検証後復元(epic-91のみ/factoryDispatch run 0)。次の一手=小Epic投入で手動疎通1周テスト。
  - result: 

- [ ] [[2026-05-31_epic-contract]]
  - createdAt: 2026-05-31 16:29
  - app: progress
  - project: progress / AI工場統合 / Epic契約形式整備(自動化テスト前提)
  - priority: high
  - summary: Epic作成を明示的な契約形式に整備。必須(title/goal/doneCriteria/decisionPolicy[autonomous/approval_required/manual]/priority[P0-P2]/riskFlags[7種])+任意(notes/targetApp/relatedRepo/preferred・fallbackExecutor/factoryEligible)。3方式=画面フォーム(doneCriteria動的行・riskFlagsチェックボックス・モバイル優先)/JSONインポート(① preview dryRun→② confirm)/JSONテンプレコピー(入れ子コードブロックなし)。バリデーション(title/goal/doneCriteria空→error、policy/priority不正→error、riskFlags未知値→error)。Factory対象判定evaluateFactoryEligibility(goal非空&doneCriteria≥1&policy/priority設定&manual除外&危険riskFlags[billing/production_db/auth_secret/deploy/migration/destructive]除外&external_publishもApproval必須で除外&Approval待ち0&status≠blocked&factoryEligible≠false)。Auto Resumeにepic-scope eligibilityゲート追加(安全ゲートは不変)。新正本なし(epics.json拡張)・既存Epic破壊なし・ExecutionRun削除なし・executor非依存。自動化テスト用Epic JSON同梱([[../03_prompts/epic-contract-test-epic.json]])。検証:build/tsc/lint OK、不正JSON→5エラー/テストEpic→eligible/deploy・manual→対象外/実作成epic-progress→eligible/既存epic-91→対象外で壊れない、検証後復元(epic-91のみ/autoResume=false)。次の一手=このテストEpicをJSON投入して最初の自動化テスト開始。
  - result: 

- [ ] [[2026-05-31_ai-factory-next-phase]]
  - createdAt: 2026-05-31 15:50
  - app: progress
  - project: progress / AI工場統合 / 次フェーズ(Auto Resume/Approval即処理/Factory設計)
  - priority: high
  - summary: AI工場完成に向けた次フェーズ。Phase1 Auto Resume実装(Claude上限後に安全作業だけ自動継続。既存AutoFallback安全ゲートを再利用しfeature-toggleのdisabledのみ除外、state=running/paused/blocked/auto_resumed、再開対象件数・最終再開時刻、記録先ExecutionRun(source=auto_resume)+AutomationLog、executor非依存pickResumeExecutor)。Phase2 Approval即処理(Epic詳細でインライン承認/却下/保留→既存decide API→Decision Log自動保存→次回vloop反映、/approvalsは横断受信箱維持)。Phase3 Factoryオーケストレーションは設計docのみ(状態遷移図/対象・対象外/AutoResume・Automation・Approval・Decisionとの4境界/Claude・Codex・Both運用フロー/DecisionPolicy 3値標準化検討/残作業7項目)。Progress唯一の管制塔・新正本なし・handoff非正本・Claude/Codex専用設計禁止・Factory本体未実装を遵守。検証:build/tsc/lint OK、AutoResume(OFF→paused/ON実データ→blocked(safety)/cleanScope→running→POST→auto_resumed+ExecutionRun+AutomationLog)、Approval即処理(Epic詳細描画+decide(推奨B)→Decision Log保存epicId付き)、検証後tmp全復元(161runs/approvals0/autoResume=false)。設計図は[[../06_research/factory-orchestration-design]]。progressコードは未commit(ユーザー判断)。
  - result: 

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
