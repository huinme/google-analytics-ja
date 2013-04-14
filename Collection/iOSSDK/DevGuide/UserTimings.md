# User Timings - iOS SDK

Source : [https://developers.google.com/analytics/devguides/collection/ios/v2/usertimings?hl=ja](https://developers.google.com/analytics/devguides/collection/ios/v2/usertimings?hl=ja)

このドキュメントでは、Google Analytics SDK for iOS v2を利用してユーザー時間を計測する方法について説明します.

- [概要](#overview)
- [実装](#implementation)

## <a name="overview"></a>概要

ユーザー時間の計測は、Google Analyticsにおける一定時間を計測するネイティブな方法です.
この方法は、例えば、リソースのロード時間を計測するときなどに役立ちます.

Google Analyticsにおける経過時間は以下のフィールドで構成されます.

- NSString category : 時間イベントのカテゴリー
- NSTimeinterval interval : 計測された時間(秒)
- NSString (Optional) Name : 時間イベントの名前
- NSString (Optional) Label : 時間イベントのラベル

ユーザー時間のデータは、User Timingsレポートで確認することができます.

## <a name="implementation"></a>実装

ユーザー時間をGoogle Analyticsに送信するには, 
メソッド sendTimingWithCategory:withTimeInterval:withName:withLabel を呼び出し、
カテゴリーと同様に時間を与えて下さい.
以下の例では、あるリソースのローディングが終了した後に onLoad: メソッドが呼ばれると仮定しています.
このケースではゲームのハイスコアのリストになります.

```
- (void)onLoad:(NSTimeInterval *)loadTime
{
    [tracker sendTimingWithCategory:@"resources"
                   withTimeInterval:loadTime
                           withName:@"high scores"
                          withLabel:nil]
}
```

- - - 

Last updated 2月 15, 2013.