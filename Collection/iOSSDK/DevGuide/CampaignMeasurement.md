# Campaign Measurement - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/campaigns](https://developers.google.com/analytics/devguides/collection/ios/v3/campaigns)

- - -

このドキュメントでは、Google Analytics SDK for iOS v2を利用してキャンペーンや流入元の計測を行う手段について説明します.

- [概要](#overview)
- [一般的なキャンペーンおよび流入元の属性](#general-campaign-traffic-source-attribution)
- [キャンペーンパラメータ](#campaign-parameters)

## <a name="overview"></a>概要

Google Analyticsでキャンペーン計測を行うことで、
アプリケーション内でキャンペーンや流入元の属性をユーザーアクティビティに付加することができます.
これらのオプションはGoogle Analytics SDK for iOSのキャンペーンおよび流入元の属性で利用可能です.

- [一般的なキャンペーンおよび流入元の属性](#general-campaign-traffic-source-attribution) - どのキャンペーンや参照元がアプリインストール後に行われたかを表します.

以降のセクションでは、いつどのように各タイプのキャンペーン計測を実装するかについて説明します.

## <a name="general-campaign-traffic-source-attribution"></a>一般的なキャンペーンおよび流入元の属性

アプリがインストールされた後、アプリはキャンペーン広告やウェブサイト、あるいは他のアプリといった参照元から起動されます.
このシナリオでは、トラッカーのキャンペーンフィールドに設定することで、
流入元やマーケティングキャンペーンへの参照をその後のセッションでのユーザーアクティビティに付属させます.

キャンペーンデータを送信する最も簡単な方法は
`[GAIDictionaryBuilder setCampaignParametersFromUrl:urlString]`を使うことです.
ここで`urlString`は、[Google Analyticsのキャンペーンパラメータ](https://developers.google.com/analytics/devguides/collection/ios/v3/campaign-params)を含んだURLのことです.
この後の例では、一度だけ送信したいため、キャンペーンデータはトラッカーには直接設定されていないことに注意してください.

```
/*
 * MyAppDelegate.m
 *
 * An example of how to implement campaign and referral attribution.
 * If no Google Analytics campaign parameters are set in the referring URL,
 * use the hostname as a referrer instead.
 */
- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *) url {

  NSString *urlString = [url absoluteString];

  id<GAITracker> tracker = [[GAI sharedInstance] trackerWithName:@"tracker"
                                                      trackingId:@"UA-XXXX-Y"];

  // setCampaignParametersFromUrl: parses Google Analytics campaign ("UTM")
  // parameters from a string url into a Map that can be set on a Tracker.
  GAIDictionaryBuilder *hitParams = [[GAIDictionaryBuilder alloc] init];

  // Set campaign data on the map, not the tracker directly because it only
  // needs to be sent once.
  [[hitParams setCampaignParametersFromUrl:urlString] build];

  // Campaign source is the only required campaign field. If previous call
  // did not set a campaign source, use the hostname as a referrer instead.
  if(![hitParams valueForKey:kGAICampaignSource]) && [url host].length !=0) {
    // Set campaign data on the map, not the tracker.
    [hitParams set:kGAICampaignMedium
           value:@"referrer"];
    [hitParams set:kGAICampaignSource
           value:[url host]];
  }

  [tracker send:[[[GAIDictionaryBuilder createAppView] setAll:hitParams] build]];
```

別の方法として、キャンペーンパラメーターとは別のフォームでのキャンペーン情報を持っている場合には、
手動でNSDictionaryをセットし、送信することができます.

```
// Assumes at least one tracker has already been initialized.
id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];

// Note that it's not necessary to set kGAICampaignKeyword for this email campaign.
NSMutableDictionary *campaignData = [NSDictionary alloc dictionaryWithObjectsAndKeys:
    @"email", kGAICampaignSource,
    @"email_marketing", kGAICampaignMedium,
    @"summer_campaign", kGAICampaignName,
    @"email_variation1", kGAICampaignContent, nil];

// Note that the campaign data is set on the Dictionary, not the tracker.
[tracker send:[[[GAIDictionaryBuilder createAppView] setAll:campaignData] build]];
```

## <a name="campaign-parameters"></a>キャンペーンパラメータ

キャンペーンパラメータはトラフィックの流入元やキャンペーンの情報をアプリに渡すのに利用されます.

以下の表では、一般的なキャンペーンの計測において、キャンペーンパラメータとして利用可能なパラメータを列挙しています.

Parameter | Description | Example(s)
--- | --- | ---
urm_campaign | キャンペーン名 : あるプロダクトのプロモーションや戦略的なキャンペーンを特定するキーワード解析に利用されます. | utm_campaign=spring_sale
utm_source | キャンペーンソース : 検索エンジン、ニュースレターや他の流入元などを特定するのに利用されます. | utm_source=google
utm_medium | Campaign Medium : emailやクリック単価(cpc)などの媒体を特定するのに利用されます. | utm_medium=cpc
utm_term | キャンペーン用語 : キーワード広告を提供するための有料検索と共に利用されます. | utm_term=running+shoes
utm_content | キャンペーンコンテンツ : A/Bテストやコンテンツターゲット広告など同じURLで異なるリンクや広告のために利用されます. | utm_content=logolink, utm_content=textlink
gclid | [AdWordsの自動タギングパラメータ](http://support.google.com/analytics/bin/answer.py?hl=en&answer=1033981) : Google AdWords広告の計測に利用されます.この値は動的に生成され、変更してはいけません. | 

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.