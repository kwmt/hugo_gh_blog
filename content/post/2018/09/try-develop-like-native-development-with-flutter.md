+++
date = "2018-09-30T15:45:00+09:00"
title = "AndroidやiOSのアプリ開発でいつも私がやっていることをFlutterでやってみた"
draft = false
categories = ["Flutter"]
tags = ["Flutter","登壇", "Clean Architecture", "CI", "CD", "CI/CD", "設定切り替え"]

+++

## はじめに

2018/09/29(土)にGDG神戸さん主催の<a href="https://gdgkobe.doorkeeper.jp/events/78737" target="_blank">Flutter勉強会 (9/29)</a>が開催されました。そこで「AndroidやiOSのアプリ開発でいつも私がやっていることをFlutterでやってみた」というタイトルで約30分間発表させて頂きました。

<a href="https://speakerdeck.com/yasi/androidyaiosfalseahurikai-fa-teitumosi-kayatuteirukotowoflutterteyatutemita" target="_blank">スライドはこちら</a>

その時の発表内容を書いておきたいと思います。基本的に原稿をもとにブログ用に加筆・修正しています。また図を参照しているところは基本的にスライドを参照いただいて、必要そうなところだけブログ内に画像を貼っています。

## 冒頭

現在Flutterでチャットアプリを作っていてリリース間近なのですが、そのアプリ開発の目的の1つとして、AndroidやiOSのアプリ開発でいつも自分がやっていることはFlutterでできるのか、できるならどうするのかというのを目的に開発していました。

いつもやっていることっていうのは、3つあります。

- 1つ目は、アプリの設計をクリーンアーキテクチャという設計でやっています。
- 2つ目は、例えばサーバーの向き先を変えるなど、開発と本番での設定を切り替えることをやっています。
- 3つ目は、CI/CDサービスを使って、たとえば、git pushしたらビルドやテストを実行し、masterブランチにマージされたら関係者にアプリを配信したりしています。

これらが実現できれば、仕事にも使える可能性はありそうだなという思いで開発し始めまして、結論から言うとできそうということがわかりましたので、それぞれについて紹介したいと思います。


## クリーンアーキテクチャが実現できるか？

について見ていきましょう。

クリーンアーキテクチャはおそらくボブおじさんという方が考えたんだと思うんですが、ソフトウェア開発の設計方針の一つです。

この図は下のURLから引用したものですが、 アプリのクラスがこの円のように何層かに分かれていて、ここの矢印の方向で、クラスの依存関係を決めましょうと言っているだけです。たとえば、UIはPresenterに依存できますが、PresenterはUIに依存してはいけません。ちなみにこの円の層は何層でも構いません。

このようにすることで、

- アプリの根幹となるビジネスロジック部分がフレームワークと切り離すことができ、
- ビジネスロジックをテストしやすくなったり、
- UIが独立し、ビジネスロジックの変更なしにUIを変更できるようになります。
- またデータベースとも独立し、たとえば、いまはFirestoreを使っているけど、AWSのdynamoDBに切り替えたりすることが容易になります。

ここまでが先程のURLにも書いてあるメリットですが、僕がもう1つ思うのは

- 基本的にどんなアプリやサーバーのソフトウェア開発でも採用できるので、どこに何があるかが統一出来て分かりやすい

ということもメリットの一つかなとも思っています。まぁ一言で言うと、メンテナンスしやすい設計ということになります。


もうちょっと図を変えて説明しますね。


<img src="/images/2018/09/try-develop-like-native-development-with-flutter/clean-architecture.png" />

顔アイコンはアプリを使うユーザーと思ってください。
その下のScreenというのは、ユーザーがみてる画面で、何でも良いのですが、ここでは例えばチャットルーム一覧画面とします。
チャットルーム一覧のデータは、Firestoreというクラウドのデータベースに保存されているとします。
クリーンアーキテクチャは、このチャットルーム一覧画面がFirestoreからどうやって取得するかの方針になります。


チャットルーム一覧画面は、チャットルームの一覧を表示したいので、チャットルーム一覧を取得するUseCaseというクラスに依頼します。

そのUseCaseクラスはRoomに関するものを提供してくれるRepositoryというクラスに依頼すると、DataSourceというクラスを経由してチャットルーム一覧のデータをFirestoreから
取得し、Screenにチャットルーム一覧のデータを伝えるというのが全体の流れです。

青の矢印はデータ流れを表していて、赤の矢印は依存関係を表しています。
赤矢印の例でいうとたとえば、ScreenはUseCaseに依存していて、UseCaseはRepositoryに依存し、右側のData SourceはRepoistoryに依存しているという感じです。

また、この点線あたりで区切って、それぞれ左からpresentation層、domain層、infrastructure層と僕は呼んでいます。他の参考プロジェクトなんかはinfrastructure層をdata層と呼んだりしています。

先程のボブおじさんの図と重ねるとこんなイメージです。

ごちゃっとしててわかりにくいですかね、、、なんとなく関係ありそうなことがわかりますかね？

次に、この設計をどのように実現しているかを見ていきたいと思いますが、実現するためにには、

- 言語としてインターフェイスがあることと、
- DIができること

が必要になります。


まずDartにインターフェイスがあるかですが、結論から言うとあります。
interfaceというキーワードのインターフェイスではないのですが、クラスを定義すると、そのクラスの同名のインタフェースが暗黙的に定義されるという仕様です。また抽象メソッドのみをもつjavaのインターフェイスのようにするには、abstractキーワードで抽象クラスを作成して実現できます。

次にDIですが、詳しくは説明しませんが、DIとはDependency injectionの略で、一言で言うと、「必要なものを外から渡すこと」です。これはだいたいコンストラクタの引数にインスタンスを渡せればよくって、Dartは普通にそれができるので、DIができるかというよりは、依存関係を簡単に解決してくれるDIプラグインはあるか？に言いかえた方が良いかもしれません。
AndroidではDagger2やkodein、iOSではSwinjectとか使っている方が多いでしょうか。このようなプラグインをpub.dartで探しますと、
<a href="https://pub.dartlang.org/packages/flutter_simple_dependency_injection" target="_blank">flutter_simple_dependency_injection</a>
というプラグインがあったので、それを使います。

後ほど使い方の例をお見せしますが、名前にあるように使い方はほんとにsimpleです。AndroidのDagger2のように、なぜビルドエラーになるのか悩まされることはありません。


さて、インターフェイスがあって、DIプラグインもあるので、先程の例で挙げたチャットルーム一覧画面のところを具体的に実装して行きたいと思います。

まずチャットルーム一覧を表示したいのでRoomというクラスを作り、あるユーザーのルーム一覧を取得したいのでUserというクラスを、ドメインのモデルに作っておきます。


次にルームリストを取得するというUseCaseの名前を `FetchRoomListUseCase` としてUseCaseを作ります。これには`execute`メソッドをもたせ、抽象クラスとして定義します。
これを抽象クラスにしているのは、presentation層とdomain層の境界を疎結合にしたいためです。
また、executeメソッドの戻り値の型をFutureとしていますが、これはJavascriptのPromiseのようなもので、infrastructure層からpresentation層にデータを伝えるために重要な役割になるものです。

`FetchRoomListUseCase`を実装するのは、`FetchRoomListUseCaseImpl`クラスです。
`FetchRoomListUseCaseImpl`クラスは、Repositoryからチャットルーム一覧を取得したいので、まだ作成していないですが、`RoomRepository`をもたせています。

`RoomRepository`は抽象クラスとし、 `fetchRoomList`という抽象メソッドを定義しておきます。
この`fetchRoomList`メソッドを実装するのは、ここではFirestoreからデータを取得したいので`FirestoreDatasource`というクラスを作成し、そのクラスで実装します。

具体的にFirestoreに保存してあるチャットルーム一覧を取得する処理を書きます。
この取得に成功すると、先程のFutureの仕組みでpresentation層のチャットルーム一覧画面に伝わるようになります。

これでUseCase以降のチャットルーム一覧を取得するのに必要なクラスを作成したので、あとはそれをつなぎます。

たとえば、これは先程の`FirestoreDatasource`のコンストラクタの部分ですが
、`FirestoreDatasouce`は`firestore`がほしいと要求しているので、firestoreインスタンスを生成して、DIプラグインから取得した`injector`にfirestoreをセットします。真ん中あたりのところです。

次に一番下のあたりで、`injector`に`FirestoreDatasource`インスタンスを生成してセットしたいのですが、このとき`injector`からFirestoreインスタンスを取得することができるようになっているので、Firestoreインスタンスを取得して、`FirestoreDatasource`の引数に渡してあげます。
このようにして依存関係を解決していくだけです。

UseCaseなども同様に依存関係を解決すると、このようになります。
ここまでで`FetchRoomListUseCase`までインスタンス生成できたことになります。

あとは、チャットルーム一覧画面の`RoomListScreen`で`FetchRoomListUseCase`を使いたいので、それを渡してあげます。

DIの準備はこれで終わりです。実際に使っていきたいのですが、まずStatefulWidgetの`RoomListScreen`のコンストラクタで`FetchRoomListUseCase`を引数にとります。

Stateの`RoomListScreen`のたとえば、`initState`メソッドで`FetchRoomListUseCase`の`execute`メソッドを呼んであげると、チャットルーム一覧のデータを取得することができます。


以上で、ぼくのやりたかった設計がこれで実装できたのですが、どうですかね？
慣れてないと初見ではちょっとむずかしいかもしれませんが、これで一応先ほどの図の実装はできました。

## アプリの環境を開発用と本番用	

次にアプリの開発用の設定と本番用の設定を切り替えるにはどうすればよいかに移りたいと思います。

ここでは、基本的にAndroid、iOSそれぞれの設定が必要になります。

- Androidであればフレーバーの設定で、
- iOSであればSchemeの設定

です。このあたりはネイティブ開発でよくあることなので調べてください。
ここでは、フレーバーを、開発用はdevelopment、本番用はproductionと名前を付け、各OSの設定が終わったという前提で、Flutter側の設定の話をします。

実は開発と本番の設定を切り替える方法はいくつかあるとは思いますが、今回はFlutterの`InheritedWidget`というクラスを使う方法を紹介したいと思います。

`InheritedWidget`というのは、ドキュメントをほぼ直訳しただけなんですが、次のように書いていました。

- Widgetツリーの下位ツリーのWidgetに効率的に伝えるベースクラス
- contextから`InheritedWidget`のインスタンスを取得するには、`BuildContext.inheritFromWidgetOfExactType`を使う
- `BuildContext.inheritFromWidgetOfExactType`を使って参照された`InheritedWidget`は、Widgetの状態が変わったとき、再構築させることができる

これだけだとよくわかんないので、例をあげて説明します。
ログイン画面があるアプリを考えます。

未ログインならログインする画面があり、ログイン済みならログアウトボタン付きのホーム画面が表示されるようなアプリです。

<img src="/images/2018/09/try-develop-like-native-development-with-flutter/sample-of-inheritedwidget.png" />

この図のようにWidgetツリーとして考えると、一番上のAppウィジェットがRootウィジェットを持っていたとしてそのRootウィジェットがログインしているかどうかを判断します。判断の結果、未ログインならログインするLoginWidgetを表示し、ログイン済みならログアウトボタンがあるHomeWidgetを表示するというイメージになります。

Rootウィジェットがログインしているかどうかを判断するということは、ログイン判定用のインスタンスが必要でそれをauthとします。そのauthはログインもログアウトもできるとした場合、未ログインの場合はログインウィジェットに渡す必要がありますし、ログイン済みの場合はホームウィジェットにauthを渡す必要が出てきます。
その結果、RootとLoginとHomeそれぞれのウィジェットには、authインスタンスがある状態になってしまいます。

これだけなら特に問題ないように見えると思いますが、次の図のように、

<img src="/images/2018/09/try-develop-like-native-development-with-flutter/sample-of-inheritedwidget2.png" />

例えばLoginウィジェットの前にログイン状態とは関係ないウィジェットが複数あった場合、Rootウイジェットからauthインスタンスをログインウィジェットまで渡して行く必要があり、authとは関係ないWidgetAとかBに、使わないauthインスタンスを保持する必要が出てきてしまいます。

これを解決するのが`InheritedWidget`で、`InheritedWidget`を継承した例えば`AuthProvider`というクラスを作ります。
`AuthProvider`にはauthインスタンスをもたせて、コンストラクタでもらうようにしておきます。
後で使うofメソッドと、updateShouldNotifyメソッドを実装します。
updateShouldNotifyというのは、`inheritedWidget`が更新されたら、これを継承するウィジェットに通知してウイジェットを再構築させるかどうかのメソッドで、trueだと再構築させ、falseだとさせません。ここでは簡単のため、trueを返すことにしますが、実際はログインチェックして、状態が同じならfalseを返すとかする必要があるかと思います。
こうやって作った`AuthProvider`を先程のAppの親にして、Authインスタンスを生成します。

先程authインスタンスが必要だった、`Root`と`Login`と`Home`ウィジェットでは、どうやって`auth`インスタンスを参照するかといいますと、`AuthProvider`のofメソッドを使って`AuthProvider`インスタンスを取得できるので、それがもっているauthインスタンスを参照することができます。


前置きが長くなりましたが、この`InheritedWidget`の仕組みを使って開発用と本番用に分けたいと思います。そこで`InheritedWidget`を継承したAppConfigというクラスを作成します。
ここではサーバーのURLを開発と本番で切り替えたいので、先程はauthでしたが、ここでは`baseWebUrl`という変数を用意しています。またAppConfigを更新することがないので、updateShouldNotifyメソッドではfalseを返すようにしておきます。

次に`main.dart`ファイルを開発用と本番用それぞれのファイルに分けます。
おそらくmainファイルは、main関数の中に`runApp(MyApp())`みたいな感じで書いてるだけだったりすると思うのですが、それをまずは`main_development.dart`ファイルと`main_production.dart` ファイルに分けます。

先程の`AuthProvider`の例のように、`AppConfig`は`MyApp`の親にしたいので、
たとえば開発版では`appConfig`インスタンスを生成するとき、`baseWebUrl`には開発用URLを設定して、childにはMyApp()ウィジェットを入れます。
生成したappConfigインスタンスはWidgetなので`runApp`関数に渡すことができます。
productionの方は、`baseWebUrl`だけプロダクション用に変えたいのでそこだけ変えたらあとは同様です。

使い方は`AuthProvider`のときと同様に、ofメソッドを使ってAppConfigインスタンスを取得し、その`baseWebUrl`を参照できます。開発版のフレーバーdevelopmentでビルドしたときは、development用の設定になっていますし、productionでビルドすればproductionの設定になっています。


あとは、ビルドをどうするか、ですね。

flutterコマンドのサブコマンドにrunやbuildコマンドがあって、それらのオプションに
どのmainファイルを使うかというtargetのtオプションがあり、
今回の場合は開発をdevelopment、本番をproductionとしましたがどちらかをしてする`flavor`というオプションがありますので、それを使って`flutter run`を実行することができます。

<img src="/images/2018/09/try-develop-like-native-development-with-flutter/build-with-flavor.png" />

コマンドではなくAndroid Studioで開発と本番を切り替えるには、Run Configurationのところで、
Edit Configurationsを選択して、設定画面が開くので、左側で新しいConfigurationを追加し、たとえば、分かりやすいようにmain_developmentのように名前を付けます。

右側のDart Entrypointのところで、さきほどコマンドでtオプションで指定したファイルパスをセットし、Build Flavorのところでflavorをセットします。ここでは開発用にしたいのでdevelopmentを設定しています。
これでOKを押して通常通りRunすると、開発版でビルドされ実行されます。
ぼくは設定していないですが、productionも基本的には同様にできるかと思います。


## CI/CDはどうするのが良さそうか？

最後にCI/CDについてです。
CIはContious Integrationの略で継続的インテグレーション、CDはContinuous Deliveryの略で継続的デリバリーです。と言ってもわかりにくいので、次の図のようなことをしたいといういことです。

<img src="/images/2018/09/try-develop-like-native-development-with-flutter/ci-cd.png" />

左下の開発者がGitサービスに、たとえばgithubやgitlabにソースをプッシュしたら、CIサービスを使ってビルドして、ビルドが成功したら AndroidアプリとiOSアプリをDeploygateに上げて、ユーザーが使ってもらえるようにすることです。ネイティブアプリ開発ではどのプロジェクトもだいたいこれを実現しているのですが、Flutterではどうするのか、というか僕がチャットアプリ開発でどうしているか、というのを紹介したいと思います。

まず、僕がよく使うGitサービスはGithubかGitlabです。bitbucketっというのもありますが、ぼくには体質的に合わないので除外します。
githubはpublicリポジトリだと無料ですが、privateリポジトリだと有料で、
gitlabは有料プランもありますがどちらも無料で使えます。

publicかprivateかですが、今回はfirebase周りのcredentialな情報を扱うためprivateがよくて、個人なので無料がいいので、今回のアプリ開発ではGitlabを使っています。

AndroidとiOSに対応しているCIサービスは他にもあるかもしれませんが、僕の知ってるところだとここに挙げている3つあります。

- CircleCIは、Androidが無料で使えますが、iOSは有料です。
- BitriseはAndroid,iOSともに無料で使えますが、1回のビルド時間に10分という制限があります。
- CirrusCIというはFlutter触り始めて初めて知ったのですが、Android 、iOSともに無料で使えます。時間制限もなさそうです。ただし、Githubだけしか対応していなくて、料金はpublicリポジトリのみ無料で、privateリポジトリは有料になります。このCIはFlutter公式リポジトリで使われていますので、使うときは参考になるかもしれません。

今回はAndroidもiOSもどちらもprivateリポジトリで使えて無料枠があるbitriseを使ってみました。

ただ、CIサービスはすべてのGitサービスに対応しているわけではありませんので、bitriseがgitlabで使えるか確認すると、使えることがわかりましたので、GitサービスはGitlabを使い、CIサービスはbitriseを使いはじめました。

bitriseは、先程もちらっと言いましたが、1ビルド10分という時間制限があります。
これがネックで、bitriseは現時点ではまだFlutterには対応していないので、bitriseでのビルド中にFlutterアプリをビルドできる環境を作る必要があります。
CIで最低限やりたいのはアプリのビルドです。それプラスFlutterアプリをビルドできる環境10分以内に終わらせることができたらよかったのですが、iOSはアプリのビルド中に10分経過してしまい、CIのビルドを完了することが出来ませんでした。Androidの方はギリギリ10分の枠で収まったり収まらなかったりしてました。

そこで、iOSを無料でCIを動かすのは難しそうなのと、Androidのビルドも時間内に収まらないことがあるので、現在の運用としてはbitriseをやめていて、別のGitlabCIというCIサービビスを使ってAndroidのみCIを使う運用に切り替えています。

<img src="/images/2018/09/try-develop-like-native-development-with-flutter/ci-cd-now.png" />

もちろん、bitriseはお金を払えば十分選択肢に入ると思いますし、bitriseがFlutterに対応してくれれば、iOSのビルド時間のみですむため、無料でのワンちゃんあるかもしれません。今後に期待したいです。

ここのDeployGateに上げる部分は、Flutterの公式ドキュメントに載っていたのでAndroidもiOSもFastlaneというのを使っています。時間がないので詳細はドキュメントを参照ください。

<a href="https://flutter.io/fastlane-cd/" target="_blank">https://flutter.io/fastlane-cd/</a>


また、CIサービスにbiriseを使うならDeployGateも使うことができるようなので、紹介しておきます。

<a href="https://qiita.com/kyoro353/items/200d5b34b9f5805dd43a" target="_blank">https://qiita.com/kyoro353/items/200d5b34b9f5805dd43a</a>

まとめとして、

- いつもやっているクリーンアーキテクチャは実装できます。ただし注意として、クリーンアーキテクチャがFlutterにとって最適かどうかはまた別の話なので、もっといい設計があるかもしれません。
- 開発と本番の設定を切り替えることも可能です。
- CI/CDに関しては、使うサービスだったり、publicかprivateの組み合わせによっては無料で使うことができそうですが、privateだとすべて無料は現状厳しいかなと思いました。

以上、ご清聴ありがとうございました！



# おわりに

正直、Android/iOS開発経験者向けだったので、開発をしたことがない方にとっては難しかったと思います。まぁ自分の説明も悪かったと思うのですが・・・。
ただTwitter上の反応は良かったので、別途機会あれば再チャレンジしたいなと思いました！

<img src="/images/2018/09/try-develop-like-native-development-with-flutter/slide-on-twitter.png" />

反省点としては、

- 資料作成が朝の5時までかかってしまって、眠かった・・・
- 発表時の録音していて、その録音を聞いてたら「まぁ」っていうのをめっちゃ言ってた。。。


機会をくださったGDG神戸関係者の皆様、発表を聞いてくださった方々、ありがとうございました！

# 参考
- Clean architecture 
    - https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html 
- DIについてわかりやすいと思う記事 
    - https://blog.ishkawa.org/2017/09/18/1505701774/ 
- flavorについて 
    - https://medium.com/@salvatoregiordanoo/flavoring-flutter-392aaa875f36

