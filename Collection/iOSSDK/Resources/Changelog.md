# Google Analytics SDK for iOS Changelog

> Source : [https://developers.google.com/analytics/devguides/collection/ios/changelog](https://developers.google.com/analytics/devguides/collection/ios/changelog)

このページではGoogle Analytics SDK for iOSに加えられた変更をまとめています. 
最新の情報を得るためにこのページを定期的に確認することをおすすめします.
また、下の [Subscribe](#subscribe-to-related-changelgos) セクションにリストされたフィードから変更点を取得することができます.

## <a name="subscribe-to-related-changelgos"></a>変更ログの購読

[All Google Analytics API Changelgos](https://developers.google.com/analytics/devguides/changelog) - [RSS](https://developers.google.com/analytics/changelogs/xml/analytics.xml)  
コレクション、設定、およびレポートAPIなど全てを含みます.

[All Collection API & SDK Changelogs](https://developers.google.com/analytics/devguides/collection/changelog) - [RSS](https://developers.google.com/analytics/changelogs/xml/analytics-collection.xml)  
Webトラッキング(ga.jsおよびanalytics.js)、Android SDK, iOS SDK や 計測プロトコルについての変更を含みます.

[iOS SDK Changelog](https://developers.google.com/analytics/devguides/collection/ios/changelog#changelog) - [RSS](https://developers.google.com/analytics/changelogs/xml/analytics-collection-ios.xml)

## Version 3.0.0 のリリース (2013年8月16日)

このリリースは以下の変更を含んでいます.

- バージョン3.0の最初のリリースになります.
- analytics.jsとの調整のためにSDKのAPIが変更されました. `track<hittype>`および`send<hittype>`メソッドが削除されました.代わりに`send`および`GAIDictionaryBuilder`コンストラクタメソッドを使用して下さい.
- `GAITracker`プロトコルのほとんどのプロパティが削除されました.代わりに各種フィールドの`set`メソッドを利用してください.
- `set`メソッドを利用した全てのパラメーターは永続化されます. これまでは、いくつかのパラメーターはその次のsend/track呼び出しにのみ利用されていました.
- SDKのカスタムロギングを自身で実装できるように`GAILogger`プロトコルが利用可能になりました.
- システムの最小必要要件が変更されました. 詳細はReadme.txtを確認してください.
- すべてのSDKアクティビティ(データベースおよびネットワークアクセス)は別のスレッドで行われます.
- `clientId`が読み込み可能になりました. `[tracker get:kGAIClientId]`メソッドを呼んで下さい. この処理は`cilendId`がデータベースから読み出されるまで処理をブロックすることに注意してください.
- 2000バイト以上のデータでないかぎり、SDKは`POST`メソッドを使わないようになりました. 代わりに`GET`を利用します.
- HTTPレスポンスコードに基づいてSDKはヒットの送信をリトライしなくなりました.

2013年8月16日(金)

## Version 2.0beta4 のリリース (2013年1月8日)

v3.0の邦訳のため、以下省略

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月22日.