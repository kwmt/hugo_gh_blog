---
title: '第1回大阪Go勉強会の課題をやってなかったので、いまさらながらやってみた。 #阪Go'
author: kwmt
layout: post
date: 2013-11-19
url: /index.php/2013/11/19/hango-exercises/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - golang
  - Go言語
  - 阪Go

---
課題は、[こちら][1]です。

先生のコードとは少し違かったので、めもっとく。
  
<!--more-->

## FizzBuzz

{{< gist 7540879>}}
<!--TODO: こんな感じにしたい-->
<!--{{< gist id="7540879" file="fizzbuzz.go">}}-->

`switch`のあと何も書かなかったら、`true`っての忘れてた。
  
最初、

<pre class="go">switch i {
case i % 15 == 0:
....
}
</pre>

って書いてエラー出たので、あれ？ってなってた。

## ワードカウント

課題のワードカウントは、解答例見ちゃって、`map`の使い方でふむふむとおもった。

## その日付は何日目？

<!--TODO: こんな感じにしたい-->
<!--{{< gist id="7540879" file="dayofyear.go">}}-->

`time`パッケージはうるう年も考えてあるからラク。testコードは書いてないけど、まぁいいか。

## オライリーの総額計算

<!--TODO: こんな感じにしたい-->
<!--{{< gist id="7540879" file="oreillybook.go">}}-->

goroutineとchannelを使ってやってみたけど、終了処理がなんか微妙な気がするので、もっといい方法あれば教えてください。

 [1]: https://github.com/todoa2c/hango/blob/master/vol01.md