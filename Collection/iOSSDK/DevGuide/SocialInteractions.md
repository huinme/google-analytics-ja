# Social Interactions - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/social](https://developers.google.com/analytics/devguides/collection/ios/v3/social)

- - -

この開発者ガイドでは、Google Analytics SDk for iOS v3を利用したソーシャルインタラクションの計測方法について説明しています.

- [概要](#overview)
- [実装方法](#implementation)

**関連ドキュメント**

> - [Overview: Social Interactions](https://developers.google.com/analytics/devguides/platform/features/social-interactions-overview)
> - **Platform**
> 	- [Refercene: Social Interactions](https://developers.google.com/analytics/devguides/platform/features/social-interactions)
> - **Collection**
> 	- [Web Tracking: gs.js](https://developers.google.com/analytics/devguides/collection/gajs/gaTrackingSocial)
> 	- [Web Tracking: analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/social-interactions)
> 	- [Andrdoid SDK](https://developers.google.com/analytics/devguides/collection/android/v3/social)
> 	- [iOS SDK](https://developers.google.com/analytics/devguides/collection/ios/v3/social)
> 	- [Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#social)
> - **Reporting**
> 	- [Social Interaction Dims & Mets](https://developers.google.com/analytics/devguides/reporting/core/dimsmets/socialinteractions)
> - **Related Topics**
> 	- [Working Demo (ga.js)](http://analytics-api-samples.googlecode.com/svn/trunk/src/tracking/javascript/v5/social/facebook_js_async.html)
> 	- [Sample Code (gs.js)](https://code.google.com/p/analytics-api-samples/source/browse/trunk/src/tracking/javascript/v5/social/ga_social_tracking.js)
> 	- [About Social Analytics (Help Center)](http://support.google.com/analytics/bin/answer.py?answer=1683971)


## <a name="overview"></a>概要

ソーシャルインタラクションの計測によって、
共有やコンテンツに埋め込まれたレコメンデーションウィジェットなど、
ユーザーによる様々なソーシャル・ネットワークとのインタラクションを計測することができます。

ソーシャルインタラクションは以下のフィールドで構成されています.

Field Name | Tracker Field | Type | Requried | Description
--- | --- | --- | --- | ---
Social Network | kGAISocialNetwork | NSString | Yes | ユーザーがインタラクションを行ったソーシャルネットワーク(Facebook, Google+, Twitterなど)
Social Action | kGAISocialAction | NSString | Yes | 発生したソーシャルアクション(Like, Share, +1など)
Social Target | kGAISocialTarget | NSString | Yes | ソーシャルアクションが発生したコンテンツ(特定の記事, ビデオなど)

Google Analytics SDK for iOS v3.xによって収集されたソーシャルインタラクションのデータは、
カスタムレポート、もしくは[Core Reporting API](https://developers.google.com/analytics/devguides/reporting/core/dimsmets/socialinteractions)で利用可能です.

## <a name="implementation"></a>Implementation

Google Analyticsへソーシャルインタラクションを送信するには、
次の例のように`GAIDictionaryBuilder.createSocialWithNetwork:action:target`を利用します.
ここでTwitterのTweetボタンはAnalytics開発者ページのホームで行われているとします.

```
// May return nil if a tracker has not already been initialized with a property
// ID.
id tracker = [[GAI sharedInstance] defaultTracker];

NSString *targetUrl = @"https://developers.google.com/analytics";

[tracker send:[GAIDictionaryBuilder createSocialWithNetwork:@"Twitter"          // Social network (required)
                                                     action:@"Tweet"            // Social action (required)
                                                     target:targetUrl] build];  // Social target
```

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.