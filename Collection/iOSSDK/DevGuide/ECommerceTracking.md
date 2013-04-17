# Ecommerce Tracking - iOS SDK

このドキュメントでは、Google Analytics SDK for iOS v2を利用してアプリ内購入および収益を計測する方法について説明しています.

- 概要
- 実装
    - 通貨単位

## <a name="overview"></a>概要

Eコマース計測によってGoogle Analyticsにアプリ内購入および売上データを送信することができます.
Google AnalyticsにおけるEコマースデータは共有されたトランザクションIDを関連付けられた決済情報およびアイテムで構成されます.
Google Analytics SDK fo iOSでは、その関係はトランザクションオブジェクト生成しそれに対してアイテムを追加することで設定されます.

Eコマースデータは主に以下のようなリポートで利用されます

- Eコマース サマリー
- 商品の販売状況
- 販売実績
- トランザクション
- 購入までの間隔

## <a name="implementation"></a>実装

Google Analyticsを利用してトランザクションを計測するには３つのステップが必要です.

1. トランザクションオブジェクトを生成する.
2. アイテムオブジェクトを生成し、トランザクションオブジェクトに追加する.
3. sendTransaction メソッドでトランザクションを送信する.

以下の例では、ユーザーがアプリ内購入を管領した後に、onPurchaseCompleted が呼ばれると仮定しています.

```
- (void)onPurchaseCompleted
{
  GAITransaction *transaction = 
    [GAITransaction transactionWithId:@"0_123456"        // (NSString) Transaction ID, should be unique.
                     withAffilication:@"In-App Store"];  // (NSString) Affiliation
  transaction.taxMicros = (int64_t)(0.17 * 1000000);     // (int64_t) Total tax (in micros)
  transaction.shippingMicros = (int64_t)(0);             // (int64_6) Total shipping (in micros)
  transaction.revenueMicros = (int64_t)(2.16 * 1000000); // (int64_t) Total revenue (in micros)
  
  [transaction addItemWithSKU:@"L_789"                  // (NSString) Product SKU
            name:@"Level Pack: Space"                   // (NSString) Product name
                     category:@"Game expansions"        // (NSString) Product category
                  priceMicros:(int64_t)(1.99 * 1000000) // (int64_t) Product price (in micros)
                     quantity:1];                       // (NSInteger) Product quantity
        
  [[GAI sharedInstance].defaultTracker sendTransaction:transaction]; // Send the transaction.
}
```

### <a name="currency-type"></a>通貨単位

Google Analytics SDK for iOSでは、Eコマースの通過フィールドはマイクロ単位でなければなりません.(通過の100万倍)

例えは、4.5991という値を送信したい場合、
Google Analyticsにトランザクションを送信する際に、
上の例と同様に値をマイクロ値(つまり 4599100)に変換する必要があります.
SDKがGoogle Analyticsに値をディスパッチする際に、
その値は自動的に固定小数点型の10進数値、4.5991に変換されて送信されます.

>**NOTE:** マイクロ値に値を変換したとしても、SDKはGoogle Analyticsに通貨データを固定小数点型の10進数値を利用して送信します. これによって様々な通貨単位にわたって精度が失われないことが保証されます.

通貨シンボルはEコマースコードに含めるべきではありません.またカンマも利用してはいけません.

Eコーマスの通貨フィールドは払い戻しの必要なども考慮して、負の値もサポートしています.

- - - 

Last updated 2月 13, 2013.