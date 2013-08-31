# Dispatching - iOS SDK

> Source : [https://developers.google.com/analytics/devguides/collection/ios/v3/dispatch](https://developers.google.com/analytics/devguides/collection/ios/v3/dispatch)

- - -


> **Note:** このドキュメントの中で参照されているローカルディスパッティメソッドは、将来Google Playサービスの一部としてGoogle Analyticsが利用可能になるために、非推奨に指定されています.
> ローカルディスパッチメソッドは、Googleエクスペリエンスではないデバイスでは利用可能です.

このドキュメントでは、Google Analytics SDK for iOS v3を利用して
どのようにGoogle Analyticsのディスパッチされたデータを管理するかについて説明しています.

- [概要](#overview)
- [定期的なディスパッチ](#periodic-dispatch)
- [手動のディスパッチ](#manual-dispatch)

## <a name="overview"></a>概要

Google Analytics SDk for iOSによって集められたデータは、
別スレッドでGoogle Analyticsへとディスパッチされるまでローカルに保存されます.

データは、各プロファイルのローカルタイムゾーン設定で、**翌日の午前4時までに**ディスパッチされて受信されている必要があります.
その後に受信されたいかなるデータもレポートには表示されません.
例えば、あるヒットが午後11時59分にローカルキューに追加されたとすると、
午前3時59分、つまり4時間以内にディスパッチされなければなりません.
また一方では、午前12時にキューに追加されたヒットがレポートに表示されるには、
28時間位内つまり翌日の午前3時59分までにディスパッチされなければなりません.

## <a name="periodic-dispatch"></a>定期的なディスパッチ

デフォルトでは、データはGoogle Analytics SDKから2分ごとにディパッチされます.

```
// Set the dispatch period in seconds.
[GAI sharedInstance].dispatchPeriod = 30;
```

マイナスの値を設定することで定期的なディスパッチを無効にすることもできます. 
その場合には、データをGoogle Analyticsに送信するために[手動ディスパッチ](#manual-dispatch)を行う必要があります.

```
// Disable periodic dispatch by setting dispatch period to a value less than 1.
[GAI sharedInstance].dispatchPeriod = 0;
```

もしユーザーがネットワークアクセスを失ったり、
ディスパッチされていないhitsが残っているのにアプリを終了した場合には、
それらのhitsはローカルストレージに保存されます. 
それらのhitsは次回アプリ起動中にディスパッチが行われるときに合わせてディスパッチされます.

## <a name="manual-dispatch"></a>手動のディスパッチ

手動で、ヒットのディスパッチを行う場合、
例えばデバイスの電波が他のデータを送信するのに利用されていることを知っている場合には以下のようにします.

```
[[GAI sharedInstance] dispatch];
```

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.

