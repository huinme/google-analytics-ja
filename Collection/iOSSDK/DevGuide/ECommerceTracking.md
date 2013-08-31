# Ecommerce Tracking - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/ecommerce](https://developers.google.com/analytics/devguides/collection/ios/v3/ecommerce)

- - -

このドキュメントでは、Google Analytics SDK for iOS v3を利用してアプリ内購入および収益を計測する方法について説明しています.

- [概要](#overview)
- [実装](#implementation)
    - [通貨単位の指定](#specifying-currency)
    
**関連ドキュメント**

> - **Platform**
> 	- [Reference:Ecommerce](https://developers.google.com/analytics/devguides/platform/features/ecommerce)
> - **Collection**
> 	- [WebTracking:analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/ecommerce)
> 	- [Android SDK](https://developers.google.com/analytics/devguides/collection/android/v3/ecommerce)
> 	- [iOS SDK](https://developers.google.com/analytics/devguides/collection/ios/v3/ecommerce)
> 	- [Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#ecomm)
> - **Configuration**
> 	- [Setting Global Currency(Help Center)](http://support.google.com/analytics/bin/answer.py?hl=en&answer=2960586)
> - **Related Topics**
> 	- [Supported Currency Codes](https://developers.google.com/analytics/devguides/platform/features/currencies)

## <a name="overview"></a>概要

Eコマース計測によってGoogle Analyticsにアプリ内購入および売上データを送信することができます.
Google AnalyticsにおけるEコマースデータは共有されたトランザクションIDを関連付けられた決済情報およびアイテムで構成されます.

トランザクションは以下のフィールドをを持っています.

Field Name | Tracker Field | Type | Requried | Description
--- | --- | --- | --- | ---
Transaction ID | kGAITransactionId | NSString | Yes | トランザクション(決済情報)を表すユニークなID. このIDは他のトランザクションIDと衝突してはいけません.
Affiliation | kGAITransactionAffiliation | NSString | Yes | トランザクションが所属するエンティティ(例えば特定のストアなど)
Revenue | kGAITransactionRevenue | NSNumber | Yes | 税額や配送費を含んだトランザクションの合計利益
Tax | kGAITransactionTax | NSNumber | Yes | トランザクションの合計課税額
Shipping | kGAITransactionShipping | NSNumber | Yes | トランザクションの輸送に関わる合計コスト
Currency code | kGAICurrencyCode | NSString | No | トランザクションのローカル通貨単位. ビュー(プロファイル)の通貨がデフォルト値になります.

アイテムは以下のフィールドを持っています.

Field Name | Tracker Field | Type | Requried | Description
--- | --- | --- | --- | ---
TransactionID | kGAITransactionId | NSString | Yes | アイテムが関連付けらているトランザクションのID.
Name | kGAIItemName | NSString | Yes | 製品名
SKU | kGAIItemSku | NSString| Yes | 製品のSKU
Category | kGAIItemCategory | NSString | No | 製品の所属カテゴリ
Price | kGAIItemPrice | NSNumber | Yes | 製品の価格
Quantity | kGAIItemQuantity | NSNumber | Yes | 製品の数量
Currency code | kGAICurrencyCode | NSString | No | トランザクションのローカル通貨単位. ビュー(プロファイル)の通貨がデフォルト値になります.

Eコマースデータは主に以下のレポートの中で利用されます.

- Eコマース オーバービュー
- プロダクト パフォーマンス
- セールス パフォーマンス
- トランザクション
- 購入時間

## <a name="implementation"></a>実装

Google Analyticsへトランザクションとアイテムのデータを送信するには、
一度にトランザクションとアイテムのフィールドをトラッカーに設定し送信することが必要になります.
例えば以下のようになります.

```
/*
 * Called when a purchase is processed and verified.
 */
- (void)onPurchaseCompleted {

  // Assumes a tracker has already been initialized with a property ID, otherwise
  // this call returns null.
  id tracker = [[GAI sharedInstance] defaultTracker];

  [tracker send:[[GAIDictionaryBuilder createTransactionWithId:@"0_123456",             // (NSString) Transaction ID
                                                   affiliation:@"In-app Store",         // (NSString) Affiliation
                                                       revenue:(int64_t) 2.16,          // (int64_t) Order revenue (including tax and shipping)
                                                           tax:(int64_t) 0.17,          // (int64_t) Tax
                                                      shipping:(int64_t) 0,             // (int64_t) Shipping
                                                  currencyCode:@"USD"] build]];         // (NSString) Currency code


  [tracker send:[[GAIDictionaryBuilder createItemWithTransactionId:@"0_123456",         // (NSString) Transaction ID
                                                               sku:@"L_789"             // (NSString) Product SKU
                                                          category:@"Game expansions"   // (NSString) Product category
                                                             price:(int64_t) 1.9,       // (int64_t)  Product price
                                                          quantity:1,                   // (NSInteger)  Product quantity
                                                      currencyCode:@"USD"] build]];     // (NSString) Currency code
}
```

Eコマースの通貨単位フィールドは払い戻しなどで必要になるためマイナスの値をサポートしています.

> **注意:** 通貨記号やカンマを通貨単位フィールドに含めてはいけません.

### <a name="specifying-currency"></a>通貨単位の指定

デフォルトでは、トランザクションの値はレポートされるビュー(プロファイル)の通貨単位になります.

トランザクションと関連する製品のローカル通貨単位を上書きするためには、
トランザクションとアイテムヒットの通貨単位コードフィールドに新しい通貨単位コードをセットしてください.
サポートされる通貨単位コードの完全なリストは、[サポートされる通貨単位](https://developers.google.com/analytics/devguides/platform/features/currencies)を参照してください.

```
/*
 In this example, the currency of the transaction is set to Euros. The
 currency values will appear in reports using the global currency
 type of the view (profile).
 */
- (void)onPurchaseCompleted {

  // Assumes a tracker has already been initialized with a property ID, otherwise
  // this call returns null.
  id tracker = [[GAI sharedInstance] defaultTracker];

  [tracker send:[GAIDictionaryBuilder createTransactionWithId:@"0_123456",             // (NSString) Transaction ID, should be unique among transactions.
                                                  affiliation:@"In-app Store",         // (NSString) Affiliation
                                                      revenue:(int64_t) 2.16,          // (int64_t) Order revenue (including tax and shipping)
                                                          tax:(int64_t) 0.17,          // (int64_t) Tax
                                                     shipping:(int64_t) 0,             // (int64_t) Shipping
                                                 currencyCode:@"EUR"] build];          // (NSString) Currency code
}
```

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.