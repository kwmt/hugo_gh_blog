---
title: '#golang リフレクションを使って関数名を取得するには？'
author: kwmt
layout: post
date: 2013-10-05
url: /index.php/2013/10/05/golang-how-to-get-function-name-with-reflection/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - 1.2 rc1
  - golang
  - Go言語
  - reflect
  - runtime

---
<a href="http://golang.org/pkg/reflect/" target="_blank">&#8220;reflect&#8221;</a>パッケージだけでは取得できないようで、<a href="http://golang.org/pkg/runtime/" target="_blank">&#8220;runtime&#8221;</a>パッケージの`<a href="http://golang.org/pkg/runtime/#FuncForPC" target="_blank">FuncForPC</a>`という関数を使うようです。

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6840466">Gist</a>.
  </noscript>
</div>

## 参考 {.section}

  * <a href="http://bit.ly/1a0Zfsp" target="_blank">reflection &#8211; How to get the name of a function in Go? &#8211; Stack Overflow</a>
  * <a href="http://bit.ly/1fWdHYS" target="_blank" class="broken_link">goken/goken08-reflect.md at master · goken/goken</a>