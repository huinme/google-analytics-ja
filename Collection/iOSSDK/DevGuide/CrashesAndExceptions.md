# Crashes & Exceptions - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/exceptions](https://developers.google.com/analytics/devguides/collection/ios/v3/exceptions)

- - -


このドキュメントではGoogle Analytics SDK for iOS v3を利用したクラッシュおよび例外の測定について高レベルの概要を提供しています.

- [概要](#overview)
- [キャッチ可能な例外](#caught-exceptions)
- [キャッチ不可能な例外(クラッシュ)](#uncaught-exceptions)

## <a name="overview"></a>概要

クラッシュおよび例外の計測によって、
アプリで発生したクラッシュおよび例外の回数およびタイプについて計測することが可能になります.
例外は以下のフィールドを持っています.

Field Name | Tracker Field | Type | Required | Description
--- | --- | --- | --- | ---
Description | kGAIExDescription | NSString | No | 例外についての説明(100文字以内). `nil`も可
isFatal | kGAExFatal | BOOL | Yes | 発生した例外が致命的かを示します. `YES`の時致命的となります.

## <a name="caught-exceptions"></a>キャッチ可能な例外

キャッチ可能な例外(Caught Exceptions)とは、
データのリクエスト中に度々発生するネットワークのタイムアウトのように、
アプリの中で例外の処理を事前に定義してあるエラーを指します.

キャッチ可能な例外を例外フィールドに値を設定し、ヒットを送信することで計測するには、
以下の例のようにします.

```
/*
 * An app tries to load a list of high scores from the cloud. If the request
 * times out, an exception is sent to Google Analytics
 */
@try {

  // Request some scores from the network.
  NSArray *highScores = [self getHighScoresFromCloud];

}
@catch (NSException *exception) {

    // May return nil if a tracker has not already been initialized with a
    // property ID.
    id tracker = [[GAI sharedInstance] defaultTracker];

    [tracker send:[GAIDictionaryBuilder
        createExceptionWithDescription:@"Connection timout %d: %@", connectionError, errorDescription  // Exception description. May be truncated to 100 chars.
                             withFatal:NO] build];  // isFatal (required). NO indicates non-fatal exception.
}
```

## <a name="uncaught-exceptions"><a/>キャッチ不可能な例外

キャッチ不可能な例外(Uncaught Exceptions)とはアプリが実行中に遭遇した予期せぬ状況を表しており、
多くの場合致命的な状況でありクラッシュを引き起こします.
キャッチ不可能な例外は `trackUncaughtExceptions` プロパティを`YES`に設定することで
自動的にGoogle Analyticsに送信することが可能です.


```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [[GAI sharedInstance] setTrackUncaughtExceptions:YES;]
}
```

自動の例外計測を利用する場合には、以下のことに注意して下さい.

- 自動の例外計測を利用して送信される例外は、Google Analyticsではすべて致命的なものとしてレポートされます.
- descriptionフィールドは、例外タイプ、クラス、名前、メソッド名およびスレッド名を利用して自動でセットされます.

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.