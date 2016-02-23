---
title: golangでhtmlファイルの一部を別ファイルに分けるには
author: kwmt
layout: post
date: 2012-06-05
url: /index.php/2012/06/05/golangでhtmlファイルの一部を別ファイルに分けるには/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - golang
  - Go言語

---
## まえおき

<a href="http://kwmt27.net/index.php/2012/05/24/golang%e3%81%a7twitter%e3%81%ae%e3%82%bf%e3%82%a4%e3%83%a0%e3%83%a9%e3%82%a4%e3%83%b3%e3%82%92%e3%83%96%e3%83%a9%e3%82%a6%e3%82%b6%e3%81%ab%e8%a1%a8%e7%a4%ba%e3%81%95%e3%81%9b%e3%81%a6%e3%81%bf/" title="golangでTwitterのタイムラインをブラウザに表示させてみた。" target="_blank">前回のエントリー</a>で、「`template.ParseFiles()`の引数には、
  
複数ファイルを指定することができ、
  
たとえば、header.html、main.html、footer.htmlなどに分けることができます。」と書きましたが、
  
具体的にどうしたらよいかをまとめておこうと思います。 

## どうしたいのか

前回と同じHTMLですが、このファイルを`main.html`としましょう。 

```
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<style type="text/css">
    div.tl  {width:600px;}
    table,td {border: solid; border-collapse: collapse;}
</style>
</head>
<body>
<div class="tl">
<table border cellspacing=0>
{{range .}}
    <tr>
    <td>
    日時：{{.Created_at}}<br/>
    ツイート：{{.Text}} <br/>
    スクリーン名：{{.User.Screen_name}}
    </td>
    </tr>
{{end}}
</table>
</div>
</body>
</html>
```

これを、たとえば、ツイッターの内容の部分を別ファイルにするということを考えます。 

<!--more-->

## 対策

まず内容の部分は`<div class="tl">`から`</div>`までなので、それを切り取り別ファイルに移動しましょう。
  
移動先のファイル名は`content.html`とでもしときます。 

```
{{define "content"}}
<div class="tl">
<table border cellspacing=0>
{{range .}}
    <tr>
    <td>
    日時：{{.Created_at}}<br/>
    ツイート：{{.Text}} <br/>
    スクリーン名：{{.User.Screen_name}}
    </td>
    </tr>
{{end}}
</table>
</div>
{{end}}
```

`<div class="tl">`から`</div>`の部分を`{{define "content"}}`と`{{end}}`
  
で挟んでいますが、これは`<div class="tl">`から`</div>`までを`"content"`として定義しますよという宣言です。 

次に、元の`main.html`はどうなるかというと、次のようになります。 

```
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <style type="text/css">
        div.tl  {width:600px;}
        table,td {border: solid; border-collapse: collapse;}
    </style>
</head>
<body>
    {{template "content" .}}
</body>
</html>
```

内容があった部分が`{{template "content" .}}`に変わっていることがわかると思います。
  
この`"content"`は先程`content.html`の
  
`{{define "content"}}`で定義した`"content"`です。 
  
そして、今回の場合だとこの`"content"`のあとに `.(dot)`が必要になります。
  
この`.(dot)`がないと、きちんと`content.html`の内容を表示してくれません。 

最後に、`template.ParseFiles()`の引数に`content.html`を追加します。 

<pre class="brush: golang; title: ; notranslate" title="">t,err:= template.ParseFiles("template/main.html", "template/content.html")
t.Execute(w, tweets)
</pre>

基本的には以上が書きたかったことです。が、もうちょっとだけ。 

## `{{tempalte "content" .}}`の`.(dot)`について

上記で「今回の場合だとこの`"content"`のあとに `.(dot)`が必要になります。」
  
と書きましたが、他の場合は`.(dot)`はいらないのか？というと、いらない場合もあります。 

たとえば、copyrightのような文言を別ファイルにして、メインファイルに読み込ませたいとします。 

```
{{define "copyright"}}
Copyright <img src="http://blog.seesaa.jp/images_e/e/F074.gif" alt="コピーライトマーク" width="15" height="15" border="0" /> 2012 xxxxxx All Rights Reserved.
{{end}}
```


これを`main.html`に読み込ませたいとすると、 

```
<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="UTF-8">
        <style type="text/css">
            div.tl  {width:600px;}
            table,td {border: solid; border-collapse: collapse;}
        </style>
    </head>
    <body>
        {{template "content" .}}
        {{template "copyright"}}
    </body>
</html>
```

と、このようになり、`"copyright"`のあとには`.(dot)`がありません。
  
（もちろん、`template.ParseFiles()`の引数に`copyright.html`を追加してください。）
  
つまり、別ファイルにした際、`.(dot)`や`.Screen_name`などがあれば、`{{template "content" .}}`
  
のように`.(dot)`をつけ、そのような変数がなければ、`{{template "copyrighte"}}`のように`.(dot)`無しとして使います。
  
(今のところそういう認識) 

以上です。 

## 参考

* [text/templateパッケージ](http://golang.org/pkg/text/template/)

* templateのdefineについて:   [http://golang.org/pkg/text/template/#Nested\_template\_definitions]

* {{tempalte &#8220;name&#8221;}}と{{template &#8220;name&#8221; pipeline}}:   <http://golang.org/pkg/text/template/##Actions>

* Field substitution in nested templates(このMLで.(dot)が必要だとわかった&#8230;):   <https://groups.google.com/group/golang-nuts/browse_thread/thread/4838863461eaaae0?pli=1>

