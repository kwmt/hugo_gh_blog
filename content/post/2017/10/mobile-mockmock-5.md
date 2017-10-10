+++
date = "2017-10-10T21:10:00+09:00"
title = "モバもく会#5 を開催"
draft = false
categories = ["モバもく会"]

+++

10月になってしまいましたが、2017/09/24(日)に<a target="_blank" href="https://connpass.com/event/66127/">モバもく会#4</a>という、だいたい２，３週間に１回やっているモバイルに関連したもくもく会を開催しました。

今回は5人でした。毎回ありがとうございます！自分が毎回勉強させてもらっています（感謝）

今回みなさんがやったことは次のとおりです。


* Enumのメモリ消費についての調査
* Spark(分散処理)の事始め
* iOSのUITableViewのHeightが異なるときに対応方法についての調査＆実装
* 以前に開催されたAndroid Thingsハッカソンの続き

僕は、support design libraryの[BottomSheetのサンプル](https://github.com/android/platform_frameworks_support/blob/master/samples/SupportDesignDemos/src/com/example/android/support/design/widget/BottomSheetModalBase.java) を読んで[実際に試してみました](https://github.com/kwmt/SupportDesignSample/blob/master/app/src/main/java/net/kwmt27/supportdesignsample/BottomSheetModalActivity.java)。

実装コードまで読めてないのですが、軽くマテリアルデザインガイドラインを軽く呼んでみて、DeepLinkについて気になったので実装を試してみました。

主に参考にしたブログは、[Bottom sheet everything](http://www.hidroh.com/2016/06/17/bottom-sheet-everything/)を参考にしました。

DeepLinkは、「こんな簡単な実装で、こんなことができるんですよ」といいたかったんですが、実際はバックの黒透明の背景（ダイアログの背景のみ）が出るだけで、内容がでるところまで出来ませんでした。。。（この日はちょっといろいろありまして。。。←言い訳^^;）





