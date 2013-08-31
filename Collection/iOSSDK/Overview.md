# Google Analytics SDK for iOS v3 (Beta) - Getting Started

Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/](https://developers.google.com/analytics/devguides/collection/ios/v3/)

このドキュメントではGoogle Analytics SDK for iOS v3の使い方について説明しています.

> **v1あるいはv2からの移行については [マイグレーションガイド](/Collections/Resources/v3-Migration-Guide.md) を参照してください.

- [はじめる前に](#before-you-begin)
- [利用方法](#getting-started)
    1. [ヘッダーとライブラリの追加](#adding-headers-and-libraries)
    2. [トラッカーの初期化](#initializing-the-tracker)
    3. [スクリーン計測の実装](#implementing-screen-measurement)
- [次のステップ](#next-steps)

## <a name="before-you-begin"></a>はじめる前に

SDKの実装を始める前に、以下のことを確認してください.

- [iOS Developer SDK 5.0](https://developer.apple.com/devcenter/ios/index.action) 以降を利用していること
- [Google Analytics for Mobile Apps iOS SDK v3](https://developers.google.com/analytics/devguides/collection/ios/resources) が必要であること.
- Google Analytics を利用して計測しようとしている.
- 新しい Google Analytics の[アプリプロパティおよびビュー(プロファイル)](http://support.google.com/analytics/bin/answer.py?hl=en&answer=2614741)

## <a name="getting-started"></a>利用方法

SDKの利用を開始するには以下の３ステップが必要になります.

1. [ヘッダーおよびライブラリをプロジェクトに追加する](#adding-headers-and-libraries)
2. [トラッカーの初期化](#initializing-the-tracker)
3. [スクリーン計測](#implementing-screen-measurement)

これらのステップを完了するとGoogle Analyticsを利用して以下のことが計測可能になります.

- アプリのインストール数
- アクティブユーザーおよびデモグラフィクス
- スクリーンおよびユーザーエンゲージメント
- クラッシュおよび例外

### <a name="adding-headers-and-libraries"></a>ヘッダーとライブラリの追加

Google Analytics for iOS SDK をダウンロードし、SDKパッケージから以下のファイルを追加してください.

- GAI.h
- GAITracker.h
- GAITrackedViewController.h
- GAIDictionaryBuilder.h
- GAIFields.h
- GAILogger.h
- libGoogleAnalyticsSservices.a

Google Analytics SDKはCoreDataおよびSystemConfigurationフレームワークを利用するため、
以下のライブライをアプリケーションのターゲットにリンクして下さい.

- libGoogleAnalyticsServices.a
- CoreData.framework
- SystemConfiguration.framework
- libz.dylib

> **アプリ内ですでにCoreDataフレームワークを利用している場合** 
> 例えば、Google AnalyticsのCoreDataオブジェクトからの`NSManagedObjectContextDidSaveNotification`通知に応答する場合、例外が発生する可能性があります.
> その代わりに、AppleはCoreDataの通知をmanaged object contextのパラメーターとしてリスナーに渡すことで、通知をフィルターする方法を推奨しています. [詳しくはAppleのドキュメントを参照してください.](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/NSManagedObjectContext.html)

### <a name="initializing-the-tracker"></a>トラッカーの初期化

トラッカーを初期化するには、`GAI.h`ヘッダーをアプリケーションデリゲートクラスの`.m`ファイルに追加し、次のコードをデリゲートの`application:didFinishedLaunchingWithOptions:`メソッド内に追加して下さい.

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  // Optional: automatically send uncaught exceptions to Google Analytics.
  [GAI sharedInstance].trackUncaughtExceptions = YES;

  // Optional: set Google Analytics dispatch interval to e.g. 20 seconds.
  [GAI sharedInstance].dispatchInterval = 20;

  // Optional: set Logger to VERBOSE for debug information.
  [[[GAI sharedInstance] logger] setLogLevel:kGAILogLevelVerbose];

  // Initialize tracker.
  id<GAITracker> tracker = [[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-Y"];

}
```

> **注意:** あるトラッキングIDのトラッカーを取得した時、トラッカーはライブライ内で永続化されます.
> その後、同じトラッキングIDで`trackerWithTrackingId:`を呼んだ場合には、同じトラッカーインスタンスが返ってきます. 
> また Google Analytics SDK は最初に生成されたトラッカーインスタンスをデフォルトトラッカーとして提供します. このインスタンスには次のようにしてアクセスできます.

```
id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];
```

上記の例にある"UA-XXXX-Y"は、あながたGoogle Analytics プロパティを作成した時に割り当てられる、トラッキングIDのプレースホルダーであることに注意して下さい. 
もしアプリ内で利用するトラッキングIDが１つだけであるならば、デフォルトトラッカーを利用するのが最も良い方法です.

### <a name="implementing-screen-measurement"></a>スクリーン計測の実装

アプリ内のビュー(画面)を自動で計測するためには、`GAITrackedViewController`を継承してください.
そして、`screenName`と呼ばれるプロパティにレポート内で利用したい画面の名前を設定してください.

例えば、"Home Screen"という名前を持つ画面を計測したいのであれば、この画面のビューコントローラのヘッダーは以下のようになっているはずです.

```
@interface HomeViewController : UIViewController
```

これを以下のように変更して下さい.

```
#import "GAITrackedViewController.h"

@interface HomeViewController : GAITrackedViewController
```

またGoogle Analyticsのレポート内で利用される画面の名前を設定する必要があります.
この処理に適した場所は、ビューコントローラの初期化メソッド内か、
あるいは`viewDidAppear:`メソッドの中です.

```
- (void)viewDidAppear:(BOOL)animated {
  [super viewDidAppear:animated];
  self.trackedViewName = @"About Screen";
}
```

スクリーン計測の詳細については、[スクリーン開発者ガイド](https://developers.google.com/analytics/devguides/collection/ios/v3/screens) を参照してください.

おめでとうございます！Google Analyticsにデータを送信する準備が完了しました.

## <a name="next-steps"></a>次のステップ

キャンペーン、アプリ内決済およびトランザクションやユーザーインタラクションイベントの計測など、Google Analyticsでは多くのことができます.
これらの機能を実装方法を学ぶためには以下の開発者ガイドを参照して下さい.

- [Advanced Configuration]() - 複数のトラッカー利用など、より便利な設定オプションについて学びます.
- [Measuring Campaigns]() - どのチャンネルやキャンペーンがアプリのインストールを促進したのか、理解するためにキャンペーン計測の実装方法を学びます.
- [Measuring Events]() - ボタン、ビデオや他のメディアなどのインタラクティブなコンテンツを利用した、ユーザーエンゲージメントを計測する方法を学びます.
- [Measuring In-App Payments]() - アプリ内決済やトランザクションの計測について学びます.
- [User timings]() - ロード時間やメディアとのエンゲージメントなど、アプリ内のユーザー時間を計測する方法を学びます.

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月19日.