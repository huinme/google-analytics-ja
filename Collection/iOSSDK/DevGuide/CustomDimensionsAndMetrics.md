# Custom Dimensions & Metrics - iOS SDK

Source : https://developers.google.com/analytics/devguides/collection/ios/v2/customdimsmets

## コンテンツ

- [オーバービュー](#overview)
- [カスタムディメンション](#custom-dimension)
    - [設定](#custom-dimension-setting-values)
    - [実装上の注意](#custom-dimension-implementation-considerations)
        - [ユーザーおよびセッションレベルのスコープ](#custom-dimension-user-and-session-level-scope)
        - [プロファイルフィルター](#custom-dimension-profile-filters)
        - [自動のスクリーン計測](#custom-dimension-automatic-screen-measurement)
        - [個人を特定可能な情報について](#custom-dimension-personally-identifiable-infomation)
- [カスタムメトリクス](#custom-metrics)
    - [設定](#custom-metrics-setting-values)
    - [実装上の注意](#custom-metrics-implementation-considerations)
        - [プロファイルフィルター](#custom-metrics-profile-filters)

## <a name="overview"></a>オーバービュー

>実装を始める前に、カスタムディメンションおよびメトリクスがどのように動作するかの詳細を説明している[カスタムディメンションおよびメトリクス -機能の紹介-](https://developers.google.com/analytics/devguides/platform/features/customdimsmets)を読んで下さい.

Google Analytics SDK for iOSを利用してカスタムディメンションあるいはメトリクスを設定する場合、
それらの値はディスパッチされた次回のヒットのパラメータとして送信されます.
それらの値は、スクリーンビュー、イベント、Eコマーストランザクション、ユーザー時間、ソーシャルインタラクションなど
Google Analyticsの全てのタイプのヒットにも適用可能です。

カスタムディメンションあるいはメトリクスを実装するには２つのステップが必要になります.

1. **Google AnalyticsのWebインターフェイスを利用してカスタムディメンションあるいはメトリクスを定義する.**  
   ウェブインターフェースのプロパティ設定からカスタムディメンションおよびメトリクスのインデックス、名前、およびスコープを設定して下さい.
   [カスタムディメンションあるいはメトリクスの定義の仕方について](https://developers.google.com/analytics/devguides/collection/ios/v2/customdimsmets#)
2. **カスタムディメンションおよびメトリクスの値を設定し収集するためのコードを実装する**  
   前のステップで定義したインデックス群にあるGoogle Analyticsのヒットに対して
   カスタムディメンションおよびメトリクスを設定するために、
   コードを加えて下さい.

この記事の残りの部分では、
Google Analyticsにカスタムディメンションおよびメトリクスを設定し送信するために、
どのようにコードを実装すればいいのかについて説明します.

## <a name="custom-dimension"></a>カスタムディメンション

カスタムディメンションの値は全てのGoogleAnalyticsのヒットタイプにセットされます.
値を適用したいヒットをコールするまえにカスタムディメンションの値をセットするだけで利用できます.

カスタムディメンションのスコープの定義は、(ヒットの)処理のタイミングで、
どの追加のヒットがその値と関連付けられるかを決定します.

カスタムディメンションの値を設定するには以下の２つのフィールドが必要になります.

- NSNumber Index - カスタムディメンションが定義されているインデックスです.このインデックスは1-based(※1から始まるの意味だと思われる)です.
- NSString Value - カスタムディメンションの値です.

### <a name="custom-dimension-setting-values"></a>設定

カスタムディメンションの値を設定するには、値を関連付けたいヒットを呼ぶ前に、setCustom:dimension: メソッドを呼んで下さい.

```
id tracker = [GAI sharedInstance].defaultTracker; // Get the tracker object.
[tracker setCustom:1
         dimension:@"myValue"]; // Set the dimension value for index 1.
[tracker sendView:screenName]; // Dimension value is associated and sent with this hit.         
```

### <a name="custom-dimension-implementation-considerations"></a>実装上の注意

このセクションでは、カスタムディメンションを実装するにあたって注意すべきことについてまとめています.

#### <a name="custom-dimension-user-and-session-level-scope"></a>ユーザーおよびセッションレベルのスコープ

[ユーザーあるいはセッションレベルスコープ](https://developers.google.com/analytics/devguides/platform/features/customdimsmets#scope)を持つカスタムディメンションは、過去も含めて現在のセッションの全てのヒットに適用されます.
もし現在のセッションにおいて、ユーザーあるいはセッションレベルのスコープを持つカスタムディメンションを過去のヒットに適用したくないのであれば、
それらの値がヒットに適用される前に[新しいセッションを開始](https://developers.google.com/analytics/devguides/collection/ios/v2/sessions#manual-session)して下さい.

例えば、もしユーザーレベルスコープのカスタムディメンションとして会員タイプを利用している場合に、
ユーザーがセッションの途中で会員タイプをアップグレードした時には、
新しいカスタムディメンションの値を設定する前に新しいセッションを開始したいと考えるかもしれませｎ.
これによって、アップグレードする前のヒットは古い会員タイプと関連付けられ、
また新しいヒットは新しいカスタムディメンションの値と関連付けられることを確実にします.

#### <a name="custom-dimension-profile-filters"></a>プロファイルフィルター

ユーザーあるいはセッションレベルのカスタムディメンションの値は、
たとえそれがプロファイルでフィルターされたヒットだとしても、
現在と将来のセッションにわたって値が適用されることになります.

カスタムディメンションの値をフィルタリングするときには、
各ヒットは、値が設定されているカスタムディメンションのスコープに応じてフィルタリングされます.

データが処理されるときに、フィルターおよびカスタムディメンションの値がどのように作用するかは[こちら](https://developers.google.com/analytics/devguides/platform/features/customdimsmets#filters)をご覧ください。

#### <a name="custom-dimension-automatic-screen-measurement"></a>自動のスクリーン計測

カスタムディメンションの値を、自動スクリーン計測を通じて送信されたスクリーンビューに適用するためには、
ビューコントローラの viewDidAppear: メソッドの中で値を設定してください.
例えば、ビューコントローラの実装(.m)ファイルが以下のようにしてください.

```
#import "myViewController.h"
#import "GAI.h"

@implementation myViewController

- (void)viewDidAppear
{
    id tracker = [GAI sharedInstance].defaultTracker; // Get the tracker object.
    [tracker setCustom:1
             dimension:@"myValue"]; // Set the custom dimension value.
    [super viewDidAppear:animated]; // Custom dimension value will be sent with the screen view.
}

// The remainder of the implemantation is omitted 
```

#### <a name="custom-dimension-personally-identifiable-infomation"></a>個人を特定可能な情報は値として送信しないでください.

Google Analyticsの[利用規約](http://www.google.com/analytics/tos.html)ではどんな個人情報(個人を特定可能な情報(PII))も
Google Analyticsのサーバに送信することを禁止しています.
より詳しくは[利用規約](http://www.google.com/analytics/tos.html)を確認して下さい.

## <a name="custom-metrics"></a>カスタムメトリクス

カスタムディメンションは特定のヒットに適用し、またスコープと共に注意深く管理されなければいけないのに対して、
カスタムメトリクスが適用されたヒットは一般的にはリポートに影響を及ぼしません.
そのため、カスタムメトリクスはその値が判明したタイミングでいつでも設定することができます.

カスタムメトリクスの値を設定するには以下のフィールドが必要です.

- NSInteger Index - カスタムメトリクスが定義されているインデックスです.
- NSNumber Value - カスタムメトリクスの値で、64ビット整数として扱われます. 負の値も利用できます.

### <a name="custom-metrics-setting-values"></a>設定

カスタムメトリクスの値を設定するには、
別のメソッドが呼び出される前に
setCustom:metric: メソッドを呼び出して下さい.


```
id tracker = [GAI sharedInstance].defaultTracker; // Get the tracker object.
[tracker setCustom:1
            metric:[NSNumber numberWithLongLong:1]]; // Increment the metric at index 1.
[tracker sendView:screenName]; // Metric value sent with this hit.            
```

### <a name="custom-metrics-implementation-considerations"></a>実装上の注意

残りのセクションでは、カスタムディメンションあるいはメトリクスのり用を計画する場合に覚えておくべきことについてまとめています.

#### <a name="custom-metric-values-are-aggregated-in-reports"></a>カスタムメトリクスの値の集計

カスタムメトリクスの値は、
Google Analyticsで事前に定義された他のメトリクスと同じように、
レポートの中で集約されます.
そのため、レポートの中で集約された合計値をインクリメントしたい場合には、
カスタムメトリクスの値に1を設定するようにして下さい.

#### <a name="custom-metrics-profile-filters"></a>プロファイルフィルター

カスタムメトリクスが通常はいつでも設定できるということは便利ですが、
プロファイル設定でフィルタリングされるヒットに対してカスタムメトリクスの値を設定することは避けて下さい.
もしヒットがプロファイルフィルターによってフィルタリングされるのであれば、
関連付けされた全てのカスタムメトリクスもフィルタリングされることになります.
カスタムディメンションおよびメトリクスとプロファイルフィルターの詳細については[こちら](https://developers.google.com/analytics/devguides/platform/features/customdimsmets)を見て下さい.

- - - 
Last updated 1月9日, 2013.