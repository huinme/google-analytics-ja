# Google Analytics Collection Limtis and Quotas

> Source : [https://developers.google.com/analytics/devguides/collection/ios/limits-quotas](https://developers.google.com/analytics/devguides/collection/ios/limits-quotas)

- - -

このドキュメントでは、全てのGoogle Analyticsタグ、ライブラリおよびSDKにおける、
コレクションの制限とクォータについて説明しています.

- [プロパティ仕様](#property-specific)
	- [gs.js レガシーライブラリ](#gajs-legacy-libraries)
	- [ユニバーサルアナリティクス](#universal-analytics-enabled)
- [クライアントライブラリ/SDK仕様](#client-library-sdk-specific)
	- [gs.js](#gajs)
	- [analytics.js](#analyticsjs)
	- [Android SDK](#android-sdk)
	- [iOS SDK](#ios-sdk)
	
## 概要

Google Analyticsは数百万のサイトで利用されています.
処理可能な量以上のデータ受信からシステムを保護するため、
またシステムリソースの平等な分散を確実にするため、
一定の制限が行われています.
我々のポリシーは以下のとおりですが、変更される可能性もあります.

以降のクォータおよび制限は、Google Analtyicsのコレクションタグ、ライブラリおよびSDKに適用されます.
プロパティごとの制限と、クライアントライブラリごとの制限が存在します.

## <a name="property-specific"></a>プロパティ仕様

ウェブプロパティ/プロパティ/トラッキングIDには以下の制限があります.

- **プロパティ**あたり1ヶ月に1000万ヒットまで

もしこの制限を超えた場合には、
Google Analyticsチームはプレミアムへのアップグレード、
もしくは、Google Analyticsに送信するデータ量を削減するためのクライアントサンプリング制限
について確認するために連絡をとることがあります.

[プレミアムでの１ヶ月の制限](http://www.google.com/analytics/premium/)について知りたい場合は、アカウントマネージャーあるいはサービス代理人へ連絡してください.

### <a name="gajs-legacy-libraries"></a>gs.js レガシーライブラリ

ga.js/モバイルスニペットやその他のレガシーライブラリには以下の制限があります.

- **セッション**あたりEコマース(アイテムおよびトランザクションヒットタイプ)を除いて500ヒットまで.

もしこの制限を超えた場合には、そのセッションについて以降のヒットは処理が行われません.

### <a name="universal-analytics-enabled"></a>ユニバーサルアナリティクス

analytics.js, Android/iOS SDKおよび計測プロトコルでは以下の制限があります.

- **訪問者ひとり**あたり1日 20万ヒットまで
- **セッション**あたり500ヒットまで

この制限のいずれかひとつでも超えた場合には、
当該セッションあるいは当日中の以降のヒットは処理されません.

## <a name="client-library-sdk-specific"></a>クライアントライブラリ/SDK仕様

各クライアントライブラリは、一度に大量のヒットを送信しないようにレート制限のメカニズムが実装されています.
メカニズムは[トークンバケットアルゴリズム](http://en.wikipedia.org/wiki/Token_bucket)で実装されており、
クライアントからの急な送信を制限しながら、爆発的なヒットをGoogle Analyticsに送信しても大丈夫なようになっています.

各トラッカーは同時に送信可能なリクエスト数の最大値に制限があります.
トラッカーはまた送信された並列なヒットの数を計測して保持しています.
Google Analyticsにヒットが送信されるたびに、
カウントはひとつずつ減っていきます.
カウントが0になった場合、つまり最大制限に到達した場合、新しいリクエストは送信されません.
小さい期間が過ぎた後、カウントは最初の制限値に戻り、
またデータの送信が可能になります.

ここでは、各ライブラリが制限レートをどのように処理しているかを説明しています.
もしこれらの制限のいずれかに到達した場合、
ヒットはGoogle Analyticsのサーバーに送信されず、
データはレポート内で処理されません.


### <a name="gajs"></a>gs.js

各ウェブプロパティは、2秒辺り1ヒットの割合を満たす10ヒットから始まります.
これはイベントタイプのヒットにのみ適用されます.

### <a name="analyticsjs"></a>analytics.js

各ウェブプロパティは、2秒辺り1ヒットの割合を満たす20ヒットから始まります.
これは全てのヒットに適用されます.

### <a name="android-sdk"></a>Android SDK

デバイスの各トラッカーインスタンス、各アプリインスタンスは2秒毎に1ヒットの割合を満たす60ヒットから始まります.
これは全てのヒットに適用されます.

### <a name="ios-sdk"></a>iOS SDK

各プロパティは2秒あたり1ヒットの割合を満たす、60ヒットから始まります.
これは全てのヒットに適用されます.

- - -

他の断りのない限り、このページのコンテンツは [Creative Commons Attribution 3.0](http://creativecommons.org/licenses/by/3.0/) ライセンスの下で提供されています. また、サンプルコードは、[Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0)の下で提供されています. 詳細については、[サイトポリシー](https://developers.google.com/site-policies)を参照してください.

最終更新日 2013年8月16日.