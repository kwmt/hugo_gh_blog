+++
categories = ["日記"]
date = "2018-12-31T14:30:00+09:00"
tags = ["振り返り", "日記"]
title = "2018振り返り"

+++


[昨年](/2017/12/31/looking-back-2017/)に引き続き、今年で6回目の振り返りを書いておこう。

# 昨年の目標達成状況

- :x: CraftBeerのサイトは満足するまで作る！
    - 満足はしてないですが、たまに見てこれでいいかとも思っていたところもあり、ほとんど手をつけれてないです。ただ現在見直しているところで、Go Firebase Nuxtjsとかで作りなおす予定です。
- :x: → :white_check_mark: モバもく会つづける
    - 今年は[1回だけ開催](https://mobamoku.connpass.com/event/76726/)しました。それ以降できなかった理由はオフィスが耐震工事で土日が使えなかったのが大きな理由です。
    - 来年はやっていきたい。
- :x: → :white_check_mark: AnroidはAndroid Architecture Componentをさわる
    - DroidKaigiにコントリビュートするために見たぐらいで、ガッツリさわってはいない。
    - 来年はAACのViewModelあたりから触っていきたい
- :white_check_mark: ハーフマラソン時間内に完走する
    - 高槻シティハーフマラソンを2時間12分10秒で完走した！
- :x: → :white_check_mark: ストレッチする
    - たま～にランニングする前にストレッチするぐらい
- :x: 簿記やるか
    - 本買ってから開いてない・・・
    - 来年はたぶんやらないと思う。

※ :x: → :white_check_mark: となってるところは、自分的にはできてないと思ったけれど、少しでもやれてるから○だ！と指摘されて :white_check_mark: に変えたところです。


# 仕事関連

前半は私用も含めて東京に行く回数が多かった気がします。
新年明けてからすぐの1月は東京でクラフトビール巡るために5件回りました。このとき始めて鎌倉の大仏を見に行きました。
その翌週にも仕事の打ち合わせで東京に行き、そこでもYONA YONA BEER WORKSに行きました。


1月の始めの方は、Mアプリ(M社)のセカンドフェーズがキックオフになりました。また同じ頃Fアプリ(S社)もキックオフです。
Mアプリの方はネイティブ+WebViewのハイブリッドアプリで、Fアプリの方はWebViewです。Fアプリはネイティブ担当なのでそれほど工数はかからないのですが、Mアプリが大変でした。結局6月ぐらいまでかかりました。
これはClean Architecture+DDDで設計したのですが、ドメイン領域のクラス名をAndroid、iOS両方とも同じ名前にするは、両方開発しているとありだなと思いました。Android開発していて、iOSを触ることになったら同じクラス名なのですぐジャンプしやすく効率よいと感じました。ただ認識あわせが大変ではありますが。

2月下旬、3月は少しNアプリ(WebViewアプリ)案件が入りました。こちらもネイティブ担当です。

4月はAアプリの改修が少し1週間ぐらいの作業をやりました。ガッツリネィティブです。また今もまだやっているTアプリ案件のキックオフもこの時期です。東京に打ち合わせしに行きました。いろいろあって作業自体は6月ぐらいから着手しています。

このころもまだMアプリをやっています。Google IOに行ったときにも定例打ち合わせがあったのでIO会場から定例に参加したことが懐かしい感じです。

5月ぐらいからSアプリ案件でAndroidアプリ開発がありました。ベースは別の人が作っていて僕は途中から参加しました。
Rxだらけでしたｗ

Tアプリはまだ作業中なのですが、Camera2ApiやOpenCVなど今までにあまり経験がないことをやっていて、大変ですが楽しくやっています。

大きく分けると1〜6月はMアプリ6月〜12月はTアプリという感じでした。とこどころ1,2週の作業が入ったりしてましたが、お概ねそんあ感じです。あ、5,6月ぐらいにSアプリも少しあったのですが、Webの改修もありましたね。Nuxtjsをちょっと触ったり、PHP]ちょっと触ったり。

また、今年は会社で1,2週1回社内勉強会するようになりました。最初は読書会で、それが終わったらFirebase研究したり、Flutterをみんなで触ってみたり、仕事ではやらない楽しそうなことをやっていきました。これは気分転換にもなり普段みんなが考えてることなどを聞けるので、非常によいです。これは来年も続けて行きたいです。

来年は春ぐらいまではTアプリが続きそうです。

## Flutterに関して

[Qiita](https://qiita.com/kwmt@github/items/2e81b46d62beb091d18b)にも書いたのですが、今年の後半からFlutter三昧でした。

今年は1つアプリをリリースしました。

<a href='https://play.google.com/store/apps/details?id=com.instantonnection.app&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1'><img width="300" alt='Google Play で手に入れよう' src='https://play.google.com/intl/en_us/badges/images/generic/ja_badge_web_generic.png'/></a>

iOSは審査中です。

## 勉強会

* 1月 
    * [モバもく会#11](https://mobamoku.connpass.com/event/76726/)
    * [【京都】LINE Bot開発者 新年会！](https://line.connpass.com/event/75147/)
* 2月
    * [DroidKaigi 2018](https://droidkaigi.jp/2018/)
        * 今年もDroidKaigiアプリにコントリビュートしました。去年に比べてコントリビューターが多くあまりコミットできませんたが、細かいのを[3つ](https://github.com/DroidKaigi/conference-app-2018/pulls?q=is%3Apr+is%3Aclosed+author%3Akwmt)ほどPRしました。もちろん本編にも参加。とてもいい刺激をもらいました。
    * [Cookpad TechConf 2018](https://cookpad.connpass.com/event/73971/)
        * これはDroidKaigi週の次の日だったので申し込んだのですが、キャンセル待ちのまま動かなかったので行ってないです。来年はあるのかな？
* 3月 
    * [【大阪】LINE Developer Meetup #31](https://line.connpass.com/event/81287/)
* 4月 
    * [Osaka Mix Leap Study #11 - スマホアプリ勉強会](https://yahoo-osaka.connpass.com/event/79888/)
        * ここでAndroid oreo対応についてLTさせて頂きました。[そのときのブログ](https://kwmt27.net/2018/04/21/push-notification-for-android-oreo/)
* 5月
    * [Google I/O 2018](https://events.google.com/io/)
        * 長年ずっと行きたかったGoogle I/O 2018に初参加しました！すべてが刺激的すぎてここでは書ききれませんが、Google I/Oに限らず海外カンファレンスに参加したいなと思いました！そのためには年収アップしなくては！！またIO旅行記事は[1日目](https://kwmt27.net/2018/05/27/googleio-trip-1-20180504/)しか書いてない。。。来年そうそうには続き書くか。
    * [Google I/O 2018 報告会 関西 in 京都](https://gdgkyoto.connpass.com/event/85929/)
        * 報告会の10分枠で登壇しました！[ブログ](https://kwmt27.net/2018/05/19/google_io_2018_report/)
    * [【増枠しました！】京都Devかふぇ #1 〜モバイル〜](https://kyoto-dev-cafe.connpass.com/event/85513/)

* 6月
    * [umeda.apk #4 - Report from Google I/O 2018](https://shibuya-apk.connpass.com/event/88738/)
        * Google I/Oの他の参加者のまとめを聞きつつ、自分はLTさせていただきました。面白かったと言ってもらえたので満足です！(そのときのブログは[こちら](https://kwmt27.net/2018/06/09/umeda-apk/))
    * [【増枠】Osaka Mix Leap Study #16 - Android Jetpack 勉強会](https://yahoo-osaka.connpass.com/event/89865/)
        * Navigation Architecture Componentについて発表させていただきました。
    * [【増枠しました！】京都Devかふぇ#2 〜WWDC & Google IO総復習会〜](https://kyoto-dev-cafe.connpass.com/event/89875/)
        * 前日にJetpackの勉強会で登壇して、次の日だったので同じ内容のNavigationについて発表させていただきました。
    * [Flutter Meetup Osaka #1](https://flutter-jp.connpass.com/event/89623/)
        * このころからFlutterを触り始めました。
    * [Build Actions for GDG Kyoto](https://gdgkyoto.connpass.com/event/91160/)
        * Google HOMEアプリを作って審査に出しました！結果は審査落ちた連絡が来ていたけど、そういえば対応してないな。。なんかカテゴリが不適切なだけだったような。対応しよ！
* 7月 
    * [【7/13(金) 大阪】〜クラスメソッドのモバイル開発を知る!〜全5回 #1 Android開発編](https://classmethod.connpass.com/event/93016/)
    * [Flutter Meetup Tokyo #3](https://flutter-jp.connpass.com/event/90790/)
        * こちらはライブ配信視聴枠で参加しました。発表自体はとても参考になります。交流できなのが残念なのですが。来年は発表しに行きたい！
* 8月 
    * [【大阪】Bot Developers Meetup - LINE Bot開発者の勉強会](https://line.connpass.com/event/95135/)
    * [Flutter勉強会 (8/12)](https://gdgkobe.doorkeeper.jp/events/77618)
        * FlutterについてLTさせていただきました。[ブログ](https://kwmt27.net/2018/08/16/flutter-workshop/)
    * [Osaka Mix Leap Study #22 - 体験!Yahoo! JAPAN流アナウンス訓練](https://yahoo-osaka.connpass.com/event/96913/)
        * IT以外の勉強会でした。話し方、もっと練習しないと！
    * [Flutter Meetup Tokyo #4](https://flutter-jp.connpass.com/event/95835/)
        * こちらもライブ配信視聴枠で参加しました。#3も#4もどちらも片手間な感じで聞いてるんじゃなくてくて、ガッツリ見ています。
* 9月
    * [モバイルメソッド大阪 第2回](https://classmethod.connpass.com/event/97832/)
        * こちらではIntro to Flutterというタイトルで発表させていただきました。Flutterやってみようと言ってくれる人がいて目標達成できたので良かったです！[ブログ](https://kwmt27.net/2018/09/15/intro-to-flutter/)
    * [DevFest Kyoto 2018](https://gdgkyoto.connpass.com/event/99527/)
    * [Flutter勉強会 (9/29)](https://gdgkobe.doorkeeper.jp/events/78737)
        * 「AndroidやiOSのアプリ開発でいつも私がやっていることをFlutterでやってみた」というタイトルで発表させていただきました。[ブログ](https://kwmt27.net/2018/09/30/try-develop-like-native-development-with-flutter/)
* 10月 
    * [Flutter Meetup Tokyo #5](https://flutter-jp.connpass.com/event/101447/)
        * こちらもライブ配信視聴枠で参加です。

* 11月 
    * [京都】LINE Developer Meetup #46](https://line.connpass.com/event/106318/)
    * [Mobile Act OSAKA #7](https://mobileact.connpass.com/event/99908/)
    * [DevFest 2018 GDG神戸 Flutterハッカソン＆コードラボ ＠さくらインターネット大阪本社](https://gdgkobe.doorkeeper.jp/events/81699)
        * 小学生含む4人で1つのアプリを作りました。4画面あるうちの僕は1つの画面とAPIから取得するところと担当しました。チームがビルドエラーなどになったときに調べるということもわりとやりました。PRは[こちら](https://github.com/gdgkobe/ITcalendar/pulls?q=is%3Apr+is%3Aclosed+author%3Akwmt)
    * [GDG温泉 in 湯布院 DevFest2018](https://gdg.connpass.com/event/98013/)
        * こちらは別名Flutter温泉です。二泊三日大分に行ってきました。温泉最高でした！
* 12月 
    * [SDLアプリコンテストに応募しようぜハッカソン](https://mashupawards.connpass.com/event/106667/)
        * 2日間のハッカソンでした。ハッカソンも楽しかったけど、2日目のBBQ良かった！
    * [GDG神戸 2018年忘年会](https://gdgkobe.doorkeeper.jp/events/83292)
        * 忘年会です。

## 統計

- 登壇回数:8回
- 勉強会参加回数:30回

# 来年の目標

* 年収を倍にする！
    * 12/30に焼き肉に一緒にいったお二人にいい刺激も頂いて、お金で買える命もあるということを教えて頂き、今後結婚や子供ができたときのために稼いでおきたいと本気で思いました。
    * どうやって上げるかを考えないといけないが、なんにせよ早く動き出す必要がありそうだ。
* 英語をスムーズに話せるようなる！
    * ゆっくりの英語や片言レベルでは話せるが、早いと聞き取れなかったり語彙というが表現がないため、ぱっと話せない。これをなんとかしたい。週１，２ぐらいでレアジョブをやっていたが来年は少なくとも2倍以上は回数を増やそう！
* 海外カンファレンスに参加したい！
    * Google I/OやFlutter Live、Firebase summitなど世界各地で開催される海外カンファレンスに参加したいです。海外いくだけならいいのですが、そこでの生活費（宿泊や移動費など）が結構かかるので、やはり稼がないと厳しそうです。
* お腹を引っ込める！
    * 学生の頃の自分の写真が出てきたので見ていたら腹筋が割れてました！たしかにそのころは腹筋をやっていたので、そのころに戻ることを意識してまずは腹筋をやること始めたいと思います。
* クラフトビールに関するアプリを作る
    * 現在趣味がクラフトビールを飲むことなので、その美味しさをもっと知ってもらいたいので、クラフトビールに関するアプリ(Webアプリかも)を製作中で、それを１，2月中には形にして世の中に出したいと思ってます。

* (2019/01/01追記) 自己評価を高くする！
    * :x: → :white_check_mark: に変えたことに関係しますが、これをやらないと年収が上がるものも上がらないですよね！

# まとめ

今年はGoogle IO行ったというのもあって、発表する機会を積極的に作りました。基本的には発表しませんか？というお誘いは断ってないです。プラスLTも自主的に申し込みました！
発表するといろいろ知らないことなどを逆に教えて頂いたりするので、来年も引き続き登壇できるように意識をしたいと思います。

ということで、今年もお世話になったみなさまありがとうございました。良いお年をお迎えください。来年も引き続きよろしくお願いします。

