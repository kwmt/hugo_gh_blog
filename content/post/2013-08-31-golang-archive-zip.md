---
title: '#golang zipファイルのコメントを取得するには？'
author: kwmt
layout: post
date: 2013-08-31
url: /index.php/2013/08/31/golang-archive-zip/
pdrp_attributionLocation:
  - end
categories:
  - golang
  - python
tags:
  - archive/zip
  - golang
  - Go言語
  - python
  - pythonchallenge

---
archive/zipパッケージの使い方

## golang編 {.section}

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6040283">Gist</a>.
  </noscript>
</div>

下記で実行できる
  
% go run archive_zip.go 90052
  
※引数の数字はchannel.zipにreadme.txtに書いてある。

## python編 {.section}

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6040265">Gist</a>.
  </noscript>
</div>

## 参考 {.section}

<a href="http://golang.org/pkg/archive/zip/" target="_blank">http://golang.org/pkg/archive/zip/</a>