# レビューキュー

> ChatGPTレビュー用の入口ファイル。
> iPhone Obsidianでは、このチェック状態をレビュー状態の正本として扱う。

## 未レビュー

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
