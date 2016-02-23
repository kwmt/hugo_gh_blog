---
title: '#golang html/template: 構造体メンバにスライスがあるとき、html側でスライスの要素を指定するには？'
author: kwmt
layout: post
date: 2013-01-15
url: /index.php/2013/01/15/golang-htmltemplate-構造体メンバにスライスがあるときhtml側で/
categories:
  - golang
tags:
  - golang
  - Go言語

---
先に答えを書くと、 `index`を使います。 

例えば、goファイル内で次のように構造体のメンバにスライスHotelsを定義した場合、 



```go
type Body struct {
	PagingInfo PagingInfo `xml:"SimpleHotelSearch&gt;paginginfo"`
	Hotels     []Hotel    `xml:"SimpleHotelSearch&gt;hotel"`
}
```

htmlファイルでは、次のように記述します。 

```
[]Hotelsの0番目の要素は{{index .Hotels 0}}です。 <!-- Hotels[0]と同じ意味です。 -->
```

## 参考

text/templateパッケージのFunctions
:   <http://golang.org/pkg/text/template/#hdr-Functions>

text/templateパッケージのテストソース(indexで検索)
:   <http://golang.org/src/pkg/text/template/exec_test.go>

stackoverflow: golang html/template: how to index a slice element?
:   <http://stackoverflow.com/questions/12701452/golang-html-template-how-to-index-a-slice-element>