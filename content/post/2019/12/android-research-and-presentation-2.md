+++
title= "Android研究&発表会#2 開催レポート"
date= 2019-12-06T20:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android研究&発表会"]
tags = ["Android研究&発表会", "勉強会", "Android"]
keywords = [""]
twitterImage = "images/2019/12/android-research-and-presentation-2/arap.png"
+++

## はじめに

2019/10/26(土)に<a target="_blank" href="https://arap.connpass.com/event/148500/">Android研究&発表会#2</a>というAndroidの勉強会を開催したので、レポートです。


## 勉強会の様子
今回は主催の僕1名と参加者1名の合計2名で開催しました。
で、発表者が僕だけでしたので、参加者1名の方に発表するシュールなスタイルとなりましたw




雰囲気です。
<img src="/images/2019/12/android-research-and-presentation-2/presen.png">


僕の発表資料はこちらです。


<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">発表資料 公開しました！<a href="https://t.co/RNnKxTuwl0">https://t.co/RNnKxTuwl0</a> <a href="https://twitter.com/hashtag/arap_osaka?src=hash&amp;ref_src=twsrc%5Etfw">#arap_osaka</a></p>&mdash; yasi kawamoto (@kwmt27) <a href="https://twitter.com/kwmt27/status/1187988081191706626?ref_src=twsrc%5Etfw">October 26, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


### 途中からモクモク会に変更

お気づきかもしれませんが、発表がだいたい20分ぐらいで終わりましたので、もちろん時間が余ります。
そこで何したかっていうと、モクモク会に切り替えました。
事前にconnpass上から、「時間余ったらモクモク会するので、ノートPC持ってきて下さい」と連絡させていただいてて、持ってきて頂いてたのでもくもく会に変更できました。


もくもく会の進め方としては、まずどんなことをもくもくするかいいあって、時間が経ったら最後にどうだったか成果のようなものを軽く発表するという形にしました。これは以前モクモク会だけのイベントをしたときにやってたことでした。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">今日は発表者が僕一人だったため、残りの時間はもくもく会になってますー <a href="https://twitter.com/hashtag/arap_osaka?src=hash&amp;ref_src=twsrc%5Etfw">#arap_osaka</a></p>&mdash; yasi kawamoto (@kwmt27) <a href="https://twitter.com/kwmt27/status/1188006247905124352?ref_src=twsrc%5Etfw">October 26, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


1人の参加者がやっていたことは、Android端末からラズパイだったかな？ちょっと忘れましたが、そういった端末にBloothoothでファイル送信して、送信したら通知がでるのを検知してアプリ上でダイアログとかを出したいとのことでした。

やられてることも興味深かったのですが、通知領域にくる通知を自分のアプリでしることができるというのをしらなかったので、勉強になりました。
ついでにサンプル作ってみたので、ご興味ある方は見てみてください。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">NotificationLisntenerServiceで困ってた人がいたので、サンプル作った！ <a href="https://t.co/hvyEXvwMgR">https://t.co/hvyEXvwMgR</a> <a href="https://twitter.com/hashtag/arap_osaka?src=hash&amp;ref_src=twsrc%5Etfw">#arap_osaka</a></p>&mdash; yasi kawamoto (@kwmt27) <a href="https://twitter.com/kwmt27/status/1188006522015514625?ref_src=twsrc%5Etfw">October 26, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

僕の方は、 Kotlin Multiplatform project(MPP)でAndroidアプリを作っていたので、その続きをしてました。
MPPはAndroidアプリにGoogleMapを表示して、サーバーにあるストアを表示する簡単なものです。
サーバーはKtorを使っています。今回はマップにストアが表示されない不具合中だったので、その改修をしていました。



## 最後に

今回は懇親会はありませんでした。
3回目もすでに終わった状態でこのレポートをかいているので、特に次回の連絡はありませんが、参加者2人以上でいれば開催していきたいと思っていますので、勉強したい方、一緒に勉強しませんか？！