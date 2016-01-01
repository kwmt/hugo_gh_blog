---
title: '#golang 画像ファイルをbase64 encode/decode するには'
author: kwmt
layout: post
date: 2013-09-29
url: /index.php/2013/09/29/golang-base64-encode-decode/
pdrp_attributionLocation:
  - end
fb_author_post_id:
  - 589534447776533
categories:
  - golang
tags:
  - 1.2 rc1
  - encoding/base64
  - golang
  - Go言語

---
ある適当な画像（ここでは&#8221;image.jpg&#8221;）をbase64エンコードした文字列に変換したあと、変換された文字列をデコードして画像ファイル（ここでは&#8221;encode\_and\_decord.jpg&#8221;）を作成する。
  
（同じ画像ファイルが作成されるだけですが&#8230;）
  
<!--more-->

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6750943">Gist</a>.
  </noscript>
</div>

## 参考 {.section}

  * <a href="http://golang.org/pkg/encoding/base64/" target="_blank">encoding/base64パッケージ</a>