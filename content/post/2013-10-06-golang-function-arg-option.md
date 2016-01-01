---
title: '#golang 関数の引数をオプション的に扱うには？'
author: kwmt
layout: post
date: 2013-10-05
url: /index.php/2013/10/06/golang-function-arg-option/
pdrp_attributionLocation:
  - end
post_to_facebook_timeline:
  - 1
categories:
  - golang
tags:
  - 1.2 rc1
  - golang
  - Go言語

---
分かりやすい言葉が思いつかなかったのすが、関数の引数をオプション的に扱うというのは、関数の引数をとってもとならなくても良いように関数を定義することです。それには`<a href="http://golang.org/ref/spec#Passing_arguments_to_..._parameters" target="_blank">...</a>`パラメータを使うと良さそうです。

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6840821">Gist</a>.
  </noscript>
</div>

<a href="http://play.golang.org/p/kRLQLIWkxN" target="_blank">http://play.golang.org/p/kRLQLIWkxN</a>
  
ただし、`<a href="http://golang.org/ref/spec#Passing_arguments_to_..._parameters" target="_blank">...</a>`パラメータは、引数の最後でしか使えませんので、注意が必要です。つまり、

<pre class="go">func f(s ...string, i int)
</pre>

のようなことはできないということです。ちなみに、こうすると次のエラーがでます。

<pre class="go">can only use ... as final argument in list
</pre>