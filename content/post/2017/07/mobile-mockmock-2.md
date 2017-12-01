+++
date = "2017-07-30T18:00:00+09:00"
title = "モバもく会 #2 を開催して、Github Trending(java)を軽く読んでみた"
draft = false
categories = ["モバもく会"]
tags = ["モバもく会", "勉強会", "Android"]

+++


## はじめに

<a  target="_blank" href="https://connpass.com/event/62526/" >モバもく会 #2</a>という、モバイルに関連したもくもく会を開催しました。

一人は前回来ていただいた方の話を聞いて行ってみようと思って来てくださったり、
東京中心に活動されてる方がたまたま大阪に来ていて、大阪の宿泊している場所ではやりにくいので、参加してくださったり、
家にいるとスプラトゥーンをやってしまうから、こういうもくもく会はありがたいというお声を頂き、嬉しい限りです。

モバもく会としては、途中で帰られる方がいらっしゃったのでせっかく足を運んで来てくださってるので、帰られる方の話も聞きたいし、その方に対しても何かしら持ち帰ってほしかったので、途中で発表し合うようにしました。

今回みなさんがやったことは次のとおりです。

* 一人は次の勉強会のための資料作成(マテリアルデザイン)
* 一人も次の勉強会のための資料作成（Notificationに関すること）
* 一人はVR
* 一人はお仕事でMonakcaについての調査

僕は、前回に引き続き、2017/07/30の時点の<a target="_blank" href="https://github.com/trending/java?since=weekly">Github Trending(java)</a>の中から2つレポジトリを見てみました。



下記は除外しています。

* SmartRefreshLayoutは前回よんだので除外
* android-interview-questions
* spring-boot




## StormPlane

<a target="_blank" href="https://github.com/HurTeng/StormPlane">https://github.com/HurTeng/StormPlane</a>

飛行機のゲームです。apkをAndroid端末にインストールして遊んだだけですｗ
飛行機を動かすのに、飛行機をタッチしないといけないのですが、指が邪魔で飛行機が全然見えなくて、ちょっとやりにくかったです。

コードとしては、SurfaceViewを使ってゴリゴリ書いてるだけなので、僕にとって特にこれと言って新しいことはありませんでした。

あと、Readmeとかが中国語なのでGoogle翻訳に頼りました。


## PLDroidShortVideo

<a target="_blank" href="https://github.com/pili-engineering/PLDroidShortVideo">https://github.com/pili-engineering/PLDroidShortVideo</a>


snowアプリみたいなことができるライブラリのようです。


起動してスタンプを選択して、顔にカメラを向けると、

<img src="/images/2017/07/mobamock2/PLDroidShortVideo_sample.png" width="300">

このようになり、保存できます。

<img src="/images/2017/07/mobamock2/PLDroidShortVideo_sample_image.png" width="300">


あと動画も撮れるようですが、スタンプを入れたまま動画の作成は出来ませんでした。

これも中国語でした。


## まとめ

中国語のレポジトリがトレンドに入ってる来てるので、中国語も勉強しないといけないと思いましたが、よくよく考えると中国製のライブラリを実運用で使うことは無さそうなので、次からは中国製のライブラリはやめてみようかと思います。


