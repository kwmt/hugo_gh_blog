---
title: '#golang 文字数をカウントするには？'
author: kwmt
layout: post
date: 2013-10-09
url: /index.php/2013/10/10/how-to-count-number-of-string-golang/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - 1.2 rc1
  - golang
  - Go言語
  - strings
  - unicode/utf8

---
## 文字数をカウントしたい！

そう思ってすぐ思いついたのは、stringsパッケージの<a href="http://golang.org/pkg/strings/#Count" target="_blank">Count</a>関数。

Exampleのあったので、確認のためRunしてみる。
  
なぜか期待する文字数の +1 になる。<a href="http://golang.org/src/pkg/strings/strings.go?#L70" target="_blank">srcを見てみたところ、+1されてる・・・</a>。なんで？

とツイートしたら、ロブ・パイク先生からお返事頂きました！

<blockquote class="twitter-tweet" width="550">
  <p lang="en" dir="ltr">
    <a href="https://twitter.com/yasi_kawamoto">@yasi_kawamoto</a> All strings contain the empty string.
  </p>
  
  <p>
    &mdash; Rob Pike (@rob_pike) <a href="https://twitter.com/rob_pike/status/387996687173550080">October 9, 2013</a>
  </p>
</blockquote>



でも、あれ？ <a href="http://golang.org/pkg/strings/#Count" target="_blank">strings#Count</a>の例にあるけど、

<pre class="go">fmt.Println(strings.Count("cheese", "e"))
</pre>

これは、3と出力しますよね。

<pre class="go">fmt.Println(strings.Count("five", ""))
</pre>

これは、なんで5を出力するの？すべての文字列にempty stringが&#8221;含まれてる&#8221;んなら、1じゃないのかな！？この答えは、たぶんプログラミング言語の基本な気がする&#8230;ちょっと調べてみよう。

とにかく、期待する文字数をカウントするには、Count関数でも使われているunicode/utf8パッケージの<a href="http://golang.org/pkg/unicode/utf8/#RuneCountInString" target="_blank">RuneCountInString</a>関数を使えばよさそうだ。