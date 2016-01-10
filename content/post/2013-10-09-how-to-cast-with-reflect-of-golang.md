---
title: '#golang リフレクションを使って、キャストするには？'
author: kwmt
layout: post
date: 2013-10-09
url: /index.php/2013/10/09/how-to-cast-with-reflect-of-golang/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - 1.2 rc1
  - golang
  - Go言語
  - reflect

---
<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6895772">Gist</a>.
  </noscript>
</div>

<a href="http://play.golang.org/p/gvadP4k1Bo" target="_blank">http://play.golang.org/p/gvadP4k1Bo</a>

<a href="http://golang.org/pkg/strconv/#FormatInt" target="_blank">strconv.FormatInt</a>の第一引数は&#8221;int64&#8243;型で、<a href="http://golang.org/pkg/strconv/#FormatUint" target="_blank">strconv.FormatUint</a>の第一引数は&#8221;uint64&#8243;型です。
  
たとえば、ただ100と書くとint型になりますので、fv.Callの引数のところで、キャストしてあげる必要があります。キャストするには、reflectパッケージの<a href="http://golang.org/pkg/reflect/#Value.Convert" target="_blank">Convertメソッド</a>を使います。

ほとんど同じ処理で、ある一部分だけ異なる関数を使っているという場合に役に立つと思います。

ちなみに、このConvertメソッドは、go1.1のリリース時に追加されました。
  
<a href="http://golang.org/doc/go1.1#reflect" target="_blank">http://golang.org/doc/go1.1#reflect</a>

## 参考

  * <a href="http://bit.ly/1fWdHYS" target="_blank" class="broken_link">goken/goken08-reflect.md at master · goken/goken</a>
  * <a href="http://golang.org/pkg/reflect/#Value.Convert" target="_blank">http://golang.org/pkg/reflect/#Value.Convert</a>
  * <a href="http://bit.ly/1ctDnMp" target="_blank">#golang リフレクションを使って、関数の引数の型を取得するには？ | Androg</a>
  * <a href="http://bit.ly/1ctDtDT" target="_blank">#golang リフレクションを使って、関数をコールするには？ | Androg</a>