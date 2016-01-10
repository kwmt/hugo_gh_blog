---
title: '#golang bzip2データから復元するには？'
author: kwmt
layout: post
date: 2013-08-31
url: /index.php/2013/08/31/golang-compress-bzip2/
pdrp_attributionLocation:
  - end
categories:
  - golang
  - python
tags:
  - compress/bzip2
  - golang
  - Go言語
  - python
  - pythonchallenge

---
compress/bzip2の使い方

## golang編

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6043517">Gist</a>.
  </noscript>
</div>

bzip2.NewReaderするだけで復元できるのですね。
  
関数名が少し微妙な気もするけど、まぁいいか。

## python編

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6043549">Gist</a>.
  </noscript>
</div>

## 参考

<a href="http://golang.org/pkg/compress/bzip2/" target="_blank">http://golang.org/pkg/compress/bzip2/</a>