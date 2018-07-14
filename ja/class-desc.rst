==================
クラス・機能の説明
==================

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exec/Crawler.java
 - Javaプロセスとして実行されるクロールのクラス
 - クロールジョブが実行されたときに呼ばれる
 - コマンドオプション
  - -s: セッションIDを指定する
  - -n: 名前を指定する
  - -w: ウェブコンフィグIDを指定する
  - -d: データコンフィグIDを指定する
  -         public String dataConfigIds;
  - -p: プロパティーパスを指定する
  - -e: 削除期限を指定する

 - ゲッター
  - ウェブコンフィグIDリストを返す
  - ファイルコンフィグIDリストを返す
  - データコンフィグのIDリストを返す
  - コンフィグIDリストを生成する

 - toString
  - セッションID、名前、ウェブコンフィグID、ファイルコンフィグID、データコンフィグID、プロパティーパス、削除期限の文字列を返却する

 - メイン
  - オプション生成する
  - コマンドラインパーサーに渡して実行する
  - デバッグログを出力
  - システムプロパティを設定する
    - トランスポートアドレス //TODO どういうアドレスなのか調べる
    - クラスタ名
  - try
    - シングルトンLaコンテナファクトリを生成する //TODO laコンテナについて調べる
      - コンフィグパス(app.xml)を設定する
      - エクスターナルコンテキストを設定する //TODO エクスターナルコンテキストについて調べる
      - エクスターナルコンテキストDefレジスターを設定する
    - シャットダウン用スレッドを生成する
    - 終了コードにプロセスの返却値を入れる
  - catch
  　 - クローラの停止のログを出力する
  　 - クローラが機能していないログを出力する
   - finally
  　 - クローラを消去する
   - 終了コードが正常終了でない場合に終了させる

 - メソッド：シングルトンlaコンテナを削除する
 - メソッド：プロセス
   - クローラーを生成する
   - オプション：セッションIDがあれば設定する(日付をyyyyMMddHHmmss形式で設定する)
   - クロール情報ヘルパーを生成する
   - ダイナミックプロパティを生成する(?)
   - try
     - オプション：プロパティパス有り
       - 設定する
     - オプション：プロパティパス無し
       - クローラプロパティの一時ファイル作成
       - 一時ファイルを削除する。
       - システムプロパティを一時ファイルで更新する
       - deleteOnExit //TODO :L249 プロパティファイルの終了コードを削除する?
   - catch
     - ファイル入出力エラーのログを出力する

   - try
     - クロール情報ヘルパーを生成する
     - オプション：削除期限の値：有り
       - 削除期限を設定する
     - オプション：削除期限の値：無し
       - FessConfigから削除期限を設定する
     - クロール情報ヘルパーを生成する
   - catch
     - クロール情報の設定失敗のログを出力する

   - try
     - クローラを生成する
   - finally
     - try
       - クロール情報を格納する
     - catch
       - クロール情報格納に失敗したらログに出力する
     - クロール情報のinfoログを出力

     - try
       - クロール情報をメール送信
     - catch
       - メール送信失敗のログを出力する

 - メソッド：渡されたマップをメール送信する
   - FessConfigの設定があればメールを作成する

 - メソッド：Fromマップの値を取得する

 - メソッド：クロールを実行する
   - try
     - セッション情報の時間を書き出す

     - セッションIDとパスマッピング情報をパスマッピングヘルパーにセットする
     - duplicateHostHelperを初期化する
     - duplicateHostHelper // TODO duplicateHostHelperがどういうものか調べる

     - deleteSessionIdsBefore // TODO セッションIDに紐づく削除期限を削除する？


     - ウェブ・ファイルコンフィグIDリストがあればウェブ・ファイルクロールのスレッドを生成する
     - ウェブ・ファイルコンフィグIDリストがあればデータクロールのスレッドを生成する

     - スレッドをクローラスレッドにジョインする

     - 終了コードを返却
   - catch
     - Throwableをログに出力する
   - finally
     - セッションIDに紐づくパスマッピングヘルパーを削除する
     - クロール情報のマップにステータスを渡す
     - セッション情報のクローラ終了時間を出力する
     - クロール情報にクローラ実行時間を設定する

 - メソッド：セッション情報時間を出力する
   - クロール情報に時間を設定する(yyyy-MM-dd'T'HH:mm:ss.SSSZ)

 - メソッド：クローラスレッドをジョイン(スレッドの終了を待機)する

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exec/ThumbnailGenerator.java
  - サムネイル生成用のインターフェイス
    - Options
      -s セッションID -n 名前 -p プロパティパス -t スレッド番号 -c クリーンアップ 
      toString オプションに設定された値を返す
    - initializeProbes
      プロセスProve（監視）
      OSProove
      JVMinfoのインスタンス生成
    - main(arg)
    　オプション生成
      オプションを引数にコマンドラインパーサー生成
      try
      　argを引数にコマンドラインパーサーのparseArgumentメソッド呼ぶ
      catch
        システムエラー　当クラスのCanonical名を出力
        オプションと使用方法を画面に出力
        return
      
      デバッグ有効時
        JVM起動パラメータ、プロパティ、環境変数、オプションをデバッグログへ出力する
        
      Constansから取得したEsClientの転送先アドレスを設定する
      Constansから取得したEsClientのクラスター名を設定する
      TimeoutTaskクラスをsystemMonitorTaskとして定義するする
      exitcodeを定義する
      
      try
      	シングルトンLaコンテナファクトリのコンフィグファイルパスにapp.xmlを設定する
      	シングルトンLaコンテナファクトリのエクスターナルコンテキストにGenericExternalContextインスタンスを設定する
      	シングルトンLaコンテナファクトリのエクスターナルコンテキストデフォルトレジスターに
      	GenericExternalContextComponentDefRegisterインスタンスを設定する
        シングルトンLaコンテナファクトリのinitメソッドを実行する
        ShutdownHookスレッドインスタンスを生成する
        runメソッドをオーバーライド(Threadクラスが継承するRunnableインターフェース)
	        デバッグ有効時
	        		Laコンテナ削除のメッセージを出力する
	        	コンテナを削除する
        RuntimeクラスのshutdownHookにShutdownHookスレッドインスタンスを登録(VMのshutdown)する（securityManagerでチェック）
      	systemMonitorTaskにaddTimeoutTargetメソッドの戻り値TimeoutTaskを設定する
      	引数：SystemMonitorTargetインスタンス、サジェストシステムモニター期間のint(60)、true
      	プロセスメソッドを実行し、サムネイルの合計を変数に格納する
      	合計が0でなければサムネイルファイルの合計をログに出力する
      	0の場合はサムネイルが作成されなかったログを出力する
      	終了コード0を設定する
      	catch ContainerNotAvailableExceptionが起きた場合（コンテナが利用不可）
      		デバッグ有効時：サムネイルジェネレーター停止したログメッセとExceptionログ出力
      		インフォ有効時：サムネイルジェネレーター停止したログメッセ
      		終了コード：1失敗
      	catch Throwable　上記以外のエラーが起きた場合
      		エラー：サムネイルジェネレーター異常ログメッセとThrowableログ出力
      		Error, RuntimeException, Exception全てcatchする
      		サムネイル生成処理なので処理ないで起きたエラーでアプリを止めないため
      		終了コード：1失敗
      	finally
      		SystemMonitorTargetがnullでない場合cancelメソッドを実行する
      		コンテナを削除する
      	System.exitにexitCodeを設定する
    
    - destroyContainer
    		privateメソッド
    		SingletonLaContainerFactoryクラスをsynchronized（排他制御：ロック）
    			SingletonLaContainerFactoryのdestroyメソッドを実行する
    - process(Options)
    		DynamicPropertiesクラスをインスタンス化する。SystemProperties
    		optionのpropertiesPathがからでない場合
    			systemPropertiesをリロード(propertiesPath)
    		else
    			try
    				propFile：一時ファイルを生成する。"thumbnail_+createTempFileメソッドによって命名+.properties”
    				propfileのdeleteメソッドを実行して正常終了し、loggerがデバッグ有効の場合
    					デバッグログに一時ファイル削除のログメッセと一時ファイルの絶対パスを出力する。
    					一時ファイル(絶対パス）ををリロードする。systemProperties
    			catch IOException
    				WARNログを出力する。
    				システムプロパティーファイルの作成失敗ログメッセとException
    				
    			totalCountを0に設定する
    			countを1に設定する。
    			options.numOfThreads(スレッド数)を引数にForkJoinPoolをインスタンス化する。
    			countが0でない場合
    				ComponentUtilのThmbnailManagerメソッドのgenerateメソッドを実行し、戻り値をcountに設定する（引数：pool, potions.cleanup）。
    				→ThumbnailManagerクラスを参照
    				totalCountにcountを加算する。
    			return
    				totalCountを返す。
    				

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exec/SuggestCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/crawler/interval/FessIntervalController.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/crawler/FessCrawlerThread.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/crawler/transformer/FessTikaTransformer.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/crawler/transformer/AbstractFessFileTransformer.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/crawler/transformer/FessXpathTransformer.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/crawler/transformer/FessTransformer.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/crawler/transformer/FessFileTransformer.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/ParameterUtil.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/QueryStringBuilder.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/MemoryUtil.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/FacetResponse.java
  - ファセットのレスポンス用ユーティリティ
  	- クエリーカウントマップ、フィールドリスト
  	- FacetResponse(Aggregations aggregations)
  	- elasticsearchのaggregations(グループ)の内容を取得する
	  	- filedで始まるグループを取得してフィールドリストに追加する
	  	- queryで始まるグループを取得してqueryを除いたencodeQueryを取得する
	  		- UTF-8でデコードしたクエリー、ドキュメント数をクエリーカウントマップに設定する
	- hasFacetResponse()
		- クエリーカウントマップかフィールドリストが空でなければtrueを返す
	- Field
		- valueカウントマップ、name
		- コンストラクタ：Field(Terms termFacet)
			- termFacetの名前からfieldを除いたencodeFieldを取得する
			- UTF8ででコードした結果をnameに入れる
			- termFacetからBuckets(検索結果の分類)ごとに処理をする
				- valueカウントマップにBuvketのKey文字列、ドキュメントカウントを追加する

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/JobProcess.java
	- Jobプロセスのインスタンス化を行う
	- Process, InputStreamThreadを保持する
	
	
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/ResourceUtil.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/RenderDataUtil.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/KuromojiCSVUtil.java
	- KuromojiCSVユーティリティ
	- parse(final String line)
		- CSVパース処理をする
	- unQuoteUnEscape(final String original)
		- 置換パターンに一致する文字列のエスケープクォートを外す
	- quoteEscape(final String original)
		- 置換パターンに一致する文字列にエスケープを施す
	
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/QueryResponseList.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/InputStreamThread.java
	- Threadを継承したInputStreamThreadクラス
	- BufferedReader, 最大バッファサイズ、リスト（linkedList：前後の要素情報も保持し、要素の追加・削除が速い)
	- コンストラクタ：InputStreamThread(InputStream is, String charset)
		- is、charsetを元にInputStreamReaderをインスタンス化してBufferedReaderをインスタンス化する
	- run()
		- Threadのrun()オーバーライド
			- BufferedReaderがnullでなければlistに追加
			- 最大バッファサイズよりlistサイズが大きい場合はlistの1つめの要素を削除する
	
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/ComponentUtil.java
  - DIコンテナに登録するクラス（コンポーネント）を登録する
    - loggerインスタンスを取得する
    - 登録するクラスの宣言をする
    - ComponentUtilのコンストラクタ
    - processAfterContainerInit(final Runnable process)
    		- プロセスを実行するか、初期化プロセスのリストに追加する
    		- availableメソッドでシステムヘルパーのコンポーネントが取得できる場合
    			- プロセスを実行する
    		- そうでない場合
    			- initProcessesリストにプロセスを追加する
    	- doInitProcesses(final Consumer<? super Runnable> action)
    		- initProcessesリスト分actionの処理を実行し、例外は呼び出し元にthrowする
    - ゲッター 
    		- cipher名が一致するCachedCipherを返す(cipher:暗号)
    		- QueryResponseListを返す
    		- DynamicProperties(システムプロパティ)を返す（クローラープロパティ）
    		- SystemHelperを返す（(nullかホットデプロイ有効の場合はコンポーネントを取得する)
    		- ViewHelperを返す
    		- SambaHelperを返す
    		- QueryHelperを返す
    		- LableTypeHelperを返す
    		- SearchLogHelperを返す
    		- CrawlingConfigHelperを返す(nullかホットデプロイ有効の場合はコンポーネントを取得する)
    		- PopularWordHelperを返す
    		- PathMappingHelperを返す
    		- PathMappingHelperを返す
    		- DuplicateHostHelperを返す
    		- ProcessHelperを返す
    		- JobHelperを返す
    		- WebApiManagerFactoryを返す
    		- UserAgentHelperを返す
    		- DatastoreFactoryを返す
    		- IntervalControlHelperを返す
    		- ExtractorFactoryを返す
    		- JobExecutorを返す
    		- FileTypeHelperを返す
    		- IndexUpdaterを返す
    		- KeyMatchHelperを返す
    		- IndexingHelperを返す(nullかホットデプロイ有効の場合はコンポーネントを取得する)
    		- UserInfoHelperを返す
    		- MessageManagerを返す
    		- DictionaryManagerを返す
    		- DataService<EsAccessResult>を返す
    		- FessEsClientを返す
    		- FessConfigを返す(nullの場合はコンポーネントを取得する)
    		- SuggestHelperを返す
    		- RoleQueryHelperを返す
    		- LdapManagerを返す
    		- ActivityHelperを返す
    		- RequestManagerを返す
    		- ResponseManagerを返す
    		- JobManagerを返す
    		- DocumentHelperを返す
    		- QueryParserを返す
    		- PermissionHelperを返す
    		- SsoManagerを返す
    		- ThumbnailManagerを返す
    		- AuthenticationManagerを返す
    		- PrimaryCipherを返す
    		- CrawlerClientFactoryを返す
    		- RelatedQueryHelperを返す
    		- RelatedContentHelperを返す
    		- VirtualHostHelperを返す
    		- AccessTokenHelperを返す
    		- QueryStringBuilderを返す
    		- CurlHelperを返す
    	- <T> T getComponent(final Class<T> clazz)
    		- try
    			- SingletonLaContainerのコンポーネントを返す
    		- catch (final NullPointerException)
    			- デバッグログ有効時
    				- ContainerNotAvailableExceptionにクラスの正規名とExceptionを渡してthrowする
    			- 上記以外の場合
    				- ContainerNotAvailableExceptionにクラスの正規名を渡してthrowする
    - <T> T getComponent(final String componentName)
    		- try
    			- 引数のコンポーネント名からコンポーネントを取得して返す
    		- catch (final NullPointerException)
    			- デバッグログ有効時
    				- ContainerNotAvailableExceptionにクラスの正規名とExceptionを渡してthrowする
    			- 上記以外の場合
    				- ContainerNotAvailableExceptionにクラスの正規名を渡してthrowする
    
    - Helperが存在する場合はtrueを返す
    		- hasViewHelper
	    	- hasQueryHelper
    		- hasPopularWordHelper
    		- hasRelatedQueryHelper
    	- available
    		- システムヘルパーがnullでなければtrueを返す
    		- 上記以外の場合はfalseを返す
    	- setFessConfig(final FessConfig fessConfig)
    		- ConponentUtilのfessConfigに引数のfessConfigを設定する
    		- fessConfigがnullの場合
    			- FessPropのpropMapを削除する
    
    
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/GroovyUtil.java
	- Groovyスクリプトを実行する
		- パラメータマップからGroovyShellをインスタンス化してtemplateを実行する
		
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/OptionalUtil.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/WebApiUtil.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/DocList.java
	- Documentのリスト
	- clear()
		- ArrayListを消去して
		- contentSizeとprocessingTimeを0に設定する
	- getContentSize()
		- contentSizeを返す
	- ProcessingTime()
		- processingTimeを返す
	- addProcessingTime()
		- processingTimeを加算する
	- toString()
		- DocList coontentSize,processingTime, elementData（配列マップサイズ)

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/DocMap.java
	- Documentのオブジェクトマップ
	- マップに対する操作のoverride処理
	- entrySet()
		- Set<Map.Entry<String, Object>>を返す
		- クラス内で定義したMap(parent)のキーにlangが含まない場合
			- parent.entrySet()の結果を返す
		- parent.entrySet()を初期値にList<Map.Entry<String, Object>>をインスタンス化する
			- listをソートする
				- 第一オブジェクト、第二オブジェクトに分けてo1,o2とする
				- o1のキーを取得するk1
				- o1がlangの場合
					-1を返却する(異常ステータス)
				- -2のキーを取得するk2
				- o2がlangの場合
					- -1を返却する(異常ステータス)
				- k1とk2を結合して返却する
			- Collectionsのソート処理を抜けた場合
				- listを初期値にLinkedHashSet<>をインスタンス化して返却する

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/util/DocumentUtil.java
	- Documentのユティリティに関するクラス
	- 引数のnullチェックをする
	- <T> T getValue(final Map<String, Object> doc, final String key, final Class<T> clazz, final T defaultValue)
		 - docマップ、キー、クラスを元にvalueを取得する
	- <T> T getValue(final Map<String, Object> doc, final String key, final Class<T> clazz)
		- keyをもとにdocのvalueを取得する
		- valueがListオブジェクトの場合
			- isAssignableFrom(クラス同士の比較)クラスがListクラスの場合
				- valueを返す
			- Listオブジェクトのvalueがからの場合
				- nullを返す
			- プライベートメソッドのconvertObjでクラスを変換して返す
	- <T> T convertObj(final Object value, final Class<T> clazz)
		- Listオブジェクトがどのクラスかを判別して返却する
	- String encodeUrl(final String url) 
		- requestされたurlをエンコード処理する
		- requestの文字コードを取得してnullでなければ,streamのmapにつめる
		- requestの文字コードがOptionalなのでなければUTF-8が入る
		- urlの長さ+100のStringBuilderをインスタンス化する
		- urlのchar配列を取得して1charごとに処理する
		- charがurl使用可能文字であればbufに追加する
		- そうでない場合
			- charのURLエンコード化して追加する
			- サポートされてないエンコードの場合はcharをbufに追加する
		- bufのtoStringメソッドを返却する
			

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/UnsupportedSearchException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/SearchQueryException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/ContainerNotAvailableException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/UserRoleLoginException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/DataStoreException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/WebApiException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/InvalidQueryException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/FessSystemException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/ServletRuntimeException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/SsoLoginException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/DataStoreCrawlingException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/ResultOffsetExceededException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/LdapOperationException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/exception/FessUserNotFoundException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/protwords/ProtwordsFile.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/protwords/ProtwordsItem.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/protwords/ProtwordsCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/seunjeon/SeunjeonCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/seunjeon/SeunjeonItem.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/seunjeon/SeunjeonFile.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/DictionaryManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/synonym/SynonymItem.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/synonym/SynonymFile.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/synonym/SynonymCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/DictionaryException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/DictionaryFile.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/mapping/CharMappingFile.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/mapping/CharMappingItem.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/mapping/CharMappingCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/kuromoji/KuromojiCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/kuromoji/KuromojiItem.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/kuromoji/KuromojiFile.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/DictionaryCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/DictionaryExpiredException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/dict/DictionaryItem.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/BadWordService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/DuplicateHostService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/GroupService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/FailureUrlService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/CrawlingInfoService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/KuromojiService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/SynonymService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/SearchService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/RequestHeaderService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/WebAuthenticationService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/UserInfoService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/KeyMatchService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/FileConfigService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/CharMappingService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/BoostDocumentRuleService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/RoleService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/PathMappingService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/ProtwordsService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/JobLogService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/ScheduledJobService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/FileAuthenticationService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/DataConfigService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/LabelTypeService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/ElevateWordService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/SearchLogService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/SeunjeonService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/UserService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/RoleTypeService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/WebConfigService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/service/FavoriteLogService.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/logic/AccessContextLogic.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/osdd/OsddAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/logout/LogoutAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/help/HelpAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/error/ErrorBadrequrestAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/error/ErrorForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/error/ErrorSystemerrorAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/error/ErrorNotfoundAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/error/ErrorAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/profile/ProfileAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/profile/ProfileForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/thumbnail/ThumbnailForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/thumbnail/ThumbnailAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/search/SearchAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/go/GoForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/go/GoAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/FessSearchAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/FessAdminAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/FessBaseAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/FessLoginAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/login/OpenIdConnectCredential.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/login/SpnegoCredential.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/login/ActionResponseCredential.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/base/login/FessLoginAssist.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/CrudMode.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/login/LoginAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/login/LoginForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dashboard/AdminDashboardAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/backup/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/backup/AdminBackupAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/duplicatehost/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/duplicatehost/AdminDuplicatehostAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/duplicatehost/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/duplicatehost/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/protwords/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/protwords/DownloadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/protwords/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/protwords/AdminDictProtwordsAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/protwords/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/protwords/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/seunjeon/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/seunjeon/AdminDictSeunjeonAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/seunjeon/DownloadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/seunjeon/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/seunjeon/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/seunjeon/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/ListForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/synonym/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/synonym/AdminDictSynonymAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/synonym/DownloadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/synonym/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/synonym/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/synonym/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/AdminDictAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/mapping/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/mapping/DownloadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/mapping/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/mapping/AdminDictMappingAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/mapping/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/mapping/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/kuromoji/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/kuromoji/DownloadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/kuromoji/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/kuromoji/AdminDictKuromojiAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/kuromoji/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dict/kuromoji/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/labeltype/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/labeltype/AdminLabeltypeAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/labeltype/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/labeltype/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileauth/AdminFileauthAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileauth/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileauth/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileauth/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webauth/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webauth/AdminWebauthAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webauth/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webauth/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/user/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/user/AdminUserAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/user/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/user/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/role/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/role/AdminRoleAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/role/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/role/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/scheduler/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/scheduler/AdminSchedulerAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/scheduler/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/scheduler/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webconfig/AdminWebconfigAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webconfig/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webconfig/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/webconfig/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/systeminfo/AdminSysteminfoAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/joblog/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/joblog/AdminJoblogAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/joblog/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/upgrade/UpgradeForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/upgrade/AdminUpgradeAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/badword/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/badword/DownloadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/badword/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/badword/AdminBadwordAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/badword/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/badword/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/group/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/group/AdminGroupAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/group/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/group/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/log/AdminLogAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/general/AdminGeneralAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/general/MailForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/general/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/keymatch/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/keymatch/AdminKeymatchAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/keymatch/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/keymatch/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/reqheader/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/reqheader/AdminReqheaderAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/reqheader/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/reqheader/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/esreq/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/esreq/AdminEsreqAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/design/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/design/FileAccessForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/design/AdminDesignAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/design/DesignForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/design/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/boostdoc/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/boostdoc/AdminBoostdocAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/boostdoc/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/boostdoc/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/pathmap/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/pathmap/AdminPathmapAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/pathmap/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/pathmap/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/elevateword/UploadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/elevateword/DownloadForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/elevateword/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/elevateword/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/elevateword/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/elevateword/AdminElevatewordAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dataconfig/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dataconfig/AdminDataconfigAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dataconfig/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/dataconfig/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/searchlist/ListForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/searchlist/DeleteForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/searchlist/AdminSearchlistAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/searchlist/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/searchlist/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/failureurl/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/failureurl/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/failureurl/AdminFailureurlAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileconfig/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileconfig/AdminFileconfigAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileconfig/CreateForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/fileconfig/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/AdminAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/crawlinginfo/SearchForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/crawlinginfo/AdminCrawlinginfoAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/crawlinginfo/EditForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/wizard/AdminWizardAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/wizard/IndexForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/wizard/StartCrawlingForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/admin/wizard/CrawlingConfigForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/RootAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/sso/SsoAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/cache/CacheAction.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/web/cache/CacheForm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/UserPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/WebAuthPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/JobLogPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/RoleTypePager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/LabelTypePager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/RolePager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/PathMapPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/KeyMatchPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/BoostDocPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/FileConfigPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/KuromojiPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/ProtwordsPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/BadWordPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/WebConfigPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/FailureUrlPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/SeunjeonPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/SynonymPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/SchedulerPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/GroupPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/ReqHeaderPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/FileAuthPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/CrawlingInfoPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/CharMappingPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/DuplicateHostPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/ElevateWordPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/pager/DataConfigPager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/job/AllJobScheduler.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/app/job/ScriptExecutorJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/UserAgentHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/UserInfoHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/WebFsIndexHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/FileTypeHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/SystemHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/SambaHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/JobHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/DataIndexHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/ViewHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/CrawlingConfigHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/QueryHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/SearchLogHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/OpenSearchHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/PermissionHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/RoleQueryHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/CrawlerLogHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/CrawlingInfoHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/SuggestHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/IntervalControlHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/ActivityHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/KeyMatchHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/PathMappingHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/IndexingHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/DuplicateHostHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/LabelTypeHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/ProcessHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/PopularWordHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/helper/DocumentHelper.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/indexer/DocBoostMatcher.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/indexer/IndexUpdater.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/ElevateWordToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/DataConfigToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/CrawlingInfoParamBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/RequestHeaderBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/BoostDocumentRuleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/WebAuthenticationBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/ScheduledJobBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/DataConfigBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/DuplicateHostBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/WebConfigToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/WebConfigBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/WebConfigToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/JobLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/PathMappingBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/LabelToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/LabelTypeBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/DataConfigToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/KeyMatchBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/FailureUrlBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/BadWordBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/FileConfigToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/FileConfigBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/CrawlingInfoBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/ElevateWordBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/FileAuthenticationBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/RoleTypeBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exbhv/FileConfigToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsCrawlingInfo.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsElevateWordToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsCrawlingInfoParam.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsWebConfigToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsBadWord.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsWebAuthentication.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsWebConfigToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsFileAuthentication.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsLabelToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsRoleType.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsWebConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsScheduledJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsDuplicateHost.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsFileConfigToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsDataConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsRequestHeader.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsDataConfigToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsKeyMatch.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsElevateWord.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsDataConfigToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsFileConfigToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsLabelType.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsPathMapping.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsBoostDocumentRule.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsFailureUrl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/CrawlingInfoParamDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/PathMappingDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/WebConfigToRoleDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/BadWordDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/FileConfigDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/JobLogDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/ElevateWordToLabelDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/DataConfigToLabelDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/ElevateWordDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/DataConfigDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/DataConfigToRoleDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/LabelTypeDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/WebConfigDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/WebAuthenticationDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/WebConfigToLabelDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/FailureUrlDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/LabelToRoleDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/FileConfigToLabelDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/DuplicateHostDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/BoostDocumentRuleDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/RequestHeaderDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/KeyMatchDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/ScheduledJobDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/FileConfigToRoleDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/FileAuthenticationDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/RoleTypeDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/dbmeta/CrawlingInfoDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsFileConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsentity/BsJobLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/allcommon/EsAbstractConditionQuery.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/allcommon/EsPagingResultBean.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/allcommon/EsAbstractConditionBean.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/allcommon/EsAbstractBehavior.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/allcommon/EsAbstractEntity.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/allcommon/EsSqlClause.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/PathMapping.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/RoleType.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/CrawlingInfo.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/ElevateWord.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/LabelToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/LabelType.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/DataConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/BadWord.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/ScheduledJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/FileAuthentication.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/WebConfigToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/DataConfigToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/CrawlingInfoParam.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/CrawlingConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/FileConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/FailureUrl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/FileConfigToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/WebAuthentication.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/RequestHeader.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/WebConfigToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/BoostDocumentRule.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/FileConfigToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/KeyMatch.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/ElevateWordToLabel.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/DuplicateHost.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/DataConfigToRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/JobLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/exentity/WebConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsLabelTypeBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsRoleTypeBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsScheduledJobBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsBoostDocumentRuleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsCrawlingInfoBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsDuplicateHostBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsFailureUrlBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsWebConfigToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsCrawlingInfoParamBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsFileConfigBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsElevateWordToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsFileConfigToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsFileConfigToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsElevateWordBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsKeyMatchBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsDataConfigToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsPathMappingBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsWebAuthenticationBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsDataConfigToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsFileAuthenticationBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsWebConfigToLabelBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsRequestHeaderBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsJobLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsWebConfigBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsBadWordBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsLabelToRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/bsbhv/BsDataConfigBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/LabelToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/WebConfigToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/DataConfigCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/CrawlingInfoCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/WebConfigCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/BoostDocumentRuleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/JobLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/BadWordCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/RequestHeaderCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/PathMappingCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/DuplicateHostCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/ElevateWordToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/DataConfigToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/LabelTypeCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/KeyMatchCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/FileConfigToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/ScheduledJobCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/FailureUrlCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/FileAuthenticationCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/ElevateWordCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/RoleTypeCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/FileConfigToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/DataConfigToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/WebAuthenticationCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsElevateWordToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsDuplicateHostCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsWebConfigCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsJobLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsBadWordCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsFileConfigCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsWebConfigToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsFileConfigToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsFailureUrlCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsLabelToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsScheduledJobCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsDataConfigToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsLabelTypeCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsRequestHeaderCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsCrawlingInfoCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsPathMappingCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsWebAuthenticationCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsElevateWordCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsWebConfigToLabelCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsBoostDocumentRuleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsDataConfigCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsFileConfigToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsRoleTypeCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsCrawlingInfoParamCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsDataConfigToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsKeyMatchCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/bs/BsFileAuthenticationCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/CrawlingInfoParamCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/FileConfigCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/cq/WebConfigToRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/ElevateWordToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/CrawlingInfoCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/WebConfigToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/FileAuthenticationCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/JobLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/LabelTypeCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/KeyMatchCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/DataConfigToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/WebConfigToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/PathMappingCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/BadWordCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/BoostDocumentRuleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/LabelToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/ElevateWordCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/WebAuthenticationCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/RequestHeaderCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/FileConfigToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/FileConfigCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/DuplicateHostCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/ScheduledJobCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/FailureUrlCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/RoleTypeCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/FileConfigToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsDataConfigToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsCrawlingInfoCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsWebConfigToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsJobLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsFileConfigToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsLabelTypeCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsElevateWordCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsKeyMatchCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsBoostDocumentRuleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsRoleTypeCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsRequestHeaderCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsLabelToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsScheduledJobCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsElevateWordToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsBadWordCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsFileAuthenticationCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsPathMappingCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsFileConfigCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsWebAuthenticationCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsDataConfigCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsWebConfigCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsDataConfigToRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsDuplicateHostCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsFileConfigToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsWebConfigToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsCrawlingInfoParamCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/bs/BsFailureUrlCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/DataConfigCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/DataConfigToLabelCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/WebConfigCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/config/cbean/CrawlingInfoParamCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/exbhv/UserBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/exbhv/GroupBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/exbhv/RoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsentity/BsGroup.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsentity/BsUser.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsentity/BsRole.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsentity/dbmeta/RoleDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsentity/dbmeta/UserDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsentity/dbmeta/GroupDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/allcommon/EsAbstractConditionQuery.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/allcommon/EsPagingResultBean.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/allcommon/EsAbstractConditionBean.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/allcommon/EsAbstractBehavior.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/allcommon/EsAbstractEntity.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/allcommon/EsSqlClause.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/exentity/Group.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/exentity/Role.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/exentity/User.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsbhv/BsUserBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsbhv/BsGroupBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/bsbhv/BsRoleBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/cq/UserCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/cq/GroupCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/cq/bs/BsGroupCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/cq/bs/BsRoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/cq/bs/BsUserCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/cq/RoleCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/GroupCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/UserCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/RoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/bs/BsRoleCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/bs/BsUserCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/user/cbean/bs/BsGroupCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exbhv/UserInfoBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exbhv/FavoriteLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exbhv/SearchFieldLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exbhv/ClickLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exbhv/SearchLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/BsSearchLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/BsUserInfo.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/BsClickLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/BsFavoriteLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/BsSearchFieldLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/dbmeta/ClickLogDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/dbmeta/FavoriteLogDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/dbmeta/SearchLogDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/dbmeta/UserInfoDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsentity/dbmeta/SearchFieldLogDbm.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/allcommon/EsAbstractConditionQuery.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/allcommon/EsPagingResultBean.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/allcommon/EsAbstractConditionBean.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/allcommon/EsAbstractBehavior.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/allcommon/EsAbstractEntity.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/allcommon/EsSqlClause.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exentity/UserInfo.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exentity/FavoriteLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exentity/ClickLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exentity/SearchFieldLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/exentity/SearchLog.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsbhv/BsSearchFieldLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsbhv/BsSearchLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsbhv/BsFavoriteLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsbhv/BsClickLogBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/bsbhv/BsUserInfoBhv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/SearchFieldLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/ClickLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/FavoriteLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/bs/BsSearchLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/bs/BsClickLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/bs/BsFavoriteLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/bs/BsUserInfoCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/bs/BsSearchFieldLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/SearchLogCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/cq/UserInfoCQ.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/ClickLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/SearchLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/UserInfoCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/FavoriteLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/bs/BsFavoriteLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/bs/BsUserInfoCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/bs/BsSearchFieldLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/bs/BsSearchLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/bs/BsClickLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/log/cbean/SearchFieldLogCB.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/client/FessEsClient.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/es/client/FessEsClientException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/Constants.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/validation/UriTypeValidator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/validation/CronExpression.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/validation/CronExpressionValidator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/validation/UriType.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/taglib/FessFunctions.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/thumbnail/impl/BaseThumbnailGenerator.java
  - サムネイル生成用の抽象クラス
  　ThumbnailGeneratorインターフェースの実装
  	コンディションマップの生成
  	ディレクトリ名の長さ5を設定
  	ジェネレーターリスト宣言
  	ファイルパスマップ生成
  	名前宣言
  	最大リダイレクト数10を設定
  	利用可能フラグnullを設定
  
  - addCondition(final String key, final String regex)
  	引数のキーからコンディションマップの値を取得する
  	値が空ならコンディションマップにキーと正規表現を設定する
  	そうでなければコンディションマップにキーと値|正規表現を設定する
  	
  - isTarget
    conditionMapのvalueに一致した文字があるかを判定
    
  - isAvalable
    - generatorListがnullか空でなければ
      - PATH,Path,pathでパスを取得し、値があればpathListに入れる
      - PathListのpathでファイル生成→生成できればfilePathMapにgeneratorとfilePathを入れる
      - generatorListが{$path}で始まっている場合
      	- pathListのパスでgeneratorListの{$path}を置換する
      	- ファイルが存在する場合
      		- filePathに絶対パスを設定する
      		- filePathMapにパスと絶対パスを設定する
      		- filePathを返却する
      	- generatorListのパスを返却する
      - 全generatorListがファイルがの判定結果をavailableに設定する
    - そうでなければ available=trueを設定する
    - availableを返却する
    
    -  Tuple3<String, String, String> createTask(final String path, final Map<String, Object> docMap) 
    		- サムネルのタスクを生成する
    		- Fessコンフィグを取得する
    		- FessコンフィグのインデックスフィールドIDをキーにドキュメントマップの値を取得し、thumbnailIdに設定する
    		- 名前,サムネイルID,パスをもとにタスクを生成する
    		- デバッグログ有効時
    			- サムネイルタスク生成のログメッセとtaskを出力する
    		- taskを返却する
    	- setDirectoryNameLength
    		- ディレクトリ名の長さのセッター
    	- expandPath
    		ファイルパスマップから値を取得して返却する
    	
    	- boolean process(final String id, final BiPredicate<String, String> consumer)
    		- Predicateのtestメソッドの判定処理内容の実装箇所
    		- →BaseThumbnailGenerator抽象クラスを継承したクラスで実装する
    		- →process(final String id, final Predicate<ResponseData> consumer) 
    		- →process(final String id, final BiPredicate<String, String> consumer)
    		-
    		- FessElasticsearchクライアント,id,[インデックスフィールドサムネイル,インデックスフィールドコンフィグID]をもとにドキュメントを取得する
    		- ドキュメントが見つからない場合はログメッセとidをthrowする
    		- ドキュメントからurlを取得する
    		- urlが空の場合はサムネイルが無効のログメッセとurlをthrow
    		- インデックスフィールドコンフィグIDをキーにドキュメントからコンフィグIDを取得する
    		- コンフィグIDがnullか長さが2未満の場合は無効なコンフィグIDのログメッセとconfigIdをthrow
    		- BiPredicateクラスのtestメソッドでコンフィグIDとurlを判定した結果を返却する
    		- catch ThumbnailGenerationException
    			- Causeがnullの場合
    				- デバッグログを出力する
    			- そうでなければprocess失敗のログメッセとid, ExceptionのWARNログ
    		- catch Exception
    			- process失敗のWARNログを出力する
    		- falseを返却する
    		
    	- boolean process(final String id, final Predicate<ResponseData> consumer) 
    		- 上記processメソッドの判定結果を返却する
    			- クロールコンフィグヘルパーを取得する
    			- コンフィグIDをもとにコンフィグを取得する
    			- コンフィグがない場合はメッセージとconfigIdをthrow
    			- Info有効時
    				- サムネイル生成のログメッセとurlのinfoログを出力する
    			- クローラークライアントファクトリークラスのインスタンスを取得する
    			- urlをもとにクローラークライアントファクトリーからクローラークライアントインスタンスを取得する
    			- クライアントがnullならログメッセとconfigId,urlをthrow
    			- 最大リダイレクト数に達するまでループ処理
    				- try クライアントからurlでリクエストデータを生成し、クライアントからレスポンスデータを取得する
    					- レスポンスデータのリダイレクトロケーションが空でない場合
    						- uにリダイレクトロケーションを設定して処理を継続
    					-　空の場合
    						- サムネイル内容のprocess失敗のログメッセとurlとレスポンスURLが空のログメッセをthrow
    					- Predicateでレスポンスデータの判定結果を返却する
    				- catch CrawlingAccessException
    					- デバッグログ有効時
    						- processのサムネイル内容が失敗のログメッセとurl,ThumbnailGenerationExceptionをthrow
    					- そうでない場合
    						-  ThumbnailGenerationExceptionのメッセージをthrow
    				- catch Exception
    					- processのサムネイル内容が失敗のログメッセとurl,ThumbnailGenerationExceptionをthrow
    			- processのサムネイル内容が失敗のログメッセとurlとリダイレクトループのログメッセをthrow
    	- GeneratorListのセッター
    	- Nameのゲッター
    	- Nameのセッター
    	- 最大リダイレクト数のセッター
      
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/thumbnail/impl/WebDriverGenerator.java
    - HTMLのサムネイル生成用クラス
    - init
      - phantomjsが存在し、サムネイル出力対象か判定
      - （調べる）DesiredCapabilities
      - サムネイルのサイズを設定
    - destroy
    - generate
      - 既にファイルが存在する場合はスクリーンショットを取らない
      - スクリーンショットがなければ撮影してconvertメソッドで出力する
    - isAvalable
      - webDriverがnullでなければtrue
    - convert
      - thumbnailWidth, thumbnailHeightでサイズを変更する
    - loadImage
      - ImageIOで読み込む
    - createDriverService
      - コマンドラインでphantomjsが利用可能か判定する
      - phantomjs起動
    - findCLIArgumentsFromCaps
      - webDriverの利用に必要だが詳細は調べる(調)
      
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/thumbnail/impl/CommandGenerator.java
  - コマンドを生成するクラス
    - 初期化
      - ベースディレクトリを取得
      - プロセス削除までのタイマーを設定
    - コマンド生成
      - デバッグ出力時はパスを表示
      - 出力ファイルパス、ファイルの存在確認
      - コマンドのリストに含まれる"url"と"outputFile"を置換
      - プロセスビルダーにコマンドリストを入れてタスクを開始
      - コマンドリストをストリング分割してnullチェック
      - タスクのnullチェック
      - 出力ファイルが空か長さ0なら消去
      - チェックに問題なければtrue返却
    - プロセスを消すクラス(タイムアウト)

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/thumbnail/impl/EmptyGenerator.java
  - generateメソッドはfalseを返却する
  - destroyメソッドを定義する
  
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/thumbnail/impl/HtmlTagBasedGenerator.java
  - destroyメソッドを定義する
  - Tuple3<String, String, String> createTask(final String path, final Map<String, Object> docMap)
  	- タスクを生成して返却する
  	- ThumbnailGeneratorインターフェースのcreateTaskメソッドをオーバーライド
  	- Fessコンフィグを取得する
  	- ドキュメントマップ、FessコンフィグのインデックスフィールドIDからサムネイルIDを取得する
  	- Name、サムネイルID、引数のパスからタスクインスタンスを生成する
  	- デバッグログ有効時
  		- 生成したタスクのログメッセとtaskをデバッグログ出力する
  	- return task
  
  - boolean generate(final String thumbnailId, final File outputFile)
  	- サムネイルの生成を行うメソッド
  	- outputFileの存在確認、ディレクトリ判定をする
  	- processメソッドの結果を返す
  		- responsDataのlambdaはPredictionのtestメソッドの
  		- isMimeTypeメソッドの結果がfalseの場合
  			- デバッグログ有効時はサムネイルイメージがないログメッセージとthumbnailId,responseDataのURLを出力する
  			- インデックスのサムネイルフィールドに空を設定する
  			- return false
  		- createImageInputStreamメソッドでresponseDataのレスポンスボディからInputStreamを取得する
  		- saveImageメソッドでInputStreamをoutputFileにイメージを保存する
  		- saveImageメソッドの結果によって処理を分岐する
  			- OK:created true
  			- FAILED:サムネイルの生成に失敗した
  			- INVALID_SIZE:サムネイルサイズが無効
  			- default:未知のサムネイル結果
  		- catch Throwable
  		- サムネイル生成に失敗した
  		- finally
  			- createdがfalseならインデックスのサムネイルフィールドに空を設定する
  		- return outputFileの存在確認結果を返却する
  		
  	- boolean isImageMimeType(final ResponseData responseData)
  		- MimeTypeの判定結果をbooleanで返すメソッド
  		- true: null, "image/png","image/gif","image/jpeg","image/bmp"
  		
  	- Result saveImage(final ImageInputStream input, final File outputFile) throws IOException
  		- サムネイルイメージを保存するメソッドでResultを返却する
  		- Fessコンフィグを取得する
  		- inputからImageReaderイテレータを取得する
  		- イテレータ分ループ
  			 - 処理中のImageReaderにinputを設定する
  			 - readerのデフォルト読み込みパラメーラを取得する
  			 - 幅、高さを取得する
  			 - パラメータ：サンプリング用の幅、高さを設定する
  			 - readerのイメージを読み込み、デフォルトのInputParamからBufferedImageを取得する
  			 - サムネイルの幅、高さを設定する
  			 - 設定したサムネイルの幅、高さ、typeからBufferedImageのインスタンスを生成する
  			 - outputFileにサムネイルを描画イメージを書き込む
  			 - return Result.OKを返す
  		- readerを破棄する
  		- return Result.Failedを返す
  		
  	- enum Result
  		- OK, FAILED, INVALID_SIZE

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/thumbnail/ThumbnailGenerator.java
  - サムネイル生成用のインターフェイス
    - Options
      -s セッションID -n 名前 -p プロパティパス -t スレッド番号 -c 掃除 
    - isTarget
    - isTarget(ドキュメントのMap)
    - isAvailable
    - destroy
* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/thumbnail/ThumbnailManager.java
  - サムネイル生成管理クラス
  - PostContstructアノテーション（初期処理用メソッドのアノテーション）
  - init()
    プロパティファイルからサムネイルパスを設定する
    パスがnullでない場合
    		パスにファイルを生成する
    パスがnullの場合
    		プロパティからFESSのvarパスを取得する
    		varパスがnullでなければvarパスとサムネイルディレクトリ名からファイルを生成する
    		nullの場合はResourceUtilのgetThumbnailPathメソッドから取得したパスにtoFileメソッドでファイルを生成する
    		FileクラスのbaseDir変数へ格納する
    	baseDirのmkdirsメソッドが成功した場合
    		loggerに絶対パスのメッセージを出力する
    	baseDirがディレクトリでない場合
    		絶対パスが見つからないメッセージとともにFessSystemExceptionをthrowする
    	debugログ有効時
    		サムネイルディレクトリの絶対パスを出力する
    	
    	BlockingQueue Stringタプル3つのthumbnailTaskQueueにLinkedBlockingQueueクラスをキューサイズ10000を渡してインスタンス化する
    Booleanのgeneratingに大小無視した"true"とサムネイルプロセスのプロパティ値を比較結果の逆を設定する
    ThreadのthumbnailQueueThreadにThreadをインスタンス化する（第一引数：ターゲット） Runnableのラムダ->
    		List Stringのタプル3のtaskListにArrayリストをインスタンス化する
    		generatingの間ループを繰り返す
	    		try
	    			Stringのタプル３のtaskにArrayListをインスタンス化する
	    			taskがnullの場合
	    				taskListが空でない場合
	    					taskListを引数にstoreQueueメソッドを実行する
	    				taskListにtaskが含まれる場合
	    					taskListにtaskを追加する
	    					taskListサイズがthumbnailTaskのバルクサイズより大きい場合
	    						taskListを引数にstoreQueueメソッドを実行する
	    		catch InterruptedException
	    			デバッグログ有効時
	    			タスクが中断しバッグログメッセージとExceptionをログ出力する
	    		catch Exception
	    			generatingがtrueの場合
	    			サムネイル生成に失敗したログメッセとExceptionをWARNログを出力する
	    	taskListがからでない場合
	    		taskListを引数にstoreQueueメソッドを実行する
	    	, （第二引数：名前）"ThumbnailGenerator"
	thumbnailQueueThreadのstartメソッドを実行する
	init処理終了
	
  - PreDestroyアノテーション（コンテナ終了時の処理のアノテーション）
  - destroy()
  	generatingをfalseに設定する
  	thumbnailQueueThreadのinterruptメソッド(割り込み)でスレッドを中断する
  	try
  		10000msを引数にthumbnailQueueThreadのjoinメソッドを実行する
  		スレッドが生きている間10000ミリ秒waitして停止したmsが0以下になるまで実行する
  		例外時はInturruptedExceptionをthrowする
    	catch InterruptedException
    		サムネイルタイムアウトのログメッセとExceptionのWARNログを出力する
    	generatorListのforEach
    		try
    			リストの要素につきdestroyメソッドを実行する
    			implクラスのgenaratorでオーバーライドされたdestroyメソッドを実行する
    		catch Exception
    			サムネイル生成停止失敗のログメッセとExceptionのWARNログを出力する
    			
  - getThumbnailPathOption()
  	"-D [Fessサムネイルパス]=[絶対パス]"の文字列を返すメソッド
  	コマンドリストに入れる際に呼び出されるメソッド
  	
  - storeQueue(taskList Stringのタプル3)
    	Fessのコンフィグクラスを取得する
    	システムヘルパークラスを取得する
    	サムネイル生成のターゲット配列を取得する
    	サムネイルキューのリストを生成する
    	taskListのentityがnullでない場合、taskList forEach　task ->
    		targetsの数分ループ
    			サムネイルキューを生成する entity
    			以下、サムネイルキューへの設定をする
    				ジェネレーターを設定する
    					設定値：taskのvalue1
    				サムネイルIdを設定する
    					設定値：taskのvalue2
    				パスを設定する
    					設定値：taskのvalue3
    				ターゲットを設定する
    					設定値：target
    				作成者を設定する
    					設定値：定数のシステムユーザーの値
    				作成日時を設定する
    					設定値：システムヘルパークラスの現在日時(long)
    				listにentityを追加する
    	taskListのループを抜ける
    	taskListをクリアする
    	デバッグログ有効時
    		リストサイズ(サムネイルタスク数)をデバッグログを出力する
    	ThumbnailQueueBhvを取得する（コンポーネントユーティリティクラスにインスタンス化されたThumbnailQueueBhvクラス）
    	ThumbnailQueueBhvのバッチ登録メソッドでlistを登録する
    	
  - generate(final ForkJoinPool pool, final boolean cleanup)
  	Fessコンフィグを取得する
  	idListを生成する
  	thumbnailQueueBhvを取得する
  	thumbnailQueueBhvの検索結果を渡してpoolに登録する
  	検索内容
  		Fessコンフィグのデフォルトジョブターゲットが空の場合はコンフィグのデフォルトジョブターゲットをターゲットに設定して検索する
  		上記以外はデフォルトジョブターゲットとFessコンフィグのスケジューラーターゲット名でターゲットを検索する
  		作成日時の降順でソートする
  		entityからIdを取得してidListに追加する
  		もしcleanupがtrueかつデバッグログ有効時
  			サムネイルキューが削除されたメッセとentityのデバッグログを出力する
  		cleanupがfalseの場合
  			fessConfigとentityを引数にprocessメソッドを実行する
  	idListが空でない場合
  		thumbnailQueueBhvで検索して削除をする
  			検索内容
  				idListのIdでthumbnailQueueのIdを検索
  		thumbnailQueueBhvをリフレッシュする
  	return idListサイズ
  		
  - generate(final FessConfig fessConfig, ThumbnailQueue entity)
  	デバッグログ有効時
  	サムネイル処理中のメッセとサムネイルキュークラスのtoStringメソッドのログ出力
  	try
  		アウトプットファイルをインスタンス化する
  		ノーイメージファイルをインスタンス化する
  		ノーイメージファイルがファイルでなく、削除期限超過の場合
  			ノーイメージファイルがファイルで削除できなかった場合
  				削除失敗のWARNログ出力
  			サムネイルジェネレーターインスタンスを取得する
  			ジェネレーター有効時
  				サムネイルキューのサムネイルIDと出力先ファイルパスからサムネイルを生成する
  				生成できなかった場合
  					ノーイメージファイルとファイルの最終更新日を設定する  	
    				生成できた場合
    					インターバルを設定する
    					0でなければスレッドをインターバルの分だけスリープする
    			有効でない場合はWARNログを出力する
    		それ以外の場合はデバッグログ有効時
    			ノーイメージファイルが存在しないデバッグログを出力する
    	catch Exception
    		サムネイル生成失敗のWARNログを出力する
    	
  - offer(final Map<String, Object> docMap) 
  	ジェネレーターリストの数分繰り返し
  		ドキュメントマップがターゲットの場合
  			ドキュメントマップからイメージファイル名を取得し、パスに設定する
  			ドキュメントマップとパスからタスクを生成する
  			タスクがnullでない場合
  				デバッグログが有効な場合は追加サムネイルタスクのログを出力する
  				生成したタスクでサムネイルタスクキューofferメソッド(リストの最後に追加)が失敗した場合
  					サムネイルタスク追加失敗のログメッセとtaskの内容をログ出力する
  				return true
  			return false
  	デバッグログ有効時
  		サムネイルジェネレーターが見つからなかったログメッセと
  		docmapがnullでなければdocmapのurl
  		docmapがnullならdocmapの内容を出力する
  	return false
  - String getImageFilename(final Map<String, Object> docMap)
  	サイズ50のStringビルダー生成
  	Fessコンフィグ取得
  	docmapからFessコンフィグのインデックスフィールドdocidを生成する
  	docidの長さ分ループ
  		ループ1回以上でiがsplitサイズで割り切れる場合
  		Stringビルダーに/を追加する
  	Stringビルダーに.pngを追加する
  	return Stringビルダー文字列
  - File getThumbnailFile(final Map<String, Object> docMap) 
  	docmapからイメージファイル名を取得する(thumbnailパス)
  	サムネイルパスがブランクでなければbaseDirとthumbnailパスからファイルを生成する
  	fileがFileクラスなら
  		return file
  	return null
  - add(final ThumbnailGenerator generator)
  	デバッグログ有効時
  		ジェネレーター名が利用可能のメッセージ出力
  	ジェネレーターが利用可能な場合
  		ジェネレーターリストにgeneratorを追加する
  	
  - long purge(final long expiry)
  	baseDirが存在しない場合
  		return 0
  	try
  		ファイル一掃訪問者？FilePurgeVisitorクラスのvisitorを生成する
  		引数:baseDirのパス、拡張子、有効期限
  		return  visitor数
  	catch Exception
  		JobProcessingExceptionをスロー
  
  - class FilePurgeVisitor implements FileVisitor<Path>
  	有効期限
  	カウント
  	最大削除サイズ
  	削除ファイルリスト
  	baseパス
  	拡張子
  	FessEsクライアント
  	Fessコンフィグ
  	
  	- FilePurgeVisitor(final Path basePath, final String imageExtention, final long expiry)
  		引数の変数をクラスのローカル変数に設定する
  		baseパス、拡張子、有効期限
  		Fessコンフィグを取得する
  		Fessコンフィグのページサムネイル削除最大取得サイズを取得する
  		FessEsクライアントを取得する
  		
  	- deleteFiles
  		ハッシュマップdeletedFileMapを生成する
  		deletedFileListの数分ループ
  			docIdにパスから取得したdocIdを設定する
  			docIdがブランクかdeletedFileMapがdocIdを含む場合
  				パスのファイルを削除する
  			そうでない場合
  				dleteFileMapにdocIdとパスを設定する
  		deletedfileListをクリアする
  		
  		deleteFileMapが空でない場合
  			docIdFieldにFessコンフィグのインデックスフィールドDocIdを設定する
  			FessEsClientクラスのgetDocumentListメソッドを実行する
  				Fessコンフィグからインデックスドキュメント検索インデックスを取得、
  				Fessコンフィグからインデックスドキュメントタイプを取得、
  				searchRequestBuilder
  					searchRequestBuilderの検索条件を設定する
  						QueryBuildersタームクエリー(
  						docIdフィールド
  						,deleteFileMapキーセット[deleteFileMapサイズ]の配列
  					searchRequestBuilder取得ソースを設定する
  						docIdFiled, 空文字
  					forEach
  						docIdに取得結果のdocIdFieldを設定する
  							docIdがnullでない場合
  								deleteFileMapからdocIdを削除する
  								デバッグログ有効時
  									サムネイル保持のメッセとdocIdをログ出力する
  			deleteFileMapの値をforEach
  				マップ分deleteFileメソッドを実行する
  			カウント変数にdeleteFileMapサイズを加算する
  	- deleteFile(final Path path)
  		Filesクラスのdeleteメソッドでパスのファイルを削除する
  		デバッグログ有効時
  			削除のログメッセとパスをログ出力する
  		
  		parent　パスの親ディレクトリを取得する
  		while deleteEmptyDirectoryメソッドでparentを削除する
  			parentにparentの親ディレクトリを設定する
  			（親ディレクトリを再帰的に削除する)
  		catch IOException
  			削除失敗したパスとExceptio文字列のWARNログを出力する
  	
  	 - String getDocId(final Path file)
  	 	ファイルURI文字列を取得する
  	 	basePathのURI文字列を取得する
  	 	ファイルURI文字列の
  	 		basePath文字列を空白で置換する
  	 		.と拡張子を空白で置換する
  	 		/を空白で置換する
  	 	デバッグ有効時
  	 		baseパスURI文字列、ファイルURI文字列、docIdをログ出力する
  	 	return id
  	 	
  	 - getCount() 
  	 	deletedFileListが空でない場合
  	 		deleteFileメソッドを実行してファイルを削除する
  	 	return カウント変数
  	 	
  	 @Override
  	 - FileVisitResult preVisitDirectory(final Path dir, final BasicFileAttributes attrs) throws IOException
  			return FileVisitResultのCONTINUE
  	
  	@Override
  	 - 	FileVisitResult visitFile(final Path file, final BasicFileAttributes attrs) throws IOException
  	 	ファイルが有効期限日を超えていた場合
  	 		deletedFileリストにファイルを追加する
  	 		deletedFileリストサイズが最大削除サイズを超えていた場合
  	 			ファイルを削除する（deleteFilesメソッド）
  	 	return FileVisitResultのCONTINUE
  	@Override
  	 - FileVisitResult visitFileFailed(final Path file, final IOException e) throws IOException
  	 	参照ファイルが壊れていた場合の処理
  	 	exceptionがnullでない場合
  	 		I/O exceptionディレクトリのWARNログを出力する
  	 	return FileVisitResultのCONTINUE
  	 	
  	@Override
  	 - FileVisitResult postVisitDirectory(final Path dir, final IOException e) throws IOException
  	 	参照ファイルがディレクトリの処理
  	 	exceptionがnullでない場合
  	 		I/O exceptionディレクトリのWARNログを出力する
  	 	空ディレクトリを削除する
  	 	return FileVisitResultのCONTINUE
  		
  	- boolean deleteEmptyDirectory(final Path dir) throws IOException
  		ディレクトリがnullの場合
  			return false
  		PathクラスのdirをFileクラスに変換してdirectoryに設定する
  		directoryのリストがnullでなく、長さ0でサムネイルディレクトリ名とdirectoryの名前が同じでない場合
  		＝サムネイルディレクトリ名以下のディレクトリで空のディレクトリの場合
  			dirを削除する
  			デバッグログ有効時
  				Deleteディレクトリのデバッグログを出力する
  			return true
  		return false
  		
  	- void setThumbnailPathCacheSize(final int thumbnailPathCacheSize)
  		サムネイルパスキャッシュサイズのセッター
  	- void setImageExtention(final String imageExtention)
  		イメージ拡張子のセッター
  	- void setThumbnailTaskQueueSize(final int thumbnailTaskQueueSize)
  		サムネイルタスクキューサイズのセッター
  	- void setNoImageExpired(final long noImageExpired)
  		ノーイメージ期限のセッター 

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/timer/SystemMonitorTarget.java
	- StringBuilder append(final StringBuilder buf, final String key, final Supplier<Object> supplier)
		- "key":を設定する
		- supplierのgetメソッドからvalueを取得する
		- valueに応じてStringBuilderに追加する
		- StringBuilderを返す
		
	- void expired
		- StringBuilderをインスタンス化する
		- システムモニターを出力する
		- OS統計
			- ElasticsearchのモニターOsProbeクラスのインスタンスを取得する
			- メモリ：{物理:{未使用、合計}, スワップ領域:{未使用、合計}, CPU:{利用率}, (OsStatsインスタンスを取得する)負荷平均}
		- プロセス統計
			- ElasticsearchのモニターProcessProbeクラスのインスタンスを取得する
			- ファイルディスクリプタ:{open:{開いているファイル数}, max:{最大ファイルディスクリプタ数(同時オープン数)}, cpu:{cpu使用率, cpu合計時間}, 仮想メモリ:{仮想メモリサイズ}}
		- JVM統計
			- ElasticsearchのモニターJVMStatsクラスのインスタンスを取得する
			- lasticsearchのモニターJVMStats.Memクラスのインスタンスを取得する(java.lang.management.MemoryMXBean)
			- jvm:{memory:{heap:{used:使用中ヒープサイズ, committed:確定ヒープサイズ, max:最大ヒープサイズ, percent:ヒープ使用率}
			- , non_heap:{used:未使用ヒープサイズ, commi†ted:未確定ヒープサイズ}}
			- jvmStats.BufferPoolクラスのリストを取得する(List<java.lang.management.BufferPoolMXBean>)
			- bufferePoolリストのlambda
				- bufferPool名をJsonエスケープして出力する:{count:プール数, used:使用中プールサイズ, capacity:合計プール領域サイズ}
			- bufferePoolのStringBuilderを結合する
			-グローバルコレクターズ（GC）をJvmStatsから取得する
			- グローバルコレクターのlambda
			- gc名をJsonエスケープして出力する:{count:コレクション数, time:コレクションミリ秒}
			- GCのStringBuilderを結合する
			- jvmStatsからThreadsクラスを取得する
			- threads:{count:スレッド数, peak:ピークスレッド数,}
			- jvmStatsからClassesクラスを取得する
			- classes:{loaded:読み込みクラス数, total_loaded:合計読み込みクラス数, unloaded:未読み込みクラス数},
			- uptime:更新時間}
			
		- Elasticsearch統計
			- try
				- FessEsクライアントインスタンスを取得する
				- ノード統計レスポンスを設定する（以下取得する統計情報）
					- Elasticsearchクライアント.admin.cluster.準備ノード統計.ingestなし.回路ブレーカなし（OOM対策のメモリ制限）.ノード検出なし.ファイルシステム情報あり
					- .Httpなし.インデックスあり.JVMあり.OSあり.プロセスあり.プロセスあり.スクリプトなし.スレッドプールあり
					- .トランスポート(転送)あり.10000件を上限に取得を実行する
				- ElasitcsearchのJSONビルダーを取得する
				- ビルダーに開始オブジェクトを設定する
				- XContextにノード統計レスポンスを設定してJSONビルダーにレスポンスを設定する
				- ビルダーに終了オブジェクトを設定する
				- Stringのstatsにビルダーの文字列を設定する
			- catch Exception
				- Elasticsearch統計情報へのアクセスに失敗したログメッセを出力する
			- StringBuilderに"elasticsearch:[statsの情報を出力する],"
		- StringBuilderにタイムスタンプを出力する
		- StringBuilderに"}"を出力する
		-StringBuilderの文字列をinfoログに出力する

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/DataStore.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/DatabaseDataStoreImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/IndexUpdateCallbackImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/CsvListDataStoreImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/EsListDataStoreImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/FileListIndexUpdateCallbackImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/CsvDataStoreImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/AbstractDataStoreImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/impl/EsDataStoreImpl.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/DataStoreFactory.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ds/IndexUpdateCallback.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/FessBoot.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/creator/PagerCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/FessEnv.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/FessFwAssistantDirector.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessMailDeliveryDepartmentCreator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessTimeResourceProvider.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessUserLocaleProcessProvider.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessSecurityResourceProvider.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessListedClassificationProvider.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessCookieResourceProvider.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessCurtainFinallyHook.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessUserTimeZoneProcessProvider.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessMultipartRequestHandler.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessCurtainBeforeHook.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessActionAdjustmentProvider.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/sponsor/FessApiFailureHook.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/FessProp.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/direction/FessConfig.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/mail/TestmailPostcard.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/mail/CrawlerPostcard.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/mail/EsStatusPostcard.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/action/FessLabels.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/action/FessMessages.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/action/FessHtmlPath.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/mylasta/action/FessUserBean.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/WebApiResponse.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/es/EsApiManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/WebApiRequest.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/gsa/GsaApiManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/BaseApiManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/suggest/SuggestApiManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/WebApiManagerFactory.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/json/JsonApiManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/api/WebApiManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/GeoInfo.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/FessUser.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/FacetInfo.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/PingResponse.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/SearchRenderData.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/QueryContext.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/SearchRequestParams.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/entity/FacetQueryView.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/sso/SsoAuthenticator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/sso/spnego/SpnegoAuthenticator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/sso/SsoManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/sso/oic/OpenIdConnectAuthenticator.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ldap/LdapManager.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/ldap/LdapUser.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/impl/GroovyExecutor.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/AggregateLogJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/JobExecutor.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/PurgeLogJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/ScheduledJobException.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/SuggestJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/CrawlJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/job/PurgeDocJob.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/filter/EncodingFilter.java

* https://github.com/codelibs/fess/blob/master/src/main/java/org/codelibs/fess/filter/WebApiFilter.java
