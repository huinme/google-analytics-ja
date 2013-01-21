# Custom Dimensions & Metrics - Feature Reference

Source : [https://developers.google.com/analytics/devguides/platform/features/customdimsmets](https://developers.google.com/analytics/devguides/platform/features/customdimsmets)

## Contents

- Overview
- Understanding Custom Dimensions and Metrics
    - Configuration
    - Collection
        - Scope and Precedence
    - Processing
    - Reporting
    - Examples
        - Hit-scoped dimension
        - Session-scoped dimension
        - User-scoped dimension
        - Custom metric
    - Implementation
    - Considerations
    - Available Libraries
    
## Overview

カスタムディメンションおよびメトリックは、どれだけの数のユーザーがあなたのコンテンツとインタラクションを行なっているかに関して、
新しい疑問に答える手助けとなる追加データをGoogle Analyticsにもたらすことを可能にします.

#### What are dimensions and metrics?

メトリックは、あるデータタイプの総数で、例えばページビューがあります。
メトリックはGoogle Analyticsのヒット全体をまたいで集計されます。
メトリックはレポートページのカラムに該当します。
ディメンションはメトリックをある特定の値によってブレイクダウンするもので、
例えばスクリーン名あたりのページビューがあります。
ディメンションはレポートページのロウ（行）に該当します。

Google Analyticsには200を超えるディメンションとメトリックが用意されています。
それらによって、例えばページビューが各ディメンションにわたってどう分散しているか、
といったことを調べることを可能にします.

カスタムディメンションとメトリックを利用することで、
どれだけのユーザーがコンテンツとインタラクションを行なっているかの分析を補強してくれる、
独自のディメンションとメトリックを定義することができます。

#### Why are custom dimensions and metrics userful?

カスタムディメンションおよびメトリックは、
たとえばCRMシステムのようにGoogle Analyticsの外で利用するかもしれないデータを利用できるようにし、
またあなたのコンテンツあるいは事業にかんする疑問に答えるためのレポートを組み上げるための
基礎となるデータをもたらしてくれます.

- もしCRMシステムにおける登録ユーザの性別を保存するのであれば、Google Analyticsのデータと、性別によるページビューを組み合わせることになるでしょう。日ステムディメンションを利用することで、登録ユーザーの性別情報をプラットフォームへと送ることができるようになります。また、どれだけの異なるタイプのユーザーがあなたのコンテンツを敬遠しているのかを調べるために、ページビューを性別によってわけたレポートを作成することが可能になります.
- もしあなたがゲーム開発者なのであれば、レベル別の到達度やハイスコアといったメトリクスは、予め用意されているページビューや平均滞在時間といったメトリクスよりもあなたにとって適したものになるでしょう。カスタムメトリックを利用してこれらのデータを追跡することで、柔軟かつ読みやすいカスタムリポートの中にある最重要なメトリクスと照らしあわせて進捗を追跡することができます.

#### How are custom dimensions different from custom variables?

カスタムディメンションは機能的には[カスタム変数](https://developers.google.com/analytics/devguides/collection/gajs/gaTrackingCustomVariables)ととても似ています.
どちらの機能も、あなたが定義したスコープ上にあるヒットに対して値を適用することで、
新しい方法でデータをグルーピングすることが可能になります。

これらの基本的な差は、カスタムディメンションはサーバサイドで管理されるのに対し、カスタム変数はクライアントサイドで管理される点にあります.
このことはいくつかの事柄を意味します.

- 各ヒットにおいて送信するデータ量が減ります.カスタムディメンションの名前とスコープはプロパティ設定で定義されているので、インデックスと値のみ送信するだけですむためです。
- カスタムディメンションの定義は(カスタム変数に比べて)より柔軟です.名前とスコープはアプリケーションのコードを変更することなくプロパティ設定から編集することができます.[ここ](https://developers.google.com/analytics/devguides/platform/features/customdimsmets#changing-values)からカスタムディメンションの定義を編集することがレポートにどう影響を与えるかについて詳しくしることができます.
- 各プロパティでは20のカスタムディメンションインデックスが利用可能です.対してカスタム変数はクライアントサイドで管理されプロパティあたり５つのスロットしかもっていません.

現在、カスタムディメンションおよびメトリックはGoogle Analytics SDKs or AndroidおよびGoogle Analytics SDKs or iOSのv2.xでサポートされています.
古いバージョン(v1.x)及びNative App SDKsはjavascriptのga.jsライブラリと同様にカスタム変数をサポートしています.

## Understanding Custom Dimensions and Metrics

カスタムディメンションとメトリックのライフサイクルには４つのステージがあります.

- Configuration : インデックス、名前、スコープと合わせてカスタムディメンションとメトリックを定義します.
- Collection : 実装コードからGoogle Analyticsに向けてカスタムディメンションとメトリックを送信します.
- Processing : カスタムディメンションやメトリック、あるいはプロファイルフィルターを利用して、データを処理します.
- Reporting : Core Reporting APIもしくはWebインターフェイスからカスタムディメンションおよびメトリックを利用して新しいレポートを作成します.

### Configuration

カスタムディメンションとメトリックをGoogle Analyticsに送信する前に、Google Analyticsのプロパティからこれらを定義する必要があります.
Google Analyticsの各プロパティは20個のカスタムディメンションおよび20個のカスタムメトリックのインデックスが利用可能です.

カスタムディメンションあるいはメトリックを定義する際には、
あるインデックスに対して、以下の値を指定しなければなりません.

- Name : カスタムディメンションあるいはメトリックの名前で、レポートに表示されます.
- Scope : (カスタムディメンションのみ)どのヒットがカスタムディメンションに適用されるかを指定してください.詳細はScopeの項目を参照してください.
- State : カスタムディメンションとメトリックの値が処理されるかどうか(=有効か無効か)を決定します.インアクティブなカスタムディメンションおよびメトリックはなおレポートに表示されますが、それらの値は処理されません.

カスタムディメンションおよびメトリックはGoogle AnalyticsのWebインターフェイスから設定できます. 詳細は[こちら](http://support.google.com/analytics/bin/answer.py?answer=2709829)を参照してください.

一度カスタムディメンションあるいはメトリックを定義すると、利用可能なときに名前やスコープを編集することは避けてください.
変更がレポートにどのような影響をあたえるかは[実装上の注意](https://developers.google.com/analytics/devguides/platform/features/customdimsmets#implementation-considerations)を参照してください.


### Collection

カスタムディメンションおよびメトリックの値はインデックスと値のペアとして集計のタイミングでGoogle Analyticsに送信されます.
インデックスパラメータは、Configurationで定義したカスタムディメンションあるいはメトリックのインデックスに対応します.

他のタイプのデータとは異なり、カスタムディメンションおよびメトリックは他のヒット(たとえばページビュー、イベント、Eコマーストランザクション)に付加されたパラメータとしてGoogleAnalyticsに送信されます.
トラッキングのコールの前にセットされる必要があるカスタムディメンションあるいはメトリックの値は、その値がGoogle Analyticsに送信されるために付加されます.

例えば、Androidアプリでスクリーンビューにカスタムディメンションを負荷するためには、コードはこのようになるはずです.

    Tracker tracker = EasyTracker.getTracker();
    tracker.setCustomDimension(1, "Level 1"); // Set the custom dimension value.
    tracker.trackView(); // Send the custom dimension value with a screen view hit.

### Processing

カスタムディメンションが処理されるとき、スコープは、どのヒットがカスタムディメンションの値として適用されるかを決定します.
一方で、プロファイルフィルターはどのヒットとそれらに紐付けられた値が最終的にレポートに含まれるかを決定します.

#### Scope and Precedence

スコープ(Scope)はあるヒットが特定のカスタムディメンションの値と関連付けられるかどうかを決定します.
スコープにはhit, session, userの３つの種類があります.

- Hit : 値は関連付けられた一つのヒットに適用されます.
- Session : 値は一つのセッションの中のすべてのヒットに対して適用されます.
- User : 値は、その値が変更されるかカスタムディメンションが無効になるまで、現在及び将来のすべてのセッションについて適用されます. 

**Hit-level scope**

カスタムディメンションがヒットレベルのスコープを持っている場合、その値は、値がセットされたヒットにのみ適用されます.下のFigure A, Figure B, Figure Cはその説明です.

![figure of Hit-level scope](https://developers.google.com/analytics/images/customdim_hit.png)

**Session-level scope**

セッションスコープを持っている２つの値が、あるセッションにおいて同じインデックスにセットされている場合、最新の値が優先され、そのセッションのすべてのヒットに対して適用されます. 下のFigure Dでは、最新の値セットがそれまでのすべての同じインデックスの値を上書きしています.

![Figure of Session-level scope](https://developers.google.com/analytics/images/customdim_session.png)

**User-level scope**

最後に、もし同じセッションの中で、２つのユーザーレベルスコープを持ったカスタムディメンションの値がセットされる場合、最新の値が優先され、そのユーザーの現在のセッションおよび将来のセッションについてその値は適用されます.

下のFigure Bでは、CD(Custom Dimension)の値Aが、ちょうどセッションレベルスコープのようにセッション2の全てのヒットについて適用されています.
しかしながら、Figure C ではセッションレベルスコープとは異なり、CDの値Aは、3番目のセッション

#### Filters

プロファイルのフィルターはカスタムディメンションとメトリクスをいくつかの方法で組み合わせることができます.

カスタムディメンションおよびメトリックの値は、スコープを考えない場合、それぞれ各ヒットと関連付けられています.
もしそのヒットがプロファイルフィルターによってフィルターされた場合、
カスタムディメンションおよびメトリックもまたそのスコープに依存してフィルタリングされます.

1. Hit-scope : カスタムディメンションおよびメトリックのどちらも、それが関連付けられたヒットがフィルター場合には同じくフィルターされます.
2. Session or User-scope : セッションもしくはユーザースコープのカステムディメンションは、もし関連付けられたヒットがフィルターされたとしても、同じくフィルターされることはありません. それらの値は現在のセッションの全てのヒットについて適用されており、もしスコープがユーザースコープであれば、将来のセッションについても同様に適用されます.

カスタムディメンションはプロファイルフィルターを構築するためにも利用できます.
これにより、カスタムディメンションのスコープに基づいてヒットをフィルターすることができるようになります.
例えば、ユーザースコープを持つカスタムディメンションの値にフィルターをかけることで、
ディメンションの値と関連付けられたユーザーの集合から現在と将来のセッションにフィルターをかけることができます.

### Reporting

collection ,configurationおよびその他のパイプラインステージが完了した後は、
カスタムディメンションおよびメトリックはWebリポーティングインターフェイスあるいはCore Reporting APIを通じて利用可能になります.

カスタムディメンションおよびメトリックは、カスタムレポートとアドバンスドユーザーセグメントで利用できます.
カスタムディメンションはまた、標準レポートのセカンダリディメンションとして利用できます.

## Examples

ここから先の例では、モバイルゲーム開発者がプレイヤーの振る舞いを学ぶために、
カスタムディメンションおよびメトリックをどのように利用することができるのかを説明します.

ゲーム開発者は最近新しいゲームをアンドロイド向けにリリースしました.

現在のGoogle Analyticsの実装はユーザーがあるレベルをプレイする毎のスクリーンビュー(ページビュー)をトラックします.
開発者はすでに、各レベルがどれだけプレイされているかを知っています.
今回、彼らは以下の追加の疑問について知りたいと考えました.

1. ミディアムおよびハードレベルにたいして、イージーレベルは何回プレイされているのか？
2. 3-dayトライアルの期間中、各レベルは何回プレイされているのか？
3. 有料課金をしたユーザーと比べてトライアル中は各レベル何回プレイされているのか？

これらの疑問に答えるために、
ヒット、セッション、ユーザーをグルーピングするためにカスタムディメンションを利用することにしました.

### Hit-level scope

各難易度(easy, medium, hard)の各レベルが何回遊ばれたかを知るために、
開発者がヒットレベルスコープのカスタムディメンションをどのように利用できるかの例を見てみます.

開発者は、各レベルが何回遊ばれたかの計測はスクリーンビュー(ページビュー)を利用して計測しているとします.
今、彼らはどの難易度が最も遊ばれたかを知りたいと考えています.

リポートは以下のようになります.

Difficulty | Screen Views
--- | ---
easy | 
medium | 
hard | 

カスタムディメンションを使う前は、
レベル別でのの合計のスクリーンビューを見ることはできましたが、
それらを難易度でグループ分けすることはできませんでした.

ヒットレベルスコープのカスタムディメンションを利用することで、
難易度は各スクリーンビューと関連付けることができるため、
レポートには最も遊ばれた難易度を含めることができます.

**Why hit-level scope?**

1人のユーザーはひとつのセッションのなかで、いくつかのレベルをプレイします.
ヒットレベルスコープをりようする意味は、
難易度の値はそれが送信されたスクリーンビューとのみ関連付けられるからです。
これによってあるレベルの各スクリーンビューは固有の難易度と関連付けることができます

**Configuration**

カスタムディメンションを実装する最初のステップは、
Webインターフェイスの管理者ページにあるプロパティ設定からカスタムディメンションを定義することです.
例えば、カスタムディメンションは以下のようになります.

Index | 1
--- | ---
Name | Difficulty
Scope | Hit
State | Active

**Collection**

ゲームの中で、開発者は既にスクリーンビューの各レベルを計測しています.
各レベルと難易度を関連付けるために、スクリーンビューのトラッキングを呼ぶ前に、
カスタムディメンションの値はセットされなければなりません.

Androidでは、実装は以下のようになります.

    Tracker.tracker = EasyTracker.getInstance(); // The Google Analytics tracker.
    tracker.setCustomDimension(1, "easy"); // Set the custom dimension value.
    tracker.trackView("/level_1/"); // Custom dimension value is setn with this hit.
    
この例では、カスタムディメンションはレベルのスクリーンビュートラックの前にのみセットされています.
これによって、難易度をカスタムディメンションを関連付け、
レポートの中でスクリーンビューのヒットを難易度でグループ分けすることを可能にします.

**Processing**

ヒットが集計されGoogle Analyticsに送られた後、
データは処理され、カスタムディメンションの値は、
そのスコープに合わせて各ヒットに適用されます.

例えば、1人のプレイヤーについて集められたデータ(1つのセッションで6つのレベルを遊んでいます)は以下のようになります.

    visitorId = 5555
    Session 1:
    H1: screen_name=/level_1/ cd1_value=easy
    H2: screen_name=/level_2/ cd1_value=medium
    H3: screen_name=/level_3/ cd1_value=difficult
    H4: screen_name=/level_4/ cd1_value=easy
    H5: screen_name=/level_5/ cd1_value=medium
    H6: screen_name=/level_6/ cd1_value=medium
    
ヒットレベルスコープを利用することで、
各難易度の値はそれが送信されたスクリーンビューのヒットとのみ関連付けられることを覚えておいてください.

**Reporting**

一度処理されると、各スクリーンビューはそれぞれの難易度の値を関連付けられているので、
開発者はスクリーン名と難易度をディメンションとして、ビュー数をメトリックとして利用したレポートを作成することができます.

Screen Name | Difficulty | Screen views
--- | --- | ---
/level_1/ | easy | 1
/level_2/ | medium | 1
/level_3/ | hard | 1
/level_4/ | easy | 1
/level_5/ | medium | 1
/level_6/ | medium | 1

カスタムレポートでは、各難易度がどれだけ遊ばれているかを見るために、
スクリーンビューを難易度でグループ分けしたものを作成できます.

Difficulty | Screen views
--- | ---
easy | 2
medium | 3
hard | 1

このレポートでは、難易度mediumが最もプレイされています.
この調査は、スクリーンビューをグループ分けするために
ヒットレベルのカスタムディメンションを利用したことで可能になったのです.

### Session-scoped dimension

ここでは、3-dayトライアルの各日に各レベルがどれだけプレイされているかを見るために、
セッションレベルスコープのカスタムディメンションがどのように利用できるかをみてみます.

開発者は、各レベルのスクリーンビュー数の計測によって各レベルがどれだけプレイされているかを知っています.
今、彼らは各日に各レベルがどれだけ遊ばれているかを知りたいと考えています.

開発者が作りたいレポートは以下のようになります.

Day of Tria | Screen Views
--- | ---
Day 1 | 
Day 2 | 
Day 3 | 

セッションレベルのカスタムディメンションを利用することで、
開発者はスクリーンビュー数をトライアルの各日でグループ化し、
ユーザーが無料トライアル中に過ごす時間がどのように変化するのかを知ることができます.

**Why session-level scope?**

セッション全体およびそれに含まれるヒットを効率的にまとめるためにセッションレベルのスコープは利用することができます.

同じ機能を得るために、ヒットレベルのスコープも利用することができますが、
セッションレベルのスコープはトライアルの何日目かを表す値を少ない量のコードを追加することで
簡単にセットすることができるます.

**Configuration*+

The Day of Trialのカスタムディメンションは、Webインターフェイスのプロパティ設定セクションから以下のように定義できます.

Index | 2
Name | Day of Trial
Scope | Session
State | Active

**Collection**

ゲーム中では、開発者は既にスクリーンビューと一緒にレベルを計測しています.
あるセッションの中の全てのスクリーンビュー数と日を関連付けるために、
カスタムディメンションの値は１度のセッションで一度セットすればすみます.

Androidでは、開発者はゲームのスタート時にカスタムディメンションをセットすれば良いのです.

    Tracker tracker = EasyTracker.getInstance(); // The Google Analytics tracker.
    String day = getDayOfTrial(); // Get the day of the trial
    tracker.setCustomDimension(2, day); // Set the custom dimension value.
    tracker.trackView("/home_screen/"); // Custom dimension value is sent with this hit.

セッションレベルのカスタムディメンションは１度のセッション中にいつでもセットすることができることを覚えておいて下さい.
しかしこの例では、セッションの開始時にトライアル何日目かを決定しセットするのが、開発者にとって簡単で便利です.

**Processing**

ヒットが集計されGoogle Analyticsに送信された後、
データは処理されカステムディメンションの値はそのスコープに合わせてヒットに適用されます.

例えば、１日目に２回、２日目に１回、３日目に１回だけゲームをプレイした１人のユーザーのデータは以下のようになります.

    visitorId = 5555
    Session 1:
    H1: screen_name=/level_1/  cd2_value=1
    H2: screen_name=/level_2/
    H3: screen_name=/level_2/
    
    Session 2:
    H4: screen_name=/level_3/  cd2_value=1
    H5: screen_name=/level_4/
    H6: screen_name=/level_4/
    
    Session 3:
    H1: screen_name=/level_1/  cd2_value=2
    H2: screen_name=/level_2/
    H3: screen_name=/level_3/
    
    Session 4:
    H1: screen_name=/level_3/  cd2_value=3
    
カスタムディメンションの値は各セッションの最初のスクリーンビューのみ送信されていることに注意してください.

セッションレベルスコープはトライアル何日目かの値が、
一緒に送信される各ヒットだけではなくセッションの全てのヒットと関連付けられることを確実にします.

**Reporting**

処理の後、セッションレベルカスタムディメンションの値は同じセッション中に受けとった全てのスクリーンビュート関連付けられます.
開発者はDay of Trialおよびスクリーン名をディメンションとして、スクリーンビュー数をメトリックとしてレポートを作成できます.

Day of Trial | Screen name | Screen views
--- | --- | ---
1 | /level_1/ | 1
1 | /level_2/ | 2
1 | /level_3/ | 1
1 | /level_4/ | 2
2 | /level_1/ | 1
2 | /level_2/ | 1
2 | /level_3/ | 1
3 | /level_3/ | 1

そして最後に、スクリーンビューを日でグループ化し、
トライアルの各日に各レベルがどれだけプレイされているかを知るために、
開発者はDay of Trialをプリマリディメンションとして利用しレポートを作成することができます.

Day of Trial | Screen views
--- | ---
1 | 6
2 | 3
3 | 1

このデータでは、1日目が最も遊ばれており、2日目, 3日目と少なくなっていることがわかります.
この調査結果は、複数のセッションとそこに含まれるヒットを一つの値でグループするために、
セッションレベルのカスタムディメンションを利用したことで可能になったのです.

### User-scoped dimension

最後に、無料トライアルユーザーと有料購入ユーザーで、どれだけ遊ばれているかを知るために、
開発者がどのようにユーザーレベルセッションを利用できるかの例を紹介します.

これまでの例と同様に、各プレイ中に各レベルがどれだけ遊ばれているかは、
スクリーンビューと合わせて計測できています.
しかし、開発者は無料ユーザーと有料ユーザーでまとめたいと考えています.

開発者が望むレポートは以下のようになります.

Player Type | Screen views
--- | ---
Free | 
Paid |

ユーザーレベルスコープのカスタムディメンションを利用することで、
開発者は特定ノユーザーの全てのスクリーンビューを、
現在から将来のセッションにわたってひとつのPlayer Typeの値と関連付けてデータを取得することができます.

**Why user-level scope?**

ユーザーレベルスコープは、あるユーザーの全てのセッションおよびヒットを一つの値で簡単にまとめることができます.
この例の、Player Typeのように特定のユーザーで頻繁に変わることのない値に対して理想的であるといえます.

同様の機能はヒットレベルあるいはセッションレベルのスコープでも実現可能ですが、
ユーザーレベルスコープは少量のコードで手に入る最も便利な方法を提供します.  

**Configuration**

Webインターフェイスの管理セクションからThe Player Typeのカスタムディメンションを以下のように定義します.

Index | 3
Name | Player Type
Scope | User
State | Active

**Collection**

これまでの例と同様に、開発者はすでにスクリーンビューをレベルを一緒に計測しています.
これらのスクリーンビューをPlayer Typeでまとめるために、
開発者はユーザーがアプリを起動したときにPlayer Typeディメンションを設定するだけで良いのです.
もしユーザーが有料購入してフルバージョンのアプリを手に入れた時に再度実行してください.

Androidでは、開発者はユーザーが初めてゲームを始めた時にカステムディメンションを設定してください.

    Tracker tracker = EasyTracker.getInstance(); // The Google Analytics tracker.
    tracker.setCustomDimensin(3, "Free");        // Set the custom dimensin value.
    tracker.trackView("/level_1/");              // Custom dimension value is sent wit this hit.

またユーザーがフルバージョンを手に入れるために課金した時に再度カスタムディメンションを設定してください.

    Tracker tracker = EasyTracker.getInstance();
    tracker.setCustomDimension(3, "Paid");
    tracker.trackView("/level_3/");
    
**Processing**

これまでの例と同様に、データは集計、処理され、カスタムディメンションはそのスコープに合わせて各ヒットに適用されます.

例えば、２回は無料ユーザーとして、その後１回は有料ユーザーとしてプレイした１人のユーザーのデータは以下のようになります.

    visitorId = 5555
    Session 1:
    H2: screen_name=/level_1/ cd3_value=free
    H3: screen_name=/level_2/
    
    Session 2:
    H1: screen_name=/level_2/
    H2: screen_name=/level_3/
    H3: screen_name=/level_3/
    
    Session 3:
    H1: screen_name=/level_3/ cd3_value=paid
    H2: screen_name=/level_4/

Session 1で送られたfreeの値はそのセッションと、
新しい値"paid"がセットされるSession 3までのSession 2にも適用されることに注目してください.

**Reporting**

処理のあと、Player Typeのカスタムディメンションはその値が関連付けられたセッションと、
同様にその後のセッションとヒットに対しても関連付けが行われます.

開発者は、Player Typeとスクリーン名をディメンションとして、
スクリーンビュー数をメトリックとしてレポートを作成できます.

Player Type | Screen name | Screen views
Free | /level_1/ | 1
Free | /level_2/ | 2
Free | /level_3/ | 2
Paid | /level_3/ | 1
Paid | /level_4/ | 1

最後に、スクリーンビュー数をPlayer Typeでまとめ、無料ユーザーと有料ユーザーでどれだけ遊ばれいているかを知るために、
開発者はPlayer Typeをプライマリディメンションとして利用しレポートを作成することができます.

Player Type | Screen views
Free | 5
Paid | 2

このデータでは、課金ユーザーよりも無料ユーザーのほうが遊んでいることがわかります.
この調査結果はユーザーとそのユーザーのセッション、ヒットを一つの値でまとめることができる
ユーザーレベルカスタムディメンションを利用することで可能になりました.

### Custom metric

これまでの例では、ゲーム開発者はプレイレベルをスクリーンビューと一緒に計測しています.
各レポートの生成の際に、スクリーンビューメトリックは
プレイヤーがクリアしようとしたレベルの代わりとして利用されます.

しかしながら、開発者は各レベルの達成率も知りたいと考えるでしょう.

達成率を決定するために、
開発者はLevel Completionsというカスタムメトリックを導入し、
それを各レベルのスクリーンビューと比較することになります.

開発者が欲しいレポートはこのようになります.

Screen name | Screen views | Level Completions
--- | --- | ---
/level_1/ | | 
/level_2/ | | 
/level_3/ | | 

**Why use a custom metric?**

多くの場合、最も重要なメトリクスを計測するために、
イベントやスクリーンビューやカスタムメトリックを組み合わせて利用することができます.
しかしながら、カスタムメトリックはより柔軟で読みやすいカスタムレポートを作ることができます.
そしてそれは最も重要なメトリックを計測するための便利な方法です.

今回の例では、レベルの達成はレベルあたりのスクリーンビューを２回計測しないことには計測することができません.
そのため他の方法を望むことになるでしょう.

イベントはそれ単体で利用することができますが、その階層的な性質から
一つのディメンションの下でスクリーンビューとレベル達成を組み合わせた上記の様なレポートを作成するのは困難です.

上記の様な制限によって、またレベル達成度が開発者にとって重要なメトリックであることから、
レベル達成を計測する最も簡単な手段はカスタムメトリックであるといえます.

**Configuration**

The Level Completion カスタムメトリックはWebインターフェイスの管理セクションからこのように定義できます.

Index | 1
Name | Level Completion
State | Active

カスタムディメンションとは異なり、カスタムメトリックの定義にはスコープを含みません.
カスタムメトリックは暗黙的にヒットレベルのスコープとなります.

**Collection**

開発者はスクリーンビューから各レベルのスタートを計測しています.
今彼らはカスタムメトリックを利用してレベルの達成を計測したいと考えています.

カスタムディメンションのように、カスタムメトリックは他のヒットのパラメータとしてGoogle Analyticsに送信されます.
カスタムメトリックの値を送信するために、開発者はユーザーがレベルを達成したことを記録するための追加のヒットを送信する必要があります.
この例では、レベルが達成された時にイベントが発生し、そのイベントとカスタムメトリックが関連付けられます.

Androidでは、実装はこのようになります.

    // Called whenever a level is completed.
    public void onLevelComplete(){
        // Get the tracker object.
        Tracker tracker = EasyTracker.getTracker();
        
        // Incremnt the level completion metric by 1.
        tracker.setCustomMetric(1, 1);
        
        // Custom metric value is sent attached to the event hit.
        tracker.trackEvent("Level", "Completion");
    }
    
**Processing**

処理の前に、１回のセッション中に３つのレベルでプレイした１人のユーザーのデータは以下のようになります.

    visitorId = 5555
    Session 1
    H1: type=screen_view screen_name=/level_1/
    H2: type=event screen_name=/level_1/ cm1_value=1
    H3: type=screen_view screen_name=/level_2/
    H4: type=screen_view screen_name=/level_2/
    H5: type=screen_view screen_name=/level_2/
    H6: type=event screen_name=/level_2/ cm1_value=1
    H7: type=screen_view screen_name=/level_3/
    H8: type=event screen_name=/level_3/ cm1_value=1
    
**Reporting**

処理の後、開発者はスクリーン名をディメンションとして、
スクリーンビュー数と合計イベント、レベル達成をメトリックとして利用したレポートを作成できます.

Screen name | Screen views | Total Events | Level Completions
--- | --- | --- | ---
/level_1/ | 1 | 1 | 1
/level_2/ | 3 | 1 | 1
/level_3/ | 1 | 1 | 1

開発者はレベルの達成をカスタムメトリックとして計測しているので、
合計イベントからレベルの達成イベントを抽出する必要をなくしてくれます.

その代わりに、開発者はLevel Completionカスタムメトリックを利用した以下のようなカスタムレポートを作成できます.

Screen name | Screen views | Level Completions
--- | --- | ---
/level_1/ | 1 | 1 
/level_2/ | 3 | 1 
/level_3/ | 1 | 1 

このデータは、level 2 は level 1, 3に比べて相対的に難しいことを示しています.
スクリーンビューを基準として達成率は33%となります.
レベルの達成をカスタムメトリックとして利用することで、
開発者はキーメトリクスに関する疑問に答え、
他の人と共有するためのシンプルなレポートを作成することができます.

## Implementation Considerations

カスタムディメンションあるいはメトリックを実装するとき、以下のことに注意してください.

**既存のディメンションあるいはメトリックの編集**

すでに存在するカスタムディメンションあるいはメトリックの名前あるいはスコープを編集する場合、
データは以下のような影響を受けます.

- Editing name : すでに処理されたデータに影響があります. 古いデータは新しい名前のデータとしてのみアクセス可能です.
- Editing scope : すでに処理されたデータには影響はありません. 新しいデータのみが新しいスコープをもとに処理されます.
- Changing state : stateフィールドはカスタムディメンションあるいはメトリックが実際に処理されるかどうかを決定します. インアクティブ(無効)なカスタムディメンションあるいはメトリックはレポートに表示され続けますが、データの処理は関連するデータがないので、処理自体は行われていません.

**scope設定の計画**

どのスコープをあるカスタムディメンションに利用するかを決めるとき、どの値がどれだけ頻繁に変更される可能性があるかを考慮してください.
ゲームのレベルのように、あるセッションの中で頻繁に値が変更になるのであれは、hit-scopeを利用した上で各ヒットのたびにvalueを送信してください.
一方で、性別のようなカスタムディメンションはユーザーレベルで一度だけ設定されるものです。
性別の値を各ヒットの旅に送信するのは不要な量の作業が必要になってしまうため、
ユーザースコープであって頻繁に変更されるカスタムディメンションを設定することは,
各ヒットとカスタムディメンションの値を誤って紐付けてしまうことになります.


## Available Liraries

カスタムディメンションとメトリックは以下のライブラリでサポートされています.

- [GoogleAnalytics SDK for Android v2.x](https://developers.google.com/analytics/devguides/collection/android/v2/)
- [GoogleAnalytics SDK for iOS v2.0](https://developers.google.com/analytics/devguides/collection/android/v2/)