# Custom Dimensions & Metrics - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/customdimsmets](https://developers.google.com/analytics/devguides/collection/ios/v3/customdimsmets)

- - -

この開発者ガイドでは、Google Analytics SDK for iOS v3を利用した
カスタムディメンションおよびメトリクスの実装法について説明しています.


- [概要](#overview)
- [値の設定及び送信](#setting-and-sending-values)
- [実装上の注意](#implementation-considerations)
	- [カスタムディメンション](#custom-dimensions)
	- [カスタムメトリクス](#custom-metrics)
	

** 関連ドキュメント **
>
> - [Overview: Custom Dims & Mets](https://developers.google.com/analytics/devguides/platform/features/customdimsmets-overview)
> - **Platform**
> 	- [Reference : Custom Dims & Mets](https://developers.google.com/analytics/devguides/platform/features/customdimsmets)
> - **Collection**
> 	- [Web Tracking: analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/custom-dims-mets)
> 	- [Android SDK](https://developers.google.com/analytics/devguides/collection/android/v3/customdimsmets)
> 	- [iOS SDK](https://developers.google.com/analytics/devguides/collection/ios/v3/customdimsmets)
> 	- [Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#customs)
> - **Configuration**
> 	- [Setup Custom Dims & Mets (Help Center)](http://support.google.com/analytics/bin/answer.py?answer=2709829)
> - **Reporting**
> 	- [Custm Dims & Mets in Reports](https://developers.google.com/analytics/devguides/platform/features/customdimsmets#reporting)
> - **Related Topics**
> 	- [Abount Custom Dimensions(Help Center)](http://support.google.com/analytics/bin/answer.py?hl=en&answer=2709828&topic=2709827&ctx=topic)

## <a name="overview"></a>概要

カスタムディメンションはGoogle Analytics内でヒット、ユーザーおよびセッションをメタデータとの関連付けを可能にします.
また、カスタムメトリクスはGoogle Analyticsで自信のメトリクスを作成し、インクリメントすることができます.

1. Google Analyticsのウェブインターフェースからカスタムディメンションおよびメトリクスの設定を行なってください. [設定方法についてはこちら](https://support.google.com/analytics/answer/2709829?hl=en&ref_topic=2709827)
2. アプリからカスタムディメンションおよびメトリクスをセットし送信してください.

カスタムディメンションおよびメトリクスは２つのフィールドで構成されています.

- `NSNumber` Index - カスタムディメンションあるいはメトリクスのインデックスです. インデックスは1から始まります.
- `NSNumber` Value - カスタムディメンションあるいはメトリクスの値です. メトリクスの場合には、整数値としてパースされるか、もしくはメトリクスが通過として設定されている場合には10進数の固定少数点としてパースされます.

## <a name="setting-and-sending-values"></a>値の設定及び送信

カスタムディメンションの値をセットし送信するには以下のようにします.

```
// May return nil if a tracker has not yet been initialized with a property ID.
id tracker = [[GAI sharedInstance] defaultTracker];

// Set the custom dimension value on the tracker using its index.
[tracker set:[GAIFields customDimensionForIndex:1]
       value:@"Premium user"]

[tracker set:kGAIScreenName
       value:@"Home screen"];

// Send the custom dimension value with a screen view.
// Note that the value only needs to be sent once, so it is set on the Map,
// not the tracker.
[tracker send:[[[GAIDictionaryBuilder createAppView] set:@"premium"
                                                  forKey:[GAIFields customDimensionForIndex:1]] build]];
```

カスタムディメンションの値は、
スクリーンビュー、イベント、Eコマーストランザクション、ユーザー時間、およびソーシャルインタラクションを含む、
Google Analyticsの全てのヒットタイプとともに送信することができます.
事前に定義された[スコープ](https://developers.google.com/analytics/devguides/platform/features/customdimsmets#scope)が、プロッセッシングのタイミングでディメンションの値とどのヒットが関連付けられるかを決定します.

カスタムメトリックの値をセットし送信するには以下のようにします.

```
// May return nil if a tracker has not yet been initialized with a property ID.
id tracker = [[GAI sharedInstance] defaultTracker];


// Set the custom metric to be incremented by 5 using its index.
[tracker set:[GAIFields customMetricForIndex:1]
       value:5];


[tracker set:kGAIScreenName
       value:@"Home screen"];

// Custom metric value is sent with this screen view.
[tracker send:[[GAIDictionaryBuilder createAppView] build]];
```

## <aname ="implementation-considerations"></a>実装上の注意

このセクションではカスタムディメンションあるいはメトリクスを実装する際に覚えておくべき点について説明します.

### <a name="custom-dimensions"></a>カスタムディメンションの注意点

ユーザーおよびセッションレベルスコープの値は過去のヒットにも提供されます.

- ユーザーあるいはセッションスコープレベルのカスタムディメンションの値は、過去のヒットも含めた現在のセッションに適用されます. もし現在のセッションの過去のヒットにたいしてセッションあるいはユーザースコープレベルのカスタムディメンションの値を適用したくないのであれば値をヒットに適用する前に[新しいセッション](https://developers.google.com/analytics/devguides/collection/ios/v2/sessions#manual-session)を開始してください.
- 例えば、でメンバーシップタイプをユーザーレベルカスタムディメンションで利用していて、セッションの途中でユーザーがメンバーシップをアップグレードした場合には、新しいカスタムディメンションの値をセットする前に新しい値を開始してください. これによって過去のヒットは古いメンバーシップの値が関連付けされ、新しいヒットには新しい値が関連付けされることを確実にします.

カスタムディメンションとビュー(プロファイル)フィルター

- ユーザーあるいはセッションレベルカスタムディメンションの値は、送信されたヒットがビュー(プロファイル)フィルターを適用されていていたとしても、現在のセッションおよび(あるいは)将来のセッションのヒットに適用されます.
- カスタムディメンションの値をフィルターする場合には、ヒットもカスタムディメンションのスコープにしたがってフィルターされます. データの処理の際にフィルターおよびカスタムディメンションの値がどのように作用するかについては[こちら](https://developers.google.com/analytics/devguides/platform/features/customdimsmets#filters)を参照してください.


### <a name="custom-metrics"></a>カスタムメトリクスの注意点

カスタムメトリックの値は、レポート内で集計されます.

- カスタムメトリックの値はGoogle Analyticsで予め定義されたメトリクスと同じように、レポート内で集計されます.そのため、レポート内で集計された合計値をインクリメントするために、カスタムメトリックの値に1を設定します.

カスタムメトリックとビュー(プロファイル)フィルター

- カスタムメトリックの値は一般的にはいつでも簡単にセットすることができますが、ビュー(プロファイル)フィルターで処理されるヒットにカスタムメトリクスの値をセットすることは避けてください.もしヒットがビュー(プロファイル)フィルターによってフィルターされると、関連付けされた全てのカスタムメトリックの値もフィルターされてしまいます.カスタムディメンション、メトリクスとビュー(プロファイル)フィルターについては[こちら](https://developers.google.com/analytics/devguides/platform/features/customdimsmets)を参照してください.

自動スクリーン計測への値のセット

- 自動スクリーン計測を通じたスクリーンビューにカスタムディメンションの値を適用するには、ビューコントローラーの`viewDidAppeara:`メソッド内で設定してください.例えば、ビューコントローラの実装ファイル`.m`はこのようになるはずです.


```
#import "myViewController.h"
#import "GAI.h"

@implementation myViewController

-(void)viewDidAppear
{
    id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];  // Get the tracker object.
    [tracker set:[GAIFields customDimensionForIndex:1]
           value:@"premium"];
    [super viewDidAppear:animated]   // Custom dimension value will be sent with the screen view.

}

// The remainder of the implementation is omitted.
```

個人を特定できるような情報(PII, Personal Identifiable Information)を送信しないでください.

- Google Analyticsのサービス利用規約では個人を特定できるような情報(PII)をGoogle Analyticsに送信することを禁じています. より詳しい情報は、[サービス利用規約](http://www.google.com/analytics/tos.html)を参照してください.

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.