+++
title= "RxJavaのサンプルを作るにあたって、Androidアプリを作ってみた。"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Retrofit", "OkHttp", "RxJava", "Android"]
keywords = [""]
+++

## 最近RxJavaを使い始めたら、めちゃめちゃ便利！
ということを知って、使い方のサンプルを書いておこうと、<a href="https://github.com/kwmt/RxJavaSample" target="_blank">書きました</a>。サンプルとして割りきってしまえば、これはこれでありなんだと思うんですが、実際はいろんなライブラリと組み合わせて使うわけで、たとえばRetrofitと一緒に使うのはどうするんだろうとか思って書き始めていたら、何かアプリを作ってみた方が実用的になるかもと思って、<a href="https://play.google.com/store/apps/details?id=net.kwmt27.codesearch" target="_blank">Androidアプリ</a>を作ってみました。

アプリの詳細はPlay Storeの説明を見ていただきたいのですが、簡単に言うと、リストをGithubAPIから取得して表示し、そのリストの各項目をタップすると詳細がみれるという、よくあるアプリです。


このアプリは、自分の実験アプリで、自分のなかで試してみたいことを試してみてます。

* いままでMVCでコード書いてたのですが、MVPにしてみよう！とか、
* Retrofit使ったことなかったので使ってみようとか、
* RxBinding使ってスクロールの一番下で行ったら読み込んでみよう
* RecyclerViewで一部のitemを異なるviewTypeにしたい
* Firebase使ってみよう！
* wercker CIを使ってみようとか、
* <a href="https://developers.google.com/android-publisher/#publishing" target="_blank">Google Play Developer Publishing API</a>を使ってPlay Storeに自動アップロードしてみようとか、
* Retrolambdaを使わずに<a href="https://developer.android.com/guide/platform/j8-jack.html?hl=ja#supported-features" target="_blank">ラムダ式が使える</a>(一部だけ)ので使ってみようとか。

です。

最後のラムダ式を使うに関して、Retrolambdaを使わずラムダ式を使うには、Jackを有効にしないといけなくて、Jackを有効にすると、 `Instant Run is disabled when Jack compiler is used.` と表示されるようにInstant runが無効になってしまいますので、短く書けるのを取るかコンパイル速度を取るか、悩みどころです。いずれJackでもInstant runができることを期待しているので、コンパイル速度は諦めてます。



## レポジトリ
https://github.com/kwmt/GitHubSearch

## ちなみに、自分がRxJavaを使うと便利になった思うこと
* ModelからViewへの通知を今までObserverパターンを自作していたが、それが不要になった
* 連続して複数回APIを呼ぶなどするとコールバック地獄になるので読みにくくなってしまうのを、1回のコールバックで受けれるので読みやすくなった
* リスト操作やAPIコールをObservable(Stream)として同じように扱える

ただ`new Func1`とか書きたくないから、早く世の中の端末がAndroid N以降になってくれないかな。

## RxJavaの課題というか悩みどころ
* `subscription` はModelで持つべきか、Viewで持ったほうがいいか悩みます。
* リスト操作はfor文使わなくてよくなったけど、全部が全部RxJavaを使うかどうか再利用の関係で悩みます。ユーティリティ系のクラスは別のプロジェクトでも使いまわせたりしますが、RxJavaでリスト操作してしまうとそのユーティリティを使うなら必ずRxJavaの導入が必要になってきます。RxJavaが導入されてないプロジェクトだったりすると、そのユーティリティクラスは使えないので、for文の方がいいんじゃないかと思ってしまいます。


