# Social Interactions - iOS SDK

Source : [https://developers.google.com/analytics/devguides/collection/ios/v2/social?hl=ja](https://developers.google.com/analytics/devguides/collection/ios/v2/social?hl=ja)

この開発者ガイドでは、Google Analytics SDk for iOS v2を利用したソーシャルインタラクションの計測方法について説明しています.

- [オーバービュー](#overview)
- [実装方法](#implementation)

## <a name="overview"></a>Overview

ソーシャルインタラクションの計測によって、
共有やコンテンツに埋め込まれたレコメンデーションウィジェットなど、
ユーザーによる様々なソーシャル・ネットワークとのインタラクションを計測することができます。

ソーシャルインタラクションは以下のフィールドで構成されています.

- `NSString` network - ユーザーがインタラクションを行ったソーシャルネットワークを表します.(例 : Google+, Facebook, Twitter, etc).
- `NSString` action - 実行されたソーシャルアクションを表します.(例 :  ライク, 共有, +1, etc).
- `NSString` target(optional) - ソーシャルアクションが行われたコンテンツを表します. (例 : 特定の記事やビデオ).

Google Analytics SDK for iOS v2.xによって収集されたソーシャルインタラクションのデータは、
カスタムレポートでのみ利用可能です.

## <a name="implementation"></a>Implementation

Google Analyticsへソーシャルインタラクションを送信するには、
次の例のような`sendSocial:withAction:withTarget:`メソッドを呼びます.
この例では、Analyticsの開発者ページのTwitterのツイートボタンを計測しています.

```
[tracker sendSocial:@"Twitter"
         withAction:@"Tweet"
 withTarget:@"https://developers.google.com/analytics"]; // Send social interaction.
```

- - - 

[Noted](https://developers.google.com/readme/policies?hl=ja)を覗いて、このページの内容は[Creative Commons Attribution 3.0 License](http://creativecommons.org/licenses/by/3.0/)の下で提供されています. またコードサンプルは [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0) の下で提供されています

 Last updated 5月 2, 2013.