---
title: 'Intellij IDEAで #golang を使えるようにする。'
author: kwmt
layout: post
date: 2014-09-10
url: /index.php/2014/09/10/how-to-use-golang-with-intellij-idea/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - golang
  - Go言語
  - Intellij IDEA

---
ひさびさGoの記事を書く気がします。いままで、どちらかというとIDEなんて不要だ！秀丸で十分だ！ｗ と思ってた人でしたが、Intellij IDEAは素晴らしい！ので、Intellij IDEAでGo言語を使えるようにする設定を書いておこうかと思います。 

  * Intellij IDEA version:13.1.2
  * go-lang-idea-plugin version:0.9.15.3
  * golang version:1.2 darwin/amd64

<!--more-->

## 1.Plugin(Go language(golang.org) support plugin)をインストール

①Intellij IDEAを起動 -> Welcome to Intellij IDEAConfigure -> Plugin ->　Browse repositories&#8230;をクリック
  
②&#8221;golang&#8221;で検索 -> Go language(golang.org) support pluginをインストール

<img class="aligncenter size-thumbnail wp-image-1266" alt="intellij_golang_plugin_01" src="http://kwmt27.net/images/2014/09/intellij_golang_plugin_01-396x300.png" width="396" height="300" srcset="http://kwmt27.net/images/2014/09/intellij_golang_plugin_01-396x300.png 396w, http://kwmt27.net/images/2014/09/intellij_golang_plugin_01-300x227.png 300w, http://kwmt27.net/images/2014/09/intellij_golang_plugin_01-1024x775.png 1024w, http://kwmt27.net/images/2014/09/intellij_golang_plugin_01-624x472.png 624w, http://kwmt27.net/images/2014/09/intellij_golang_plugin_01-900x681.png 900w" sizes="(max-width: 396px) 100vw, 396px" />

## 2.SDKを設定

①Configure -> Project Defaults -> Project Structureをクリック
  
②左側のSDKを選択して、真ん中側の上部のプラスをクリックして、新しいSDKを追加します。このとき、`/usr/local/go`を指定（僕はソースからインストールしてます。homebrewだと違うみたいです。）

<img class="aligncenter size-thumbnail wp-image-1267" alt="intellij_golang_plugin_02" src="http://kwmt27.net/images/2014/09/intellij_golang_plugin_02-394x300.png" width="394" height="300" srcset="http://kwmt27.net/images/2014/09/intellij_golang_plugin_02-394x300.png 394w, http://kwmt27.net/images/2014/09/intellij_golang_plugin_02.png 732w" sizes="(max-width: 394px) 100vw, 394px" />

## 3.Goプロジェクトを新規作成する

New Projectから「Go」を選択 -> Next -> Next -> プロジェクト名(たとえば、hello)を入力 -> Finishを押すと、`Hello world!`を出力する`src/hello.go`ができます。 

## 4.実行する

ファイルを右クリックして、「Run &#8216;hello.go&#8217;」をクリックすれば、実行できます。 

ただ、Goプロジェクト全体を実行するには、どうしたらいいのかな。。。Run -> Run Configurationの&#8221;Arguments:&#8221;に、リンクしたいファイルを指定すれば実行できるけど、いちいち指定しないとダメなのか？また分かったら更新します。 

## 参考

・<a href="http://bit.ly/1orlCPC" target="_blank">go-lang-idea-plugin</a>