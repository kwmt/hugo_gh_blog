---
title: '#golang Examplesの仕組みについて'
author: kwmt
layout: post
date: 2013-11-08
url: /index.php/2013/11/08/about_exmaples_construction/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - godoc
  - golang
  - Go言語

---
岡山で開催中のGolang Cafeの参加者の<a href="http://d.hatena.ne.jp/taknb2nch/20131107/1383807901" target="_blank">ブログ記事</a>を見て、Examplesの仕組みが気になって調べた時にGoogle+に投稿した内容です。

<!--more-->

<pre class="go">Examplesの説明はココですね。なるほど。 
<a href="http://golang.org/pkg/testing/#hdr-Examples" target="_blank">http://golang.org/pkg/testing/#hdr-Examples</a> 

"プログラムの例示と、出力結果を示す時に、プログラムの結果が本当に正しいかを確認できるようにしている"というのは、間違いないと思われます。 

というのは、たとえば、 
<a href="http://golang.org/pkg/encoding/json/#example_Marshal" target="_blank">http://golang.org/pkg/encoding/json/#example_Marshal</a> 
ドキュメントにあるこの部分と、 
<a href="http://golang.org/src/pkg/encoding/json/example_test.go#L16" target="_blank">http://golang.org/src/pkg/encoding/json/example_test.go#L16</a> 
testプログラムにあるこの部分がまったく一緒でしたので。 

これだけの理由だと弱いというのと、どのような仕組みでなってるか気になったので、 golang.org を"Examples"で検索してみたところ、ドキュメントのテンプレートを見つけたのですが(1.2では移動してます)、 
<a href="http://golang.org/lib/godoc/package.html#{{$name_html}}" target="_blank">http://golang.org/lib/godoc/package.html#{{$name_html}}</a>
この{{example_html $ .Name}}の部分でxxx_test.goのExampleXxx部分を切り出してdocに出力しているようです。 

example_htmlはFuncMapでテンプレート用に関数を作っていました。 
<a href="https://code.google.com/p/go/source/browse/godoc/godoc.go?repo=tools#84" target="_blank">https://code.google.com/p/go/source/browse/godoc/godoc.go?repo=tools#84</a> 

1.2で移動したpackage.htmlがどこに行ったかですが、 
<a href="https://code.google.com/p/go/source/browse/godoc/static/package.html?repo=tools" target="_blank">https://code.google.com/p/go/source/browse/godoc/static/package.html?repo=tools</a> 
こちらにありました。 

ちなみに調べてて気付いたのですが、"go doc"コマンドが"godoc"に1.2から変わるみたいですね。その変更でpackage.htmlは移動したみたいです。 

以上、そんな感じだと思います。 
いや〜、勉強になりました。
</pre>

とコメントしたら、次のようにコメント頂きました。

<pre class="go">godoc用のサンプルコードに使われるってことなんですね。
</pre>

たぶん、これがしっくりくる説明になるんじゃないかと思います。

#ほとんどG+投稿内容のコピペです(^^ゞ