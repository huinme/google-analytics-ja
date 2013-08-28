# Google Analytics SDK for iOS - Migrating to v3

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/migration](https://developers.google.com/analytics/devguides/collection/ios/v3/migration)

> Google Analytics を初めて利用する開発者には [バージョン3の利用開始](https://developers.google.com/analytics/devguides/collection/ios/v3/) があります.

このガイドでは、Google Analytics SDK for iOS をv3にアップグレードする方法について説明しています.

- [はじめに: V3の変更点](#at-a-glance-whats-new-in-v3)
- [はじめる前に](#before-you-begin)
- [アップグレードパス](#upgrade-paths)
    - [v1.x から v3 へ](v1x-to-v3)
    - [v2.x から v3 へ](v2x-to-v3)
- [リファレンス](#reference)
    - [V3でのマップを利用したデータ送信](#sending-data-using-maps-in-v3)
    - [V3でのトラッカーを利用したデータ設定](#setting-data-on-the-tracker-in-v3)
    
## <a name="at-a-glance-whats-new-in-v3"></a>はじめに: V3の変更点

v3のAPI群はネイティブとWebプラットフォームの間でより一貫性を保てるように更新されました.
全てのv2 API利用者は以下のことに注意する必要があります.

- ヒットは`send:(NSDictionary *)params`メソッドのみを利用して送信されます.
- デバッグモードは `Logger` に置き換えられました.
- New: ディスパッチされたデータがレポートで表示されないようにする`dryRun`フラグが追加されました
- New: 各地域の通貨単位のサポートがiOSへ追加されました.

変更点の完全な一覧は、[Changelog](https://developers.google.com/analytics/devguides/collection/android/changelog) を参照してください.

## <a name="before-you-begin"></a> はじめる前に

v3へのアップグレードを始める前に、以下のことを行う必要があります.

- [プロパティおよびアプリプロファイルで、ユニバーサルアナリティクスを有効にする.](https://support.google.com/analytics/answer/2790010?hl=en)
- [Google Analytics SDK for iOS v3を利用する.](https://developers.google.com/analytics/devguides/collection/android/resources)

## <a name="upgrade-paths"></a> アップグレードパス

アップグレードを開始するには、現在の実装状態からのアップグレードパスを選択してください.

- [v1.x から v3へ](v1x-to-v3)
- [v2.x から v3へ](v2x-to-v3)

### <a name="v1x-to-v3"></a> v1.x から v3 へ

Google Analytics iOS SDK v1.x の利用者は、v3 の利用を始めるために[バージョン3の利用開始](https://developers.google.com/analytics/devguides/collection/ios/v3/) に従うことをおすすめします.

### <a name="v2x-to-v3"></a> v2.x から v3 へ

v2.x の利用者は以下のステップにしたがってv3へアプグレードしてください.

1. すべての`send<hit-type>`メソッドを新しい`send:`メソッドに置き換える.

```
// v2 (Old)
id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];
[tracker sendView:@"HomeScreen"];
```

```
// v3
id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];
[tracker set:kGAIScreenName value:@"Home Screen"];

[tracker send:[[GAIDictionaryBuilder createAppView]  build]];
```

2. [自動スクリーン計測 ](https://developers.google.com/analytics/devguides/collection/ios/v3/screens#automatic)の利用者は、`GAITrackedViewController.trackedViewName`を`GAITrackedViewController.screenName`へ置き換える.

```
// v2 (Old)
#import "GAITrackedViewController.h"

@implementation AboutViewController

- (void)viewDidAppear:(BOOL)animated {
  [super viewDidAppear:animated];
  self.trackedViewName = @"About Screen";
}

// ... Rest of ViewController implementation.
```

```
// v3
#import "GAITrackedViewController.h"

@implementation AboutViewController

- (void)viewDidAppear:(BOOL)animated {
  [super viewDidAppear:animated];
  self.screenName = @"About Screen";
}

// ... Rest of ViewController implementation.
```

3. デバッグモードは廃止になりました. 代わりに`Logger`を使用して下さい.

```
// v2 (Old)
[GAI sharedInstance].debug = YES;
```

```
// v3
[[GAI sharedInstance].logger setLogLevel:kGAILogLevelVerbose];
```

4. `GAI.useHttp`プロパティは削除されました. デフォルトのHTTPSの代わりにHTTPを利用してヒットの送信を行いたい場合は、`kGAIUseSecure` パラメーターを各`GAITracker`に設定してください.

```
// v2 (Old)
// AppDelegate.m

#import AppDelegate.h
#import GAI.h

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    static NSString const *kGaPropertyId = @"UA-XXXX-Y";
    id tracker = [[GAI sharedInstance] trackerWithTrackingId:kGaPropertyId];

    // Send hits using HTTP (default=HTTPS).
    tracker.useHttps = NO;

}
```

```
// v3
// AppDelegate.m

#import AppDelegate.h
#import GAI.h

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    static NSString const *kGaPropertyId = @"UA-XXXX-Y";
    id tracker = [[GAI sharedInstance] trackerWithTrackingId:kGaPropertyId];

    // Send hits using HTTP (default=HTTPS).
    [tracker set:kGAIUseSecure value:[@NO stringValue]];

}
```

5. v3 SDK はアプリの起動時に自動で新しいセッションを開始しなくなりました.もしv2からの振る舞いを維持したい場合は、ユーザーがアプリの利用を開始した時に、自身でセッション制御のロジックを実装する必要があります.

```
// AppDelegate.m

#import AppDelegate.h
#import GAI.h

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    static NSString const *kGaPropertyId = @"UA-XXXX-Y";
    id tracker = [[GAI sharedInstance] trackerWithTrackingId:kGaPropertyId];

    // CAUTION: Setting session control directly on the tracker persists the
    // value across all subsequent hits, until it is manually set to null.
    // This should never be done in normal operation.
    //
    // [tracker set:kGAISessionControl value:@"start"];

    // Instead, send a single hit with session control to start the new session.
    [tracker send:[[GAIDictionaryBuilder createEventWithCategory:@"UX"
                                                          action:@"appstart"
                                                            label:nil
                                                            value:nil] setValue:@"start" forKey:kGAISessionControl] build];

}
```

6. アプリレベルのオプトアウト設定はSDKでは保存されなくなったため、**アプリが起動される度に設定する必要があります**(デフォルトは`NO`です).[アプリケーションレベルのオプトアウトについてはこちら](https://developers.google.com/analytics/devguides/collection/ios/v3/advanced#opt-out)

## <a name="reference"></a> リファレンス

以降のセクションでは、v3 SDKを利用してのデータの設定および送信方法について例を示します.

### <a name="sending-data-using-maps-in-v3"></a> V3でのディクショナリーを利用したデータ送信

v3では、データは`send:`メソッドのみを利用して送信されます.このメソッドはGoogle Analyticsのフィールドと値をもった`NSDictionary`を引数として受け取ります. `GAIDictionaryBuilder`ユーティリティクラスはヒットを構築するプロセスを簡単にしてくれます.

```
id<GAItracker> tracker = [[GAI sharedInstance] defaultTracker];
[tracker set:kGAIScreenName value:@"Home Screen"];

[tracker send:[[GAIDictionaryBuilder createAppView] setValue:@"Premium"  // Creates a Map of hit type 'AppView' (screenview) and set any additional fields.
                                                    forKey:[customDimensionForIndex:1] build]; // Build and return the dictionary to the send method.
```

`GAIDictionaryBuilder`クラスは、例えばイベントなどサポートされる全てのヒットタイプを構築するのに利用されます.

```
id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];

[tracker send:[[GAIDictionaryBuilder createEventWithCategory:@"ui_action",
                                                  withAction:@"button_press",
                                                   withLabel:@"play_button",
                                                   withValue:nil] build]];
```

[v3でのデータ送信についてはこちら](https://developers.google.com/analytics/devguides/collection/ios/v3/advanced#set-send)

### <a name="setting-data-on-the-tracker-in-v3"></a> V3でのトラッカーを利用したデータ設定

`set:value:forKey`メソッドを利用して、`GAITracker`に対して直接値を設定することもできます.
値の直接設定は、その後の全ての`GAITracker:`のヒットに対して適用されます.

```
// Values set directly on a tracker apply to all subsequent hits.
[tracker set:kGAIScreenName value:@"Home Screen"];

// This screenview hit will include the screen name "Home Screen".
[tracker send:[GAIDictionaryBuilder createAppView]];

// And so will this event hit.
[tracker send:[[GAIDictionaryBuilder createEventWithCategory:@"ui_action",
                                                  withAction:@"button_press",
                                                   withLabel:@"play_button",
                                                   withValue:nil] build]];
```

すでに`GAITracker`に設定された値をクリアするには、
プロパティにたいして`nil`をセットしてください.

```
// Clear the previously-set screen name value.
[tracker set:kGAIScreenName
       value:nil];

// Now this event hit will not include a screen name value.
[tracker send:[[GAIDictionaryBuilder createEventWithCategory:@"ui_action",
                                                  withAction:@"button_press",
                                                   withLabel:@"play_button",
                                                   withValue:nil] build]];
```

[v3でのデータ設定についてはこちら](https://developers.google.com/analytics/devguides/collection/ios/v3/advanced#set-send)

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月19日.

