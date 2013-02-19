# Sessions - iOS SDK

- - -

このドキュメントではGoogleモバイルアプリ解析とGoogle Analytics SDK for iOS v2のセッションにおける高レベルの概要と、
アプリ内でセッションを管理するための幾つかのメソッドについて説明しています.

- [オーバービュー](#overview)
- [セッションの管理](#session_management)
    - [自動のセッション管理](#automated_session_management)
    - [手動のセッション管理](#manually_session_management)
    
## <a name="overview"></a>オーバービュー

セッションはアプリにおけるユーザーインタラクションのひとつの期間を表します.
Web解析では「訪問(visit)」が相当します.
訪問と同様に、
セッションはスクリーンビュー、イベントやEコマーストランザクションなどの計測アクティビティの
便利なコンテナーとして機能します.

デフォルトでは、Google Analyticsは,
30分以内に受け取ったヒットを同じセッションとしてグループ化します.
しかしながら多くの開発者は、アプリの状態に対応するような、
例えばアプリがバックグラウンドにあるときにどれくらいの期間かによって、
追加のセッション管理レイヤーを実装したいと思うでしょう。

ドキュメントの以降の部分では、それらのロジックを実装可能なメソッドについて説明します.
開発者は、EasyTrackerを使った完全に自動化されたセッション管理から、
手動での独自のセッション管理まで、あるいはそれらを組み合わせたものなどを
選択して利用することができます.

## <a name="session_management"></a>セッションの管理

以降の解説では、アプリ内でセッションを管理可能にするメソッドについて説明します.

### <a name="automated_session_management"></a>自動のセッション管理

Google Analytics SDK for iOSは自動のセッション管理を提供しています.
自動セッション管理では新しいセッションの開始作業を扱ってくれます.
ここでは自動セッション管理がどのように動作するかの概要を掲載します.

- デフォルトの実装ではセッションのタイムアウト期間は30秒です. タイムアウト期間は setSessionTimeout:(NSTimeInterval): を呼ぶことで変更することができます.

```
[tracker setSessionTimeout:60];
```

- アプリがセッションタイムアウト期間よりも長くバックグラウンドに居続けた場合、次回のヒットは新しいセッションの一部として計測されます.

### <a name="manually_session_management"></a>手動のセッション管理

アプリのライフサイクルにおいて重要なイベントが発生したタイミングで、
手動で新しいセッションを開始することが有効な場合があるかもしれません.

例えば、ユーザーがアプリへのサインインに成功するたびに新しいセッションを開始したいとします.
ユーザーがアプリを利用する意図は(サインインによって)変わっているか、
あるいは完全に異なるユーザーとなっているので、
サインインで新しいセッションを開始することは、
利用データを分離し、レポートを理解するのに役立ちます.

新しいセッションを開始するためには、sessionStart を YESにセットして下さい.
これによって次のヒット送信の際に新しいセッションの開始を示すパラメータが付加されます.

下の例では、ユーザーがアプリ内でサインインした時に onSignIn が呼ばれると仮定しています.

```
// Called after a user successfully signs in to your app.
- (void)onSignIn {
  … // The rest of your onSignIn code.
  tracker.sessionStart = YES;
  [tracker sendEventWithCategory:@"app_flow"
                      withAction:@"sign_in"
                       withLabel:nil
                       withValue:nil]; // First activity of new session
}
```