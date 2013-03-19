# Screen Tracking - iOS SDK

Source : [https://developers.google.com/analytics/devguides/collection/ios/v2/screens?hl=ja](https://developers.google.com/analytics/devguides/collection/ios/v2/screens?hl=ja)

- - -

このドキュメントではスクリーン計測の概要と、Google Analytics SDK for iOS v2を利用してどのようにスクリーンビューを計測するかについて説明しています.

- [オーバービュー](#overview)
- [実装](#implementation)
- [自動のスクリーン計測](#automatic-screen-measurement)
- [手動のスクリーン計測](#manual-screen-measurement)
    
## <a name="overview"></a>オーバービュー

Google Analyticsにおけるスクリーンは、ユーザーがアプリ内で見たコンテンツについて表しています.
Web解析における同様のコンセプトはページビューです.
スクリーンビューの計測によって、どのコンテンツがユーザーに最も見られているかを知り、
また異なるコンテンツ間をどのように遷移しているのかを知ることができるようになります.

スクリーンビューは、１つの文字列によるフィールドで構成されています.
このフィールドはGoogle Analytics レポートでスクリーン名として利用されます.

スクリーンビューのデータは主に、以下のレポートで利用されます.

- スクリーン レポート
- エンゲージメントフロー
- ゴールフロー

## <a name="implementation"></a>実装

以降の章では手動および自動でのスクリーン計測をどのように実装するかについて説明します.
自動スクリーン計測の利用によって、アプリ内の全てのビューにわたってスクリーン計測を素早く実装することができます.
また、手動のスクリーン計測では、追加のスクリーンビューをGoogle Analyticsに送信したい場合に利用することができます.

## <a name="automatic-screen-measurement"></a>自動のスクリーン計測

GAITrackedViewControllerクラスを利用することで、自動でスクリーンビューを計測することができます.
各ビューコントローラをGAITrackedViewControllerの継承として作成して下さい。
このクラスはUIViewControllerを拡張した便利なクラスであり、
レポート内で各ビューコントローラのスクリーン名を利用可能にします.

例えば、"About"ビューの計測をしたい場合、ビューコントローラのヘッダーは以下のようになります.

```
@interface AboutViewController : UIViewController
```

これを以下のように変更して下さい.

```
#import "GAITrackedViewController.h"

@interface AboutViewController : GAITrackedViewController
```

さらにGoogle Analyticsレポートで利用するために名前を設定する必要があります.
設定に適した場所は、もしあればビューコントローラの初期化メソッドの中になります。
あるいは viewDidLoad メソッドで

```
- (void)viewDidLoad{
    [super viewDidLoad];
    self.trackedViewName = @"About Screen";
}
```

と記述してください.
sendView: メソッドが呼ばれる前に trackedViewName が設定される限り、自動のスクリーン計測が発生します.
ビューが表示されるたびに、 スクリーン名を伴ってsendView: の呼び出しが行われます.

## <a name="manual-screen-measurement"></a>手動のスクリーン計測

手動でスクリーンビューを送信したい場合には、 以下の例のように sendView: を呼んで下さい.

```
[tracker sendView:@"Home Screen"];
```

- - -

Last updated 1月 9, 2013.