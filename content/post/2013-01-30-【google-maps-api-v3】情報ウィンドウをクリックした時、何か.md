---
title: 【Google Maps API v3】情報ウィンドウをクリックした時、何か処理させるには？
author: kwmt
layout: post
date: 2013-01-30
url: /index.php/2013/01/30/【google-maps-api-v3】情報ウィンドウをクリックした時、何か/
pdrp_attributionLocation:
  - end
categories:
  - Google Maps API

---
## はじめに

Google Maps API v3を触っていて、マーカーをクリックしたときに何か処理させる方法は結構書かれているのですが、
  
情報ウィンドウ(google.maps.InfoWindow())をクリックした時に何か処理させる方法が日本語ではあまり見つけれれなかったので、メモします。 

## したいこと

ここで一番やりたいことはタイトルどおりですが、下記のプログラムで何をしたいかを書いておくと、 

  1. マップのロード時に複数の地点（ここでは4地点）にマーカーを置く
  2. それぞれのマーカーをクリックすると、情報ウィンドウを表示させる
  3. 情報ウィンドウに書かれている文字列をクリックすると、そのクリックした位置の周りがよく見えるようにある程度ズームする

と、こんな感じです。 

{{< gist 5280009 >}}

## 参考

上記のプログラムは、[stackoverflow][1]をめっちゃ参考にしてます。このstackoverflowではInfoBoxの例もあります。InfoBoxとは、情報ウィンドウのように吹き出しタイプではなくて、
  
単なる四角のテキストボックスのようなものを作れるライブラリです。

 [1]: http://stackoverflow.com/questions/12102598/trigger-event-with-infowindow-or-infobox-on-click-google-map-api-v3