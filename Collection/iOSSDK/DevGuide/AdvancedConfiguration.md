# Advanced Configuration - iOS SDK

このドキュメントでは、Google Analytics SDK for iOS v2のいくつかの発展的な機能についての概要を説明しています.

- [Overview](#overview)
- [Using Multiple Trackers](#using-multiple-trackers)
    - [Default Tracker](#default-tracker)
- [Sampling](#sampling)
- [App-level Opt Out](#app-level-opt-out)
- [Testing and Debugging](#testing-and-debugging)

## <a name="overview"></a>Overview

Google Analytics SDK for iOS はグローバルな実装状態の管理と、
Google Analyticsサーバへのデータ送信について、
２つのクラスを利用しています

- **GAI** - GAITrackerオブジェクトやアプリレベルのオプトアウト設定や設定ディスパッチなどのアプリの実装状態をハンドルするシングルトンクラスです.
- **GAITracker** - Google Analyticsへデータを送信するクラスです. ユニークなプロパティIDを用いて複数のトラッカーをインスタン化することができます.

## <a name="using-multiple-trackers"></a>Using Multiple Trackers

SDKのバージョン2から、ユニークなトラッキングIDを利用することで、一つの実装の中で複数のトラッカーを利用することができるようになりました。
すべてのトラッカーは GAI シングルトンを通じてグローバルな状態を共有します.

以下の例では、２つのトラッカーを用いて、
異なるプロパティを持ったスクリーンビューが送信されています.
各トラッカーはユニークなプロパティIDを持っています.

```
#import "RootViewController.h"
#import "GAI.h"

@interface RootViewController()

@end

@implementation R
ootViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // Send a screen view to the first property.
    id trakcer1 = [[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-Y"];
    [tracker1 sendView:@"/HomeScreen"];
    
    // Send another screen view to the second property.
    id tracker2 = [[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-Z"];
    [tracker2 sendView:@"Home"];
}

@end
```

スクリーンビューや例外処理などの自動の計測機能は、
Google Analyticsにデータを送信する際に一つのトラッカーのみを利用することを覚えておいてください。
もしこれらの機能を利用していて、かつ他のトラッカーでデータを送信したい場合、
手動で行う必要があります.

参考までに、自動のスクリーン計測では特に GAITrackedViewController の tracker プロパティに設定されているトラッカーを利用します. 例外処理計測では GAI が保持するデフォルトのトラッカーを利用します.

### <a name="default-tracker"></a>Default Tracker 

実装において複数のトラッカーが利用可能なのに対して、
グローバルいは１つのデフォルトトラッカーが存在します.
最初に取得された Tracker がデフォルトトラッカーとなります.

デフォルトトラッカーを取得するには以下のようにします.

```
// Get default tracker.
id myDefault = [GAI sharedInstance].defaultTrakcer;
```

デフォルトトラッカーを設定するには以下のようにします.

```
// Get a new tracker.
id newTracker = [[GAI sharedInstance] trackerWithTrackingId:@"UA-NEW-TRACKING-ID"];

// Set the new tracker as the default tracker, globally.
[GAI sharedInstance].defaultTracker = newTracker;
```

## <a name="sampling"></a>Sampling

Google Analyticsへ送信するヒットの数を制限するために、
クライアントサイドサンプリングを有効にすることができます.
もしアプリが多くのユーザーを抱えているか、
あるいは大量のデータをGoogleAnalyticsへ送信する場合には、
サンプリングの有効化によって邪魔のないレポートを手に入れられます.

例えば、50%の割合でクライアントサイドサンプリングを行う場合には
以下の様なコードを書きます.

```
// Set a sample rate of 50%.
[tracker setSampleRate:50.0]; // Sample rate is a double.
```

> レポートの非一貫性を避けるために, 
> アプリの各プロファイルは同じサンプリングレートでデータを収集してください.
> もし異なるバージョンのアプリで異なるサンプリングレートを利用した場合、
> 異なるバージョンのデータを分割するために
> [プロファイルフィルター](http://support.google.com/analytics/bin/answer.py?hl=en&answer=1033162)からアプリのバージョンをディメンションとして設定してください。

## <a name="app-level-opt-out"></a>App-level Opt Out

アプリ全体を通してGoogle Analyticsを無効化するために、
app-level opt out フラグを利用することができます.
一度セットすると、アプリのライフタイムの間か、
アプリがリセットされるまでフラグは保持され続けます.

app-level opt outの設定を取得するには以下のようにします.

```
// Get the app-level opt out preference.
if([GAI sharedInstance].optOut)
    … // Alert the user they have opted out.
}
```

app-level opt outを設定するには以下のようにします.

```
// Set the app-level opt out preference.
[GAI sharedInstance].setOptOut = YES;
```

## <a name="testing-and-debugging"></a>Testing and Debugging

Google Analytics SDK for iOS ではデバッグモードを用意してあります.
デバッグモードでは、どんなデータがGoogle Analyticsに送信されるのかをログに
出力するなど有効な情報を表示します.

```
// Enable debug mode.
[GAI sharedInstance].debug = YES;
```
