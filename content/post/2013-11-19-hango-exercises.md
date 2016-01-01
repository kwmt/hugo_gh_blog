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

## FizzBuzz {.section}

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/7540879">Gist</a>.
  </noscript>
</div>

`switch`のあと何も書かなかったら、`true`っての忘れてた。
  
最初、

<pre class="go">switch i {
case i % 15 == 0:
....
}
</pre>

って書いてエラー出たので、あれ？ってなってた。

## ワードカウント {.section}

課題のワードカウントは、解答例見ちゃって、`map`の使い方でふむふむとおもった。

## その日付は何日目？ {.section}

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/7540879">Gist</a>.
  </noscript>
</div>

`time`パッケージはうるう年も考えてあるからラク。testコードは書いてないけど、まぁいいか。

## オライリーの総額計算 {.section}

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/7540879">Gist</a>.
  </noscript>
</div>

goroutineとchannelを使ってやってみたけど、終了処理がなんか微妙な気がするので、もっといい方法あれば教えてください。

 [1]: https://github.com/todoa2c/hango/blob/master/vol01.md