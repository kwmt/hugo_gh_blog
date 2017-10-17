+++
date = "2017-10-16T15:00:00+09:00"
title = "モバもく会#6 を開催"
draft = false
categories = ["モバもく会"]

+++

2017/10/15(日)に<a target="_blank" href="https://mobamoku.connpass.com/event/68548">モバもく会#6</a>という、だいたい２，３週間に１回やっているモバイルに関連したもくもく会を開催しました。

今回は3人でした。毎回ありがとうございます！自分が毎回勉強させてもらっています（感謝）

今回みなさんがやったことは次のとおりです。


* CircleCI2.0についての調査
* COSMOのAndroid SDKを使ってみる

聞いた話ですが、[FIDO](https://www.cloudgate.jp/lineup/uno/fido_u2f.html)というものがあるらしいですね。

僕はクリーンアーキテクチャの[サンプル](https://github.com/android10/Android-CleanArchitecture) を参考に自分なりに実装しようとしました。

* 参考:[Architecting Android...The clean way?](https://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/)


しようと思ったのですが、DIを実現するためにdaggerを使っているといろいろハマってしまって、それに時間取られてしまいました。。。

結論：[公式ドキュメント](https://google.github.io/dagger/android.html)をちゃんと読め！ということでした。

Daggerについては、[別記事](http://kwmt27.net/2017/10/17/dagger2-android-viewmodel/)に書きました。


以上です。

