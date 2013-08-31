# Screen - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/screens](https://developers.google.com/analytics/devguides/collection/ios/v3/screens)

- - -

このドキュメントではスクリーンおよび、Google Analytics SDK for iOS v3を利用してスクリーン計測を行う方法について説明しています.

- [オーバービュー](#overview)
- [手動のスクリーン計測](#manual-screen-measurement)
- [自動のスクリーン計測](#automatic-screen-measurement)

    
## <a name="overview"></a>オーバービュー

Google Analyticsにおけるスクリーンは、ユーザーがアプリ内で見たコンテンツについて表しています.
Web解析における同様のコンセプトはページビューです.
スクリーンビューの計測によって、どのコンテンツがユーザーに最も見られているかを知り、
また異なるコンテンツ間をどのように遷移しているのかを知ることができるようになります.

スクリーンビューは、Google Analytics レポートでスクリーン名として利用される1つの文字列フィールドで構成されています.

Field Name | Tracker Field | Type | Required | Description
--- | --- | --- | --- | ---
Screen Name | kGAIScreenName | NSString | Yes | アプリケーションスクリーンの名前

スクリーンビューのデータは主に、以下のレポートで利用されます.

- スクリーン レポート
- エンゲージメントフロー

## <a name="manual-screen-measurement"></a>手動のスクリーン計測

手動でスクリーンビューを送信したい場合には、 
トラッカーのスクリーンフィールドに値をセットし、ヒットの送信を行なってください.


```
// May return nil if a tracker has not already been initialized with a
// property ID.
id tracker = [[GAI sharedInstance] defaultTracker];

// This screen name value will remain set on the tracker and sent with
// hits until it is set to a new value or to nil.
[tracker set:kGAIScreenName
       value:@"Home Screen"];

[tracker send:[[GAIDictionaryBuilder createAppView] build]];
```


## <a name="automatic-screen-measurement"></a>自動のスクリーン計測

`GAITrackedViewController`クラスを利用することで、自動でスクリーンビューを計測することができます.
各ビューコントローラを`GAITrackedViewController`を継承して作成し、
`screenName`という名前のプロパティを追加してください.
このプロパティはスクリーン名フィールドにセットするために利用されます.

```
//
// MyViewController.h
// An example of using automatic screen tracking in a ViewController.
//
#import "GAITrackedViewController.h"

// Extend the provided GAITrackedViewController for automatic screen
// measurement.
@interface AboutViewController : GAITrackedViewController

@end


//
// MyViewController.m
//
#import "MyViewController.h"
#import "AppDelegate.h"

@implementation MyViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Set screen name.
    self.screenName = @"Home Screen";
}

// Rest of the ViewController implementation.
@end
```

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.

