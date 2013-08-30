# Advanced Configuration - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/advanced](https://developers.google.com/analytics/devguides/collection/ios/v3/advanced)

このドキュメントでは、Google Analytics SDK for iOS v3のいくつかの発展的な機能についての概要を説明しています.

- [概要](#overview)
- [初期化](#initialization)
- [データのセットと送信](#setting-and-sending-data)
    - [複数のトラッカーの利用](#using-multiple-trackers)
    - [デフォルトトラッカー](#default-tracker)
- [サンプリング](#sampling)
- [アプリケーションレベルオプトアウト](#app-level-opt-out)
- [テストおよびデバッグ](#testing-and-debugging)
- [コードサンプル](#complete-example)

## <a name="overview"></a>概要

Google Analytics SDK for iOSはGoogle Analyticsに対しデータの設定および送信を行うための`GAITracker`クラスと、
実装上のグローバルな設定のインターフェイスである`GAI`シングルトンクラスを提供しています.

## <a name="initialization"></a>初期化

データ計測を行う前に、トラッカーの名前とGoogle Analytics プロパティIDを利用して、
`GoogleAnalytics`シングルトンを通じて最低でも一つのトラッカーを初期化しなければなりません.

```
// Initialize a tracker using a Google Analytics property ID.
[[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-Y"];
```

これでGoogle Analyticsにデータを設定、送信するためのトラッカーが利用可能になりました.

## <a name="setting-and-sending-data"></a>データのセットと送信

データは、`set`および`send`メソッドを用いて、
パラメータと値のペアで構成されたマップをトラッカーにセットし送信することで、
Google Analyticsへと送られます.

```
/*
 * Send a screen view to Google Analytics by setting a map of parameter
 * values on the tracker and calling send.
 */

// Retrieve tracker with placeholder property ID.
id<GAITracker> tracker = [[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-Y"];

NSDictionary *params = [NSDictionary dictionaryWithObjectsAndKeys:
                            @"appview", kGAIHitType, @"Home Screen", kGAIScreenName, nil];
[tracker send:params];
```

`MapBuilder`クラスはこの構築プロセスを簡単にするので、ほとんどの利用ケースにおいてお勧めです.
この少ないコードで、スクリーンビューを送信することができます.

```
// Sending the same screen view hit using [GAIDictionaryBuilder createAppView]
[tracker send:[[GAIDictionaryBuilder createAppView] set:@"Home Screen"
                                                 forKey:kGAIScreenName]];
```

**Appying Values to Multiple Hits**

この例のように、
トラッカーに直接セットされた値はすべて永続化され複数のヒットに適用されます.

```
// May return nil if a tracker has not yet been initialized with
// a property ID.
id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];

// Set screen name on the tracker to be sent with all hits.
[tracker set:kGAIScreenName
       value:@"Home Screen"];

// Send a screen view for "Home Screen".
[tracker send:[GAIDictionaryBuilder createAppView]];

// This event will also be sent with &cd=Home%20Screen.
[tracker send:[[GAIDictionaryBuilder createEventWithCategory:@"UX"
                                                      action:@"touch"
                                                       label:@"menuButton"
                                                       value:nil] build]];

// Clear the screen name field when we're done.
[tracker set:kGAIScreenName
       value:nil];
```

複数のヒットにまたがって適用したい値のみトラッカーに直接設定してください.
後続するスクリーンビューとイベントのヒットに対して値が適用されるので、
スクリーン名をトラッカーに設定するのは良い方法だと言えます.
しかしながら、各ヒット毎に値が変化するため、
ヒットタイプのようなフィールドをトラッカーに設定するのは推奨されません

### <a name="using-multiple-trackers"></a>複数のトラッカーの利用

ひとつの実装(アプリ)内で複数のトラッカーを利用することができます.
これは複数のプロパティに対しデータを送信したい場合に便利です.

```
d<GAITracker> tracker1 = [[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-1"];

// Trackers may be named. By default, name is set to the property ID.
id<GAITracker> tracker2 = [[GAI sharedInstance] trackerWithName:@"altTracker"
                                                     trackingId:@"UA-XXXX-2"];

[tracker1 set:kGAIScreenName
        value:@"Home Screen"];

[tracker2 set:kGAIScreenName
        value:NSStringFromClass([self class])];

// Send a screenview to UA-XXXX-1.
[tracker1 send:[[GAIDictionaryBuilder createAppView] build]];

// Send a screenview to UA-XXXX-2.
[tracker2 send:[[GAIDictionaryBuilder createAppView] build]];
```

スクリーン名や例外計測のような自動計測機能は、
Google Analyticsにデータを送信するためにひとつのトラッカーのみ利用すべきです.
もしこれらの機能を複数のトラッカーを利用して送信したい場合には、手動で行う必要があります.

参考までに、自動スクリーン計測は`GAITrackedViewController`が持つ
`tracker`プロパティで指定されたトラッカーを利用しています.
例外計測では、`GAI`インスタンスのもつデフォルトトラッカーを利用しています.

### <a name="default-tracker"></a>デフォルトトラッカー

Google Analyticsは、デフォルトトラッカーを保持しています.
初めて初期化されたトラッカーがデフォルトトラッカーとなりますが、上書きすることも可能です.

```
// t1 becomes the default tracker because it is the first tracker initialized.
id<GAITracker> t1 = [[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-1"];

id<GAITracker> t2 = [[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-2"];

// Returns t1.
id<GAITracker> defaultTracker = [[GAI sharedInstance] defaultTracker];

// Hit sent to UA-XXXX-1.
[defaultTracker send:[[[GAIDictionaryBuilder createAppView]
                set:@"Home Screen" forKey:kGAIScreenName] build]];

// Override the default tracker.
[[GAI sharedInstance] setDefaultTracker:t2];

// Returns t2.
defaultTracker = [[GAI sharedInstance] defaultTracker];

// Hit sent to UA-XXXX-2.
[defaultTracker send:[[[GAIDictionaryBuilder createAppView]
                       set:NSStringFromClass([self class])
                    forKey:kGAIScreenName] build]];
```

## <a name="sampling"></a>サンプリング

Google Analyticsへ送信するデータを制限するために、
クライアントサイドでのサンプリングを行うことができます.
もしアプリが大量のユーザーを抱えている、あるいは大量のデータをGoogle Analyticsに送信する場合には、
サンプリングを有効にすることでレポートのじゃまにならないようにできます.

例えば、クライアント側でのサンプリングレートを50%に設定するには、以下の様なコードを書きます.

```

// Assumes a tracker has already been initialized with a property ID, otherwise
// getDefaultTracker returns nil.
id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];

// Set a sample rate of 50%.
[tracker set:kGAISampleRate value:@"50.0"];
```

> **レポートの不一致を回避するために**、各アプリのビュー(プロファイル)は同じサンプリングレートで収集されたデータを持つ必要があります.
> もしアプリの異なるバージョンで異なるサンプリングレートを使用するのであれば、
> 各バージョンからのデータを分離する[ビュー(プロファイル)フィルター](http://support.google.com/analytics/bin/answer.py?hl=en&answer=1033162)
> の設定を行う必要があります.


## <a name="app-level-opt-out"></a>アプリケーションレベルオプトアウト

アプリ全体を通してGoogle Analyticsを無効にするアプリケーションレベルオプトアウトのフラグを仕様することができます.
アプリが起動するたびに毎回フラグの設定を行う必要があることと、デフォルトでは`false`になっていることに注意して下さい.

アプリケーションレベルオプトアウトの設定を取得するには以下のようにします.

```
// Get the app-level opt out preference.
if ([GAI sharedInstance].optOut) {
  ... // Alert the user they have opted out.
}
```

アプリケーションレベルオプトアウトを設定するには以下のようにします.

```
// Set the app-level opt out preference.
[[GAI sharedInstance] setOptOut:YES];
```

## <a name="testing-and-debugging"></a>テストおよびデバッグ

Google Analytics SDK for iOSはテストおよびデバッグを簡単にするツールを提供しています.

### Dry Run

SDKは、それがセットされた場合にGoogle Analyticsへのデータ送信を防止する`dryRun`フラグを提供しています.
`dryRun`フラグはテストあるいはデバッグを行う際には設定されなければならず、そうすることでテストデータはGoogle Analyticsのレポートに表示されないようになります.

dry run フラグを設定するには以下のようにします.

```
[[GAI sharedInstance] setDryRun:YES];
```

### Logger

`GAILogger`プロトコルは`error, warning, info`および`verbose`といった各詳細レベルで、
SDKからのメッセージを便利に処理するために提供されています.

SDKは、ワーニングあるいは警告ログのメッセージをコンソールに表示する、標準の`Logger`実装で初期化されます.
`Logger`の詳細度を設定するには以下のようにします.

```
// Set the log level to verbose.
[[GAI sharedInstance].logger setLogLevel:kGAILogLevelVerbose];
```

`Logger`のカスタム実装を利用することも可能です.

```
// Provide a custom logger.
[[GAI sharedInstance].logger = [[CustomLogger alloc] init];
```

## <a name="complete-example"></a>コードサンプル

以下の例は、Google Analyticsの初期化および、ひとつのスクリーンビューを送信するために必要なコードを載せています.

その後、最初の画面がユーザーに表示された際にスクリーンが計測されます.

典型的には、以下の例のように、実装の初期化はアプリデリゲートで行われます.

```
//
//  AppDelegate.h
//

#import <UIKit/UIKit.h>
#import "GAI.h"

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;
@property (strong, nonatomic) id<GAITracker> tracker;

@end


//
//  AppDelegate.m
//

#import "AppDelegate.h"


/** Google Analytics configuration constants **/
static NSString *const kGaPropertyId = @"UA-XXXX-Y"; // Placeholder property ID.
static NSString *const kTrackingPreferenceKey = @"allowTracking";
static BOOL const kGaDryRun = NO;
static int const kGaDispatchPeriod = 30;

@interface AppDelegate ()

- (void)initializeGoogleAnalytics;

@end

@implementation AppDelegate
- (void)applicationDidBecomeActive:(UIApplication *)application {
    [GAI sharedInstance].optOut =
    ![[NSUserDefaults standardUserDefaults] boolForKey:kTrackingPreferenceKey];
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Do other work for customization after application launch
    // then initialize Google Analytics.
    [self initializeGoogleAnalytics];

    return YES;
}

- (void)initializeGoogleAnalytics: {

    [[GAI sharedInstance] setDispatchInterval:kGaDispatchPeriod];
    [[GAI sharedInstance] setDryRun:kDryRun];
    self.tracker = [[GAI sharedInstance] trackerWithTrackingId:kGaPropertyId];
}

   // The rest of the app delegate code omitted.

@end
```

次に、最初の画面がユーザーに表示された場合には、

```
//
// MyViewController.m
//
#import "MyViewController.h"
#import "AppDelegate.h"
#import "GAI.h"
#import "GAIFields.h"
#import "GAITracker.h"
#import "GAIDictionaryBuilder.h"

@implementation MyViewController

- (void)viewDidLoad {

    [super viewDidLoad];

    // This screen name value will remain set on the tracker and sent with
    // hits until it is set to a new value or to nil.
    [[GAI sharedInstance].defaultTracker set:kGAIScreenName
                                       value:@"Home Screen"];

    // Send the screen view.
    [[GAI sharedInstance].defaultTracker
        send:[[GAIDictionaryBuilder createAppView] build]];
}
```



- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.

