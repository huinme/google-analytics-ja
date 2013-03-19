# クラッシュおよび例外 - iOS SDK

Source : [https://developers.google.com/analytics/devguides/collection/ios/v2/exceptions?hl=ja](https://developers.google.com/analytics/devguides/collection/ios/v2/exceptions?hl=ja)

このドキュメントではGoogle Analytics SDK for iOS v2を利用したクラッシュおよび例外の測定について高レベルの概要を提供しています.

- [概要](#overview)
- [キャッチされる例外](#caught-exceptions)
- [キャッチされない例外](#uncaught-exceptions)

## <a name="overview"></a>概要

クラッシュおよび例外の計測によって、
アプリで発生したクラッシュおよび例外の回数およびタイプについて計測することが可能になります.
Google Analyticsでは例外は以下の要素で構成されています.

- NSString (Optional) Description - 例外の説明で最大100文字です. nilを設定することも可能です.
- boolean isFatal - 発生した例外が致命的なものかどうかを示します. YESであれば致命的な例外となります.

クラッシュおよび例外のデータは、
主に"クラッシュおよび例外"レポートから確認することができます.

## <a name="caught-exceptions"></a>キャッチされる例外

キャッチされる例外(Caught Exceptions)とは、アプリの中で例外の処理を事前に定義してあるエラーを指します.
これらは、一般的にはアプリの通常の利用中に発生する予期されたエラーです.
またデータのリクエスト中に度々発生するネットワークのタイムアウトのように、
回復可能でもあります.

これらの例外は catch ブロックの中で sendException: メソッドを呼び出すことで計測することが可能です.

以下の例では、クラウド上からハイスコアのリストをロードしようとしています.
もし、おそらくはネットワークコネクションが低速なせいで、リクエストがタイムアウトになった場合、
ユーザーに対してエラー処理を行う前に、Google Analyticsのサーバーに例外を送信することができます.

```
@try {
    NSArray *highScores = [self getHighScores];
}
@catch (NSException *exception) {
    [tracker sendException:NO // Boolean indicates non-fatal exception.
           withDescription:@"Connection timeout %d: %@", connectionError, errorDescription];
}
```

sendException:withDescription:withNSException: (※おそらくsendException:withNSException:の誤り)を利用して、
例外の名前および理由を使ってdescriptionsの部分を自動で埋めることも可能です.

```
@try {
    NSArray *highScores = [self getHighScores];
}
@catch (NSException *exception) {
    [tracker sendException:NO withNSException:exception];
}
```

また、sendException:withDescription:withNSError: (※おそらくsendException:withNSError:の誤り)を利用して、
エラーのドメイン、コード、descriptionを使って自動で送信することも可能です.

```  
NSError *error = nil;
if (![self updateHighScoresWithError:&error]) {
    [tracker sendException:NO withNSError:error];
}
```

## <a name="uncaught-exceptions"><a/>キャッチされない例外

キャッチされない例外(Uncaught Exceptions)とはアプリが実行中に遭遇した予期せぬ状況を表しており、
多くの場合致命的な状況でありクラッシュを引き起こします.
キャッチされない例外は sendUncaughtExceptions プロパティをYESに設定することで
自動的にGoogle Analyticsに送信することが可能です.
この設定はAppDelegateクラスの application:didFinishLaunchingWithOptions: メソッド内で簡単に設定できます.

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
    [GAI sharedInstance].sendUncaughtExceptions = YES; // Enable
    
    // … the rest of your code, include other GAI properties you want to set.
} 
```

自動の例外計測を利用する場合には、以下のことに注意して下さい.

- 自動の例外計測を利用して送信される例外は、Google Analyticsではすべて致命的なものとしてレポートされます.
- descriptionフィールドは、自動でスタックトレースを利用して埋められます.

- - -

Last updated 1月9日, 2013.