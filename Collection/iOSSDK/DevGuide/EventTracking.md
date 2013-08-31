# Event Tracking - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/events](https://developers.google.com/analytics/devguides/collection/ios/v3/events)

- - -

この開発者ガイドでは、Google Analytics SDK for iOS v3を利用してアプリ内でどのようにイベントを計測するのかについて説明します.

- [概要](#overview)
- [実装方法](#implementation)

## <a name="overview"></a>概要

イベントは、ボタンの押下やゲームのあるアイテムの使用など、
アプリ内のインタラクティブなコンポーネントとユーザーのインタラクションについてデータを収集するのに有効な手段です。

イベントは、アプリのコンテンツとユーザーインタラクションを記述するのに利用できる、４つのフィールドで構成されています。

Field Name | Tracker Field | Type | Required | Description
--- | --- | --- | --- | ---
Category | kGAIEventCategory | NSString | Yes | イベントカテゴリ
Action | kGAIEventAction | NSString | Yes | イベントアクション
Label | kGAIEventLabel | NSString | No | イベントラベル
Value | kGAIEventValue | NSNumber | No | イベント値

## <a name="implementation"></a>実装方法

Google Analyticsへイベントを送信するには、以下の例のように
`GAIDictionaryBuilder.createEventWithCategory:action:label:value:`を利用し、
ヒットを送信してください.

```
// May return nil if a tracker has not already been initialized with a property
// ID.
id<GAITracker> = [[GAI sharedInstance] defaultTracker];

[tracker send:[[GAIDictionaryBuilder createEventWithCategory:@"ui_action",     // Event category (required)
                                                  withAction:@"button_press",  // Event action (required)
                                                   withLabel:@"play_button",   // Event label
                                                   withValue:nil] build]];     // Event value
```

> **Note :** Google Android(Analylticsの誤字?) SDK for iOSは は短い期間に大量の送信要求が発生した場合には、イベントを絞る(減らす)ことがあります.

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.