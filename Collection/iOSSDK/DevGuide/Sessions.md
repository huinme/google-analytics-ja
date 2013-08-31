# Sessions - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/sessions](https://developers.google.com/analytics/devguides/collection/ios/v3/sessions)

- - -

このドキュメントはGoogle Analytics SDK for iOS v3に関係するセッショッンの高レベルの概要を提供しています.


- [概要](#overview)
- [セッションの管理](#session_management)
    
## <a name="overview"></a>概要

セッションはアプリにおけるユーザーインタラクションのひとつの期間を表します.
Web解析では「訪問(visit)」が相当します.
訪問と同様に、セッションはスクリーンビュー、イ
ベントやEコマーストランザクションなどの計測アクティビティの
便利なコンテナーとして機能します.

## <a name="session_management"></a>セッションの管理

デフォルトでは、Google Analyticsは30分位内に受信したヒットを同じセッションとしてまとめます.
この期間はプロパティレベルで設定可能です.
[セッションのタイムアウト期間の設定方法についてはこちら.](https://support.google.com/analytics/answer/2795871?hl=en)

**手動のセッション管理**

手動でセッションを開始したり終了するには、
トラッカーにセッション制御のパラメーターをセットしてください.

```
// May return nil if a tracker has not yet been initialized.
id tracker = [[GAI sharedInstance] defaultTracker];

// Start a new session. The next hit from this tracker will be the first in
// a new session.
[tracker set:kGAISessionControl
       value:@"start"];
```

```
// End a session. The next hit from this tracker will be the last in the
// current session.
[tracker set:kGAISessionControl
       value:@"end"];
```

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.
     