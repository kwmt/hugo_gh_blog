+++
categories = ["日記"]
date = "2017-12-31T18:30:00+09:00"
tags = ["振り返り", "日記"]
title = "2017振り返り"

+++


[昨年](/2016/12/31/looking-back-2016/)に引き続き、今年で5回目の振り返りを書いておこう。


# 仕事関連

1月の始めの方は、AndroidのGoogleMapの不具合に悩まされました。Mapをグリグリ動かすと、アプリが落ちるのです。。
[issue](https://code.google.com/p/gmaps-api-issues/issues/detail?id=10604) に挙がっていたので、ライブラリの不具合だろうと思われます。まだ解決していないのは気になりますね。これの詳細は、[Github](https://github.com/techvein/GoogleMapsApiStackOverflowError)に書いてます。

1月中旬ぐらいからMアプリの改修案件です。2016年に引き続きご依頼頂けるのはやはり嬉しいものです。
2月終わりぐらいまではMアプリをメインに、Aアプリ(A社)とAアプリ(I社)の改修案件が少しずつありました。

3月は、IoT案件でした。温度湿度をAndroid TV(Nexus Player)で表示するというシンプルな仕様でしたが、めちゃめちゃ楽しかったです。
電子パーツを買いに大阪日本橋に行きまくりました。DroidKaigiがあって東京に行ったのですが、そのときも秋葉原にパーツを求めに行きました。仕事とは関係ですが、秋葉原に行ったときにお昼を食べた[丸五](https://tabelog.com/tokyo/A1310/A131001/13000379/)というとんかつやさんは美味しかったです。並びましたが。
IoT案件は4月中旬ぐらいまで続いたようです。

4月始めの頃よりMaBeeeアプリを担当させて頂きました。会社としてはAndroid,iOSを担当し、僕はAndroidを担当しました。
アプリは[こちら](https://play.google.com/store/apps/details?id=jp.novars.mabeee.train)です。
なかなかアプリっぽくないUIだったので、UI作成は苦労しましたね。。他にこのようなUIを開発している会社さんにノウハウ聞いてみたいです。
5月下旬までやってました。

5月中旬ぐらいから、Aアプリ(A社)のiOS開発を担当しました。3人で開発。その内2人が新規画面作成のUIのみ担当するという体制でした。iOSをUIのみ担当といってもUITableViewなんかはdelegateを実装しなくちゃならので、結局Storyboard＋ViewControllerまで担当しました。
こちらは6月初旬まで。

6月中旬ぐらいから[ポケットアブストラクト](https://itunes.apple.com/jp/app/%E3%83%9D%E3%82%B1%E3%83%83%E3%83%88%E3%82%A2%E3%83%96%E3%82%B9%E3%83%88%E3%83%A9%E3%82%AF%E3%83%88/id1247374160?mt=8) というiOSアプリの新規開発を担当しました。こちらは1人で担当しました。
一からiOSを担当するのは初めてでしたが、MVVMのような設計にしたり、チャレンジさせてもらった案件でした。
待ちもあったりして8月ぐらいまで続きました。

6月ぐらいから社内でアプリを作ろうぜという話があって、アイデア出しからちょっとずつ動いていたのですが、7月8月はこのネイティブ側も触ってました。WebViewなのでそれほどやることは無いのですが、Androidのカメラまわりが大変でした。。

9月からはマイナンバーカードを使ったAndroidアプリの作成を担当しました。マイナンバーカードが必要なので、申し込んで半月ほどで届きました。だいたい9月一杯で終わりました。

10月はMアプリや社内アプリ、Kアプリ(R社)をやりました。Kアプリは既存の改修でしたね。
またMアプリの新規開発案件で、Clean Architectureを採用して設計することになり、Clean Architectureについていろいろ調べたりソース読んだりしました。AndroidはKotlinです。僕自身は初めてのKotlinでした。
基本的な設計は完了したのですが、第二弾として1月からがっつり担当することになる予定です。


11月と12月はMアプリとAアプリ(A社)でした。
Aアプリは2年前から担当させて頂いてるのですが、改修する度に思います。一から作りたい、と。

今年は、特に設計の年でしたね。
2月ごろからDroidKaigiの公式アプリにContributeしたりしていたら、MVVMに興味を持ち始め、今年担当したアプリはほとんどMVVMになってます。Clean Architecture(+MVVM)にもガッツリ触れたのでどこかでまとめたいですね。



## 勉強会

* 1月 
    * [オオサカンEXPO](https://www.osakan-space.com/osakanexpo)
        * 展示しました。とくに[IoT](https://www.tech-vein.com/blog/iot/office-open-bot)のやつは皆さんに興味を持って見ていただけていたと思います。
* 2月
    * [[関ジャバ] フロントエンド初心者勉強会 in 大阪](https://kanjava.connpass.com/event/49104/)
        * 少し趣味でWebアプリを作成していたので、気になって参加しました。この時作っていたものは中断しちゃいましたが、別のWebアプリケーションを作成中です。
* 3月 
    * [DroidKaigi 2017](https://droidkaigi.connpass.com/event/43942/)
        * 2月ぐらいから公式アプリのContributeしてたから、この日だけじゃなくて、2月ぐらいから自分の中ではDroidKaigiに参加していた感じでした。今年は公式アプリの実装を参考にしまくりました。
* 4月 
    * [umeda.go](https://umedago.connpass.com/event/53002/)
        * Goほとんど使ってないので、近況を聞きに来た感じ
    * [クラスメソッドの開発を知る！全7回勉強会 〜第3回 Swiftのアツさを語る！〜](https://classmethod.connpass.com/event/53615/)
        * 設計の話は面白かったですね。MVPでした。
    * [そうだ Go、京都。](https://go-kyoto.connpass.com/event/55599/)
        * 当日申し込んで当日LT資料作って、ひさびさLTしました。
* 5月
    * [AndroidWear2.0 ハンズオン](https://gdgkobe.doorkeeper.jp/events/60100)
        * [ハンズオンしたリポジトリ](https://github.com/kwmt/AndroidWearHandsOn)
* 6月
    * [umeda.apk #3 - Report from Google I/O 2017](https://shibuya-apk.connpass.com/event/58764/)
        * Google I/Oのことを聞きに。
* 7月 
    * [GDG神戸 VR Labs](https://gdgkobe.doorkeeper.jp/events/60101)
        * こちらは会場提供です。初めて会場提供しました。
    * [モバもく会#1](http://kwmt27.net/2017/07/20/mobile-mockmock/)
        * 僕主催のもくもく会です。
    * [モバもく会#2](http://kwmt27.net/2017/07/30/mobile-mockmock-2/)
* 8月 
    * [モバもく会#3](http://kwmt27.net/2017/08/13/mobile-mockmock-3/)
    * [【大阪開催】マテリアルデザインガイドライン輪読会 #4](https://kansai-material-design-guideline.connpass.com/event/62761/)
* 9月
    * [モバもく会#4](http://kwmt27.net/2017/09/19/mobile-mockmock-4/)
    * [iODS 2017](https://passmarket.yahoo.co.jp/event/show/detail/01ig9vz09e3i.html)
        * 朝までiOSDCのやつはしんどかったな。。。あれは行くのやめよう。
    * [モバもく会#5](http://kwmt27.net/2017/10/10/mobile-mockmock-5/)
* 10月 
    * [【大阪開催】マテリアルデザインガイドライン輪読会 #4](https://kansai-material-design-guideline.connpass.com/event/68172/)
    * [モバもく会#6](http://kwmt27.net/2017/10/16/mobile-mockmock-6/)
    * [関西Javaエンジニアの会(関ジャバ) '17 10月度](https://kanjava.connpass.com/event/68169/)
        * DDDに関する勉強会でした。ScalaのEither型が参考になりました。
    * [「正しいものを正しくつくる」とは何か(大阪開催)](https://guildworks.doorkeeper.jp/events/64730)
* 11月 
    * [モバもく会#8](http://kwmt27.net/2017/12/01/mobile-mockmock-8/)
* 12月 
    * [モバもく会#9](https://mobamoku.connpass.com/event/73662/)
    * [Mobile Act OSAKA #2](https://mobileact.connpass.com/event/68892/)
        * 香川から発表しに来られた方と話し込んでたら終電逃して、梅田から難波まで歩きました。。
    * [モバもく会#10](https://mobamoku.connpass.com/event/73680/)
      

### モバもく会について

1回だけ台風で中止しましたが、大体２，３週間間隔で10回続けることができたのはよかったかなぁと。
他の勉強会に行っても結構モバもく会知ってもらえてるようで、「知ってますよ」とか「行きたいんですけどねぇ」とか言われますｗ

また来年も続けて行きたいと思います。

### 個人アプリについて

* [CodeSearchアプリ](https://github.com/kwmt/CodeSearch-kotlin)をKotlin＋Clearn Architecute化してる途中で止まってます。。
* iOSでTodoアプリを作ってたけど、止まってます。。
* 現在はCraftBeerの最新情報の一覧が見れる[SPA](https://craftbeer-loves.com/)を作成中。

### 体について

* ジムを1年間続けたが8月にやめた。
* 12月にハーフマラソン走ったが、時間内に完走できなかった。。
    * 心肺は余裕だったけど、左太ももの外側が痛くなったのが大きな原因。


# 今年の目標はどうだった？

iOSをやりたいってのは、仕事とプライベートで達成できたかと思います。
GoでWebサービスはできてないですが、別のものをWebサービス作成中です。

# 来年の目標

* CraftBeerのサイトは満足するまで作る！
* モバもく会つづける
* AnroidはAndroid Architecture　Componentをさわる
    * DroidKaigiに公式アプリでさわれそう
* ハーフマラソン時間内に完走する
* ストレッチする
* 簿記やるか



# まとめ

ということで、今年もお世話になったみなさまありがとうございました。来年も引き続きよろしくお願いします。（ってこのまとめ、去年と一昨年一緒です（汗））


