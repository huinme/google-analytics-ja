# Event Tracking - iOS SDK

Source : [https://developers.google.com/analytics/devguides/collection/ios/v2/events?hl=ja](https://developers.google.com/analytics/devguides/collection/ios/v2/events?hl=ja)

この記事では、Google Analytics SDK for iOS v2を利用してアプリ内でどのようにイベントを計測するのかについて説明します.

- オーバービュー
- 実装方法

## オーバービュー

イベントは、ボタンの押下やゲームのあるアイテムの使用など、
アプリ内のインタラクティブなコンポーネントとユーザーのインタラクションについてデータを収集するのに有効な手段です。

イベントは、アプリのコンテンツとユーザーインタラクションを記述するのに利用できる、４つのフィールドで構成されています。

- NSString カテゴリー(Category)
- NSString アクション(Action)
- NSString ラベル(Label)
- NSNumber (Optional)バリュー(Value), 64bit整数値

## 実装

イベントを送信するには、sendEventWithCategory:withAction:withLabel:withValue: メソッドを読んで下さい.
例えば、ボタンの押下は以下のコードで計測することができます.

```
[tracker sendEventWithCategory:@"uiAction"
                    withAction:@"buttonPress"
                     withLabel:buttonName
                     withValue:[NSNumber numberWithInt:100]];
```

> **Note :** Google Android(Analylticsの誤字?) SDK for iOSは は短い期間に大量の送信要求が発生した場合には、イベントを絞る(減らす)ことがあります.

- - -

Last updated : 1月 9, 2013.