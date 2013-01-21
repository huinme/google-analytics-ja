# Google Analytics SDK for iOS v2 (Beta) - Overview

Source : https://developers.google.com/analytics/devguides/collection/ios/v2/

Google Analytics SDK for iOSはアプリからユーザーエンゲージメントデータを集める作業を簡単にします.
このドキュメントではシングルプロパティIDとEasyTrackerを用いて、アプリの計測を始めるガイドと、SDKに関する概要を提供します.

## Menu

- Introduction
- Before You
- Begin
- Getting Started
    1. Adding headers and libraries
    2. Initializing the tracker
    3. Implementing screen measurement
- Next steps

## Introduction

Google Analytics SDK for iOSはアプリからユーザーエンゲージメントデータを集める作業を簡単にします.
開発者は以下の指標を計測するためにGoogle Analayticsのレポートを利用できます.

- アプリケーションを利用しているアクティブユーザー数
- アプリケーションをどこから来て利用しているのか
- 特定の機能の利用率
- アプリ内購入とトランザクション
- アプリケーションのクラッシュ数と原因
- その他の有用な指標

## Before you Begin

SDKを導入する前に、必ず以下を用意してください.

- iOS Developer SDK 4.0 or later
- Google Analytics for Mobile Apps iOS SDK v2
- Google Analyticsを導入するiOSアプリ
- Google Analyticsの新しいプロパティとプロファイル

## Getting Started

SDKを利用し始めるためには以下の３ステップが必要になります.

1. Add headers and libraries to your project
2. Initialize the tracker
3. Add screen measurement

上記のステップを実行すると、Google Analytcsで以下のデータを計測することができるようになります.

- アプリのインストール数
- アクティブユーザとそのデモグラフィック
- スクリーンとユーザーエンゲージメント
- クラッシュと例外

### 1. Adding header files and configuring your project

Google Analytics for iOS SDKをダウンロードして、アプリの以下のファイルを追加してください.

- GAI.h
- GAITracker.h
- GAITrackedViewController.h
- GAITransaction.h
- GAITransactionItem.h
- libGoogleAnalytics.a

Google Analtyics SDKは CoreData.framework と SystemConfiguration.framework を利用しているため、
以下のライブラリをターゲットの"linked libraries"に追加してください.

- libGoogleAnalytics.a
- CoreData.framework
- SystemConfiguration.framework

**すでに CoreData.framework を利用している場合**

Google AnalyticsのCoreDataオブジェクトが発行する通知(例えばNSManagedObjectContextDidSaveNotificationなど)に応答すると、例外を引き起こします.
そのため、リスナーオブジェクトに特定のmanaged object contextをパラメータとして設定し、CoreDataの通知をフィルタリングすることをAppleは勧めています.

### 2. Initializing the tracker

トラッカーを初期化するために、"GAI.h"ヘッダーファイルをアプリケーションデリゲートファイルに追加し、appliction:DidFinishLaunchingWithOptions: メソッドの中に追加してください.

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      // Optional: automatically send uncaught exceptions to Google Analytics.
      [GAI sharedInstance].trackUncaughtExceptions = YES;
      // Optional: set Google Analytics dispatch interval to e.g. 20 seconds.
      [GAI sharedInstance].dispatchInterval = 20;
      // Optional: set debug to YES for extra debugging information.
      [GAI sharedInstance].debug = YES;
      // Create tracker instance.
      id<GAITracker> tracker = [[GAI sharedInstance] trackerWithTrackingId:@"UA-YOUR-TRACKING-ID"];
    
    }

**Note:**

特定のトラッキングIDによってトラッカーを得た後、トラッカーのインスタンスはライブラリの中で永続化されます.
その後、同じトラッキングIDを利用して trackerWithTrackingId: メソッドを呼ぶと、同じトラッカーインスタンスが返ってきます.
Google Analytics SDKは最初に作成されたトラッカーインスタンスをデフォルトトラッカーとして利用します.
デフォルトトラッカーは以下のようにしてアクセスできます.

    id<GAITracker> tracker = [[GAI sharedInstance] defaultTracker];
    
上記の例の"UA-YOUR-TRACKING-ID"はあなたが作成したGoogle Analytics 
アプリプロファイルにアサインされたトラッキングIDを入れるためのプレースホルダーです.
もしアプリの中でトラッキングIDを１つだけ利用しているのであれば、デフォルトトラッカーを利用するのが最適です.

### 3. Implementing screen measurement

アプリのビューを自動的に計測するために、ビューコントローラはGAITrackedViewControllerを拡張するようにしてください.
GAITrackedViewControllerはUIViewControllerを拡張したクラスで、各ビューコントローラのビュー名をレポートに追加します.
ビューがロードされるたびに、スクリーンのビューはGoogle Analyticsに送信されます.

例えば、"About"という名前のビューがあり、以下の様なヘッダーを持ったビューコントローラを計測したいと考えているとします.

    @interface AboutViewController : UIViewController

このヘッダーを以下のように変更してください.

    #import "GAITrackedViewController.h"
    
    @interface AboutViewController : GAITrackedViewCOntrolelr
    
また、Google Analyticsのリポートで利用されるビューの名前を必ず設定してください. 
設定をおこなうのに適した場所はビューコントローラの初期化メソッドです。
もし viewDidLoad: メソッドがるなら以下のようにしてください.

    - (void)viewDidLoad {
      [supser viewDidLoad];
      self.trackedViewname = @"About Screen";
    }
    
sendView: メソッドが呼ばれる前に trackedViewName がセットされさえすれば、自動のスクリーン計測は行われます.
ビューが表示されるたびに、設定されたビュー名を用いたsendView:メソッドの呼び出しが生成されます.

スクリーン計測についてより詳しく学ぶためには、Screens Developer Guideを参照して下さい.

おめでとうございます！Google Analyticsにデータを送信するためにセットアップが完了しました.

## Next steps

Google Analyticsでは、キャンペーン、アプリ内購入やトランザクション、ユーザーインターフェースイベントの計測など、
さらに多くのことができます. これらの機能を実装し利用するためには以下の記事を参照してください.

- Advanced Configuration : 複数トラッカーの利用を含む、拡張設定オプションについて学べます.
- Measuring Campaigns : どのチャネルやキャンペーンがアプリのインストールを向上させたかを調査するためのキャンペーン計測について学べます.
- Measuring Events : ボタン、ビデオなどイベントを利用しているインタラクティブなコンテンツとユーザーのエンゲージメントをどのように計測すればいいかを学べます.
- Measuring In-App Payments : アプリ内購入と支払いの計測方法について学べます.
- User timings : ロード時間やメディアとのエンゲージメントなど、ユーザー時間の計測について学べます.
