# User Timings - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/usertimings](https://developers.google.com/analytics/devguides/collection/ios/v3/usertimings)

- - -

このドキュメントでは、Google Analytics SDK for iOS v3を利用してユーザー時間を計測する方法について説明します.

- [概要](#overview)
- [実装方法](#implementation)

## <a name="overview"></a>概要

ユーザー時間の計測は、Google Analyticsにおける一定時間を計測するネイティブな方法です.
この方法は、例えば、リソースのロード時間を計測するときなどに役立ちます.

Google Analyticsにおける経過時間は以下のフィールドで構成されます.

Field Name | Tracker Field | Type | Requried | Description
--- | --- | --- | --- | ---
Category | kGAITimingCategory | NSString | Yes | 時間イベントのカテゴリー
Value | kGAITimingValue | NSNumber | Yes | 計測された時間(ミリ秒)
Name | kGAITimingVar | NSString | No | 時間イベントの名前
Label | kGAITimingLabel | NSString | No | 時間イベントのラベル

ユーザー時間のデータは、User Timingsレポートで確認することができます.

## <a name="implementation"></a>実装

ユーザー時間をGoogle Analyticsに送信するには, 
`GAIDictionaryBuilder.createTimingWithCategory:interval:name:label:`を利用してタイミングのヒットを作成し、
`send:`メソッドを利用して送信してください.

```
/*
 * Called after a list of high scores finishes loading.
 *
 * @param loadTime The time it takes, in milliseconds, to load a resource.
 */
- (void)onLoad:(NSTimeInterval *)loadTime {

  // May return nil if a tracker has not already been initialized with a
  // property ID.
  id tracker = [[GAI sharedInstance] defaultTracker];

  [tracker send:[GAIDictionaryBuilder createTimingWithCategory:@"resources"    // Timing category (required)
                                                      interval:loadTime        // Timing interval (required)
                                                          name:@"high scores"  // Timing name
                                                         label:nil] build];    // Timing label
}
```

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.