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

## golang編

{{< gist 6040283 >}}

下記で実行できる
  
<pre class="terminal">% go run archive_zip.go 90052</pre>
  
※引数の数字はchannel.zipにreadme.txtに書いてある。

## python編

{{< gist 6040265 >}}

## 参考

<a href="http://golang.org/pkg/archive/zip/" target="_blank">http://golang.org/pkg/archive/zip/</a>