# Campaign Tracking - iOS SDK

Source : [https://developers.google.com/analytics/devguides/collection/ios/v2/campaigns?hl=ja](https://developers.google.com/analytics/devguides/collection/ios/v2/campaigns?hl=ja)

このドキュメントでは、Google Analytics SDK for iOS v2を利用してキャンペーンや流入元の計測を行う手段について説明します.

- [概要](#overview)
- [一般的なキャンペーンの計測](#measuring-general-campaigns)
- [紹介の計測](#measuring-referrals)
- [既知の問題](#known-issues)
- [キャンペーンパラメータ](#campaign-parameters)

## <a name="overview"></a>概要

Google Analyticsでキャンペーンの計測を行うことによって、
アプリのユーザーが何処から来たのかを計測することが可能になります.
ユーザーの流入元を理解することによって、
アプリ内でユーザーのアクティビティに対してキャンペーンや流入元の属性を付加して
マーケティングチャンネルの価値を向上させる手助けになります.

Google Analytics SDK for iOSではいくつかの種類のキャンペーン計測方法が用意されています.

- [一般的なキャンペーンの計測](#measuring-general-campaigns) - インストール後起動されたアプリがどのキャンペーンや流入元からのものなのかを計測します.
- [紹介の計測](#measuring-referrals) - インストール後起動されたアプリを紹介したウェブサイトや他のアプリなどを計測します.

以降では、各タイプのキャンペーン計測手段を、いつ、どのように実装すればいいかについて説明します.

>**Note:** AppleのAppStoreは現在Google Analyticsを利用したキャンペーン計測をサポートしていません.[詳細はこちら](https://developers.google.com/analytics/devguides/collection/ios/v2/campaigns?hl=ja#known-issues)

## <a name="measuring-general-campaigns"></a>一般的なキャンペーンの計測

一般的なキャンペーンの計測は、アプリを既にインストールしているユーザーとキャンペーンあるいは流入元の関連付けをするのに利用できます.

例えば、既にアプリをインストールしている既存ユーザーに対してリーチするために、
有料のキャンペーンを行なっていたとします.
キャンペーンの結果としてどのアプリが起動されたのかを計測するために、
あなたは一般的なキャンペーンの計測を利用することができます.

### 一般的なキャンペーン計測の実装

一般的なキャンペーンの値をセットするためには、setCampaignUrl:campaignUrl メソッドを呼び出して下さい.
ここで campaignUrl は有効な [Campaign Parameter](https://developers.google.com/analytics/devguides/collection/ios/v2/campaigns?hl=ja#campaign-params) 文字列を指します.

もしあなたのアプリがカスタムURLスキームを実装しているならば、
一般的なキャンペーンの計測は有効な手段になります.
たとえば、もしアプリがURLへの応答として起動したのであれば、
アプリはGoogle Analyticsで情報を保存するために、
クエリパラメータを取得しsetCampaignUrlに渡すことができます.
その後のユーザーアクティビティは、
URLに含まれているキャンペーンパラメータ
から取得した情報と関連付けることができます.

## <a name="measuring-referrals"></a>紹介の計測

紹介の計測は、
キャンペーンパラメータではなく単純な"google.com"や"myOtherApp"といった文字列を利用する点を除けば、
ユーザーの端末上でアプリを起動した流入元を計測する他のタイプのキャンペーン計測と似ています.

"google.com"の様な紹介元をセットすると、
ソースディメンションは"google.com"に設定されます.
中間のディメンションは暗黙的に"referrer"にセットされます.

キャンペーンの計測と同様に、紹介元を設定することによって、
デフォルトでは、次回の呼び出しから新しいセッションが開始されます.

リファラーをセットするには、setReferrerUrl:referrer を呼び出して下さい.
ここで referrer は"google.com"や"myOtherApp"としった文字列を指定します.

## <a name="known-issues"></a>既知の問題

AppleのAppStoreは現在Google Analyticsを利用したキャンペーンの計測に対応していません.
しかし、カスタムURLスキームは一般的なキャンペーン計測において
アプリがダウンロード、インストールされた後にキャンペーン情報をアプリに伝えるのに利用できます.

## <a name="campaign-parameters"></a>キャンペーンパラメータ

キャンペーンパラメータはトラフィックの流入元やキャンペーンの情報をアプリに渡すのに利用されます.

- 一般的なキャンペーンの計測では、エンコードされていないキャンペーンパラメータの文字列がsetCampaignUrl:の引数として渡されます.

以下の、有効かつエンコードされていないキャンペーン文字列の例は、一般的なキャンペーンの計測で利用できます.

```
"utm_campaign=my_campaign&utm_source=google&utm_medium=cpc&utm_term=my_keyword&utm_content=ad_variation1"
```

以下の表では、一般的なキャンペーンの計測において、キャンペーンパラメータとして利用可能なパラメータを列挙しています.

Parameter | Description | Example(s)
--- | --- | ---
urm_campaign | キャンペーン名 : あるプロダクトのプロモーションや戦略的なキャンペーンを特定するキーワード解析に利用されます. | utm_campaign=spring_sale
utm_source | キャンペーンソース : 検索エンジン、ニュースレターや他の流入元などを特定するのに利用されます. | utm_source=google
utm_medium | Campaign Medium : emailやクリック単価(cpc)などの媒体を特定するのに利用されます. | utm_medium=cpc
utm_term | キャンペーン用語 : キーワード広告を提供するための有料検索と共に利用されます. | utm_term=running+shoes
utm_content | キャンペーンコンテンツ : A/Bテストやコンテンツターゲット広告など同じURLで異なるリンクや広告のために利用されます. | utm_content=logolink, utm_content=textlink
gclid | [AdWordsの自動タギングパラメータ](http://support.google.com/analytics/bin/answer.py?hl=en&answer=1033981) : Google AdWords広告の計測に利用されます.この値は動的に生成され、変更してはいけません. | 

- - - 

Last Updated 1月 9日, 2013.