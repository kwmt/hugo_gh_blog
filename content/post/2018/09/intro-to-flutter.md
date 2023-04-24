+++
date = "2018-09-15T16:30:00+09:00"
title = "Intro to Flutter"
draft = false
categories = ["Flutter"]
tags = ["Flutter","登壇"]

+++

## はじめに

2018/09/15(日)にクラスメソッドさん主催の<a href="https://classmethod.connpass.com/event/97832/" target="_blank">モバイルメソッド大阪第二回</a>が開催されました。そこで「Intro to Flutter」というタイトルで発表させて頂きました。

<a href="https://speakerdeck.com/yasi/intro-to-flutter" target="_blank">スライドはこちら</a>

その時の発表内容を書いておきたいと思います。基本的に原稿をもとにブログ用に加筆・修正しています。

## 冒頭

こんばんは！新しいiPhone出ましたね！
なんか新しいサイズが増えたとかなんとか。。
この資料作ってて全然キャッチアップできてないので、懇親会とかで教えて頂けたら嬉しいです。

余談でしたが、始めます。


今、僕自身が<a href="https://flutter.io/" target="_blank">Flutter</a>というものを使ってモバイルアプリを作成するのにハマっているので、Flutterの紹介させて頂きたと思います。


まず自己紹介でチャットアプリを作成中で下記の動画で紹介しました。

{{< youtube gNALr-zmpFY >}}


現在Flutterでチャットアプリを作成中でして、ちょっと動画あるので再生しますね。
SNSログインして、チャットルームを作成します。このときチャットルームの写真を撮っています。チャットルーム名にGopherと入れて、Gopherという名前のチャットルームができました。
そのGopherのチャットルームに入って、メッセージを送信しているという動画です。


このチャットアプリは現在Deploygateにて配信中で、インストールしてためしていただくこともできますので、もしご興味があればお声がけください。

さて、本題に入ります。

今日持って帰って頂きたいものは、Flutterというものがどういうものか知って、Fluttterを触ってみたくなることが目標です。

話さないことととしては、Flutterは時折、React NativeやXamarinと比較されることがありますが、僕自身React NativeとXamarinは触ったことがありませんので、違いについては話しません。

## 突然ですが

ここにモバイルアプリを開発できるエンジニアが一人いるとします。

<img src="/images/2018/09/intro-to-flutter/engineer.png" />


このとき、たとえば

- お客さんからAndroidとiOSのアプリを作ってくださいと言われたら、
- 個人開発でAndroidとiOS応のアプリを作りたいなぁと思ったら、
みなさんならどうしますか？

お仕事の場合は、例えば日本人向けのアプリだからまずはiOSからつくったりして、
お客さんからもうちょっとこうしたいなぁ、という要望だったり
このパターンでおちるんだけど、、、というクレームだったり

とかいろいろ対応していたら1ヶ月予定が2ヶ月、3ヶ月と過ぎてしまい、なかなかAndroidの開発できない・・・

個人の場合も、

- 例えば日本人向けのアプリだからまずはiOSからつくって、
- 大体できたんだけど、ここ使いにくいから、もうちょっとこうしたいなぁ。
- あ、使ってみて思ったけど、あの機能絶対リリースまでにはいるやん！

とか対応していたら、こちらの場合も1ヶ月予定が2ヶ月、3ヶ月過ぎてしまい、なかなかAndroidの開発できない・・・

となって、例えばiOSリリースだけで満足してしまうことってないですか？

もちろんお仕事の場合はスケジュール管理しながら進めていくと思いますのでそんなことはないと思いますが。


このようなとき、

- もし、ネィティブ並みにサクサク動くアプリが１つのソースコードコードで書けるとしたら？
- もし、プログラミング言語がkotlinやswiftとは違うけど、そこそこいい感じの言語だったとしたら？
- もし、開発スピードを上げれるとしたら？

iOSだけ作ってAndroidができないとか、またはリリース時期が遅くなるということはなくなりそうな気がしませんか？



これらを解決できるのがFlutterです。


<img src="/images/2018/09/intro-to-flutter/flutter.png" />

## Flutterとは

FlutterとはAndroidと iOSアプリを１つのソースコードで開発できるtool kit です。

プログラミング言語にはDartを採用しています。


<img src="/images/2018/09/intro-to-flutter/dart.png" />

Flutterはハイパフォーマンスのレンダリングエンジンを持っていて、DartコードはネイティブARMコードにコンパイルされます。

そのため、こちらのFlutterで作成されたアプリの動画を見ていただきたいのですが、


<table >
<tr>
<td valign="top">
<img src="/images/2018/09/intro-to-flutter/flutter-ui-1.gif" width="300"/>
</td>
<td valign="top">
<img src="/images/2018/09/intro-to-flutter/flutter-ui-2.gif" width="300" /> 
</td>
</tr>
<tr>
<td valign="top">
<img src="/images/2018/09/intro-to-flutter/flutter-ui-3.png" width="300" />

</td>
<td valign="top">
<img src="/images/2018/09/intro-to-flutter/flutter-ui-4.gif" width="300" />
</td>
</tr>
</table>




どうですか？なかなかきれいでサクサク動いてそうなイメージ湧きませんか？


これらのUIがどのように構成されているかですが、
画像もボタンもテキストもアイコンもこれらすべてWidgetとよばれるものから構成されています。
ちょっと別の例を見てみましょう。

<img src="/images/2018/09/intro-to-flutter/lakes.jpg" />


こんなレイアウトがあるとします。
これがどのような構成になっているか、分けてみますと、


<img src="/images/2018/09/intro-to-flutter/lakes-diagram.png" />

一番上から画像、星印の行、電話とかRouteとかShareボタンの行、あとは説明の行と、縦に並んでいることが分かるかと思います。
その下の図は飛ばして、下図はボタンの行がどのようなレイアウト構成になっているかを示したものですが、

<img src="/images/2018/09/intro-to-flutter/button-section-diagram.png" />

まずCALLやROUTEボタンなどが横に並んでいて、それぞれのボタンはアイコンとテキストが縦に並んでいるのがわかるかと思います。
この一つのボタンに注目して、コードではどのように書くのかを見てみたいと思います。

<img src="/images/2018/09/intro-to-flutter/flutter-layout.png" />


左の画像は、先程SHAREボタン部分を拡大したものです。
右のコードは左のボタンを表すコードになります。
FlutterではレイアウトはDartコードで書きます。AndroidのようなレイアウトxmlやiOSのようなinterface builderのようなものありません。ザッツ・オールです。

このSHAREボタンはアイコンとテキストが縦に並んでいるというレイアウトです。

まずColumnで囲った縦方向にレイアウトしますよと宣言します。このColumはWidgetです。
何を縦方向にレイアウトするかですが、ここではアイコンとテキストを入れます。
これらのアイコンとTextもWidgetです。
他の複雑なレイアウトも同じようにしていくだけです。
こうして構築したレイアウトはAndroidとiOSの両方で動きます。

<img src="/images/2018/09/intro-to-flutter/flutter-on-android-ios.png" />

## Dartはそこそこ良い

コードを少し見たので、次にDartがそこそこ良いということについてお話します。
少し言語仕様を紹介すると、DartはJavaやjavascriptに似た言語だと思います。

- JSと似ていますが、型があります！！
- 次にDartはfirst class objectです。ですので、たとえば関数の引数に関数を渡せます。
- また型推論できます。
- async/awaitがありcallback 地獄にはならなさそうという印象を抱かせてくれます。
- JSのarrow関数みたいな感じで書けたりもします。
- あと、条件付きメンバアクセスが可能です。これはkotlinとかswiftでよく使うと思いますが、Dartでもできます。
- swiftでもできますが、＋や−などの演算子をoverrideできる仕様も入っています。
- ListからListに変換するmapやある条件のものに絞り込む他の言語ではfilterと呼ばれるwhereがあり。List操作もスッキリ書けます。

この辺りはSwiftとかkotlinとかJavascriptとかでよくある仕様でとっつきやすいのかなぁと思いますが、いかかでしょうか

さらに僕が思うDartっぽい仕様でいうと、

- アクセス制御には、publicやprivateというものはありません。どうやってアクセス制御するかは、変数名や関数名の最初にアンダースコアをつけるとprivateになります。
- あとカスケード記法というのが便利で、kotlinのapplyと似てると思うのですが、オブジェクトに対して、自分自身を返さないメソッドでも、メソッドチェーンのように書けます。
- 非同期も扱いやすくて、JavaScriptのPromiseのようなFutureだったり、RxのObservableのようなイメージのStreamというのが標準でサポートされているので非同期周りもスッキリ書ける印象です。ちなみに僕はまだ使っていない（使う場面がいまのところない）のですが、RxDartもあります。


ただイケてないところもあります。

僕がいま思っているのは、

- 行末のセミコロンが必要なことです。いまどきの言語としてはイケてないかなぁと思います。
- kotlinやswiftやってると拡張関数使ってスッキリ書くことが多いのですが、Dartにはありまません。issueには挙がってるようなのですが、どうなることやら、という感じです。
- あとは、Enumが型と値をセットで定義できなかったりするので、たとえばタイプと表示する文字列をセットで定義しておきたい場合などに不便かなという印象です。

このあたりがイケてないとこでしょうか。

まぁこのイケてなさそうなところを踏まえても、他にも紹介していない仕様はありますが、そこそこよさそうな感じしませんか？


## 開発環境

次に開発環境についてお話します。
Flutterの開発環境構築は簡単です。公式サイトからFlutterSDKのzipがあるので、ダウンロードして解凍します。

flutterコマンドへのPathを通して、`flutter doctor`というコマンドを実行します。

flutter doctorコマンドはflutterを動かすのに必要なローカル環境を調べてくれて、必要なものがなければ教えてくれ、さらにどうすればそれを解決できるか教えてくれるコマンドです。

<img src="/images/2018/09/intro-to-flutter/flutter-doctor.png" />

この例だとAndroidのコマンドラインツールがなくて、このURLからダウンロードしてね、と教えてくれています。

macの人はhomebrewでインストールしたいと思うかもしれませんが、いまのところサポートされていないようです。

## エディタ(IDE)

エディタはAndroid Studio,Intellij IDEA、VSCodeでflutter pluginが提供されています。
flutterコマンドを使ってプロジェクト作成やビルド、デバイスにデプロイもできたりするので、極論言うと、使いなれたエディタでFlutter開発はできるかなと思います。ちなみに僕は主にAndroid Studioを使ってます。

Android Studioの場合、Flutter 専用のProject作成画面があって、普通のアプリケーション作るのか、プラグインを作るのかとかいう選択できます。

ちょっとアプリケーションプロジェクトを作成してみます。
すると、このようなプロジェクトが作成されます。

<img src="/images/2018/09/intro-to-flutter/new-project.png" />

左側のProjectペインを見てみると、Androidになっておらず、projectになっているのも好印象です。

## プロジェクト構成

プロジェクト構成を見ていきます。
基本的にアプリを作る際は、libディレクトリの下でDartコードを書いていくことになります。


<img src="/images/2018/09/intro-to-flutter/project-structure.png" />


必要ならtestを書くこともできます。testについては詳細は触れませんが、`flutter test`コマンドでtestを実行することができます。


<img src="/images/2018/09/intro-to-flutter/project-structure-native.png" />


上のandroidと iosディレクトリのところがネイティブの部分になっていて、これらを展開するとAndroid,iOSそれぞれのプロジェクトでよく見る構成になっていることがわかるかと思います。

Androidの application idやAndroidManifest.xmlにパーミッションの設定をするときにはこのあたりを触ることになります。

またiOSはAndroid Studio上から触ることはあまりないかもしれません。
Android StudioからXcodeを立ち上げることができますので、XcodeからBundle IDやパーミッションの設定、その他Build設定などを編集することになります。
ちなみにAndroid StudioのTools MenuのFlutterからOpen iOS module in Xcodeというのを選択すると、Xcodeを起動することができます。


<img src="/images/2018/09/intro-to-flutter/open-ios-module-in-xcode.png" />

## パッケージ

次はパッケージについてです。
たとえばFirebaseとかカメラまわりとかのいろいろなパッケージを追加することがあるのですが、それらを管理する、Androidでいうbuild.gradle、iOSでいうpodfileのようなものも用意されています。
それは*pubspec.yaml*というファイルで管理することになります。


<img src="/images/2018/09/intro-to-flutter/pubspeck.png" />

これはいま僕が作成しているpubspec.yamlの一部ですが、google signinやfirebase messagingなどを使っていて、このような感じで書きます。
このyamlファイルに記述したあと、`flutter packages get`というコマンドを叩いてパッケージをインストールします。Androidでいうsync gradleで、iOSでいうpod installに相当します。


プロジェクト構成の話はこのくらいで、プロジェクトを実行しようと思いますが、そのまえにシミュレータを立ち上げてみたいとおもいます。
デバイス選択のところから、iOSのシミュレータを起動することができます。
さらに続けてビルドしてシミュレータで実行してみます。
Xcodeを起動して実行しなくてもAndroid Studioから実行することができます。

<img src="/images/2018/09/intro-to-flutter/run-project.png" />

## ホットリロード
下記の動画を、コードを書き換えて保存したらすぐにデバイスに反映される動画です。

<img src="/images/2018/09/intro-to-flutter/hot-reload.gif" />

どうですかね？この速さ感動しませんか？
ビルド中にツイッター見る暇なんか無くなるんです（笑）

※発表では実際に動かしてデモしました。

## まとめ
これまでのまとめとして、

- もし、Android とiOSでネィティブ並みにサクサク動くアプリが1つのソースコードで書けるとしたら？FlutterならNativeにコンパイルされるため、サクサク動くコードが１つのソースコードかけそうですよね
- Dartはそこそこ良さそう？というのにも、そこそこ良さそうに思ってもらえたんじゃないかなぁと思っています。
- もし、開発スピードを上げれるとしたら？使い慣れたAndroid Studio使えるし、なによりホットリロード最高！で開発速度早くなりそうな気がしませんか？



## One more thing...

One more thingということで、あと３点ほど話させてください。


### Nativeの機能にアクセス可能
１つ目は、クロスプラットフォーム場合に気になるのが、Flutterからネイティブの機能にアクセスできるのか、というのがあると思います。これについては詳細は割愛しますがFlutterとネイティブ間で`MethodChannel`という機構を使って簡単にネイティブとのやりとりができるような仕組みがあります。ですので、Flutterだけで完結できない機能要件があったとしても、最悪ネイティブを実装することでなんとかなります。

<img src="/images/2018/09/intro-to-flutter/methodchannel.png" />

### ドキュメントが豊富
Flutterの公式ドキュメントは豊富かなと思います。Install方法からFlutterの始め方が書かれてあるのはもちろんのこと、AndroidやiOS などの各developers 開発者向けのドキュメントが用意されていたりします。そこには、たとえば、AndroidやiOSのViewに当たるものはなに？とか、どうやってWidgetを更新するのかみたいなことがAndroid iOSに関してはQ&A形式で書かれています。他には、共通のパターンがCookbookとしてまとめられたりしています。

共通のパターンを知るのはCookbookもいいですが、僕がよく見るのは<a href="https://play.google.com/store/apps/details?id=io.flutter.demo.gallery&hl=en" target="_blank">Flutter Galleryアプリ</a>をよく見ます。これはPlay Storeで公開されていて、Android端末を持っている人はいますぐ使えます。

これの何がいいかって、<a href="https://github.com/flutter/flutter/tree/master/examples/flutter_gallery" target="_blank">ソースが公開</a>されていることです。こんな動きってどう実装するんだろうというのがサクッと確認できるので、今作っているアプリはほとんどこれを参考したと言っても過言ではありません。

### Flutterは人気急上昇中
最後３つ目は、Fluttterは人気上昇中ですということをお伝えしたいと思います。6月に公開されましたMediumの<a href="https://medium.com/flutter-io/flutter-release-preview-1-943a9b6ee65a?linkId=53249457" target="_blank">Announcing Flutter Release Preview 1</a>というタイトルの記事の中で紹介されている、横軸が時間で縦軸がギッドハブのスター数のグラフを見て頂きたいのですが、
今年の２月末にFlutterのBeta1がリリースされたあと急上昇し、今年のIO以降も50%増加したとのことです。
また、世界中で多くのミートアップが開催されてます。世界50カ国150のイベントが開催されているとのことです。

日本でも東京と大阪で開催されています。大阪で6月に1回開催され、東京では先月で4回目でした。東京のミートアップはオンライン配信して下さっているので、どこでも見ることができます。僕は三回目と4回目にオンライン参加ししましたがとても良いです。勉強にはなるのですが、画面の向こう側にからビールのガチャプシュって聞こえるので、ビール飲みたくなるので注意です。（笑）次回は10月にあるそうなので、できればLTしに行きたいと思っています。

僕が知っているFlutterの勉強会は他にもあって、神戸でこれまで２回開催されてました。
２回目に参加したのですが、小学生がコードラボを参加者の前で説明していたのは衝撃的でした。ちなみに僕は[Flutterあるある](https://kwmt27.net/2018/08/16/flutter-workshop/)というLTをしたので興味がある方は見てみてください。
あと、ちょっと宣伝になってしまいますが、<a href="https://gdgkobe.doorkeeper.jp/events/78737" target="_blank">9月29日に３回目のFlutter勉強会が開催予定</a>です。こちらも興味が出てきた方は参加してみてください。

ぼくも話させて頂くのですが、もう少し開発寄りの話をしたいかなと思っています。
本日の参加者が３０名弱なので、明日になったら埋まってることを期待しています（笑）

まとめとして、

- ドキュメントが豊富で、Flutter Galleryは参考になります。
- Flutterは人気上昇中なので、この波に乗ってみませんか？
- Flutter勉強会も東京や関西で実施されてるので、興味がありましたら参加してみると良いかもしれません。

# おわりに

懇親会とかで、「Flutter触ってみます」という声がちらほら聞けて、目標であった"Fluttterを触ってみたくなること"というのは達成できたかなと思うので、嬉しいかったです！

また、冒頭に新iPhoneの話題をしたのがきっかけで、懇親会に新型iPhoneの話も聞けてよかったです！

反省点としては、

- タイマーを設定しわすれていたこと。
- デモするときのプロジェクタ確認をしていないかったこと。シミュレータが画面に収まらないとか問題があった。
- Nativeにコンパイルされるというところをちゃんと説明できなかった。

機会をくださったモバイルメソッド関係者の皆様、発表を聞いてくださった方々、ありがとうございました！

# 参考
- Flutter
    - https://flutter.io/
- Dart仕様
    - https://www.dartlang.org/guides/language/language-tour
- Announcing Flutter Release Preview 1
    - https://medium.com/flutter-io/flutter-release-preview-1-943a9b6ee65a?linkId=53249457