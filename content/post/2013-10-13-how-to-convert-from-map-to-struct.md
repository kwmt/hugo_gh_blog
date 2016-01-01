---
title: '#golang mapからstructに変換するには？'
author: kwmt
layout: post
date: 2013-10-12
url: /index.php/2013/10/13/how-to-convert-from-map-to-struct/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - 1.2 rc1
  - encoding/json
  - golang
  - Go言語
  - map
  - reflect
  - struct

---
## reflect pkgを使った方法 {.section}

最初はなんとなくリフレクションを使ってやるのかなぁと思って、調べてたらこんな感じのが出てました。（<a href="http://bit.ly/1axEplM" target="_blank">参考リンク</a>）

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6938201">Gist</a>.
  </noscript>
</div>

<a href="http://play.golang.org/p/5Inl2jO7zL" target="_blank">http://play.golang.org/p/5Inl2jO7zL</a>

<!--more-->

## encoding/json pkgを使った方法 {.section}

ただちょっとこれだと扱いにくくて(MapToStructの中身を修正すれば扱いやすくなるかもしれないですが)、パフォーマンスを気にしなければ、mapを一旦JSONにして、そのJSONをstructにするという方法が扱いやすいということを教えてもらいました。

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kwmt/6950666">Gist</a>.
  </noscript>
</div>

<http://play.golang.org/p/Kd7TRoRG5w>

## ライブラリmapstructureを使う方法 {.section}

<a href="https://github.com/mitchellh/mapstructure" target="_blank">https://github.com/mitchellh/mapstructure</a>
  
mapstructure.Decode関数を使えばよさげ。

## 補足：mapから map[string]interface{}に変換する方法 {.section}

最初は、MapToStructの第一引数にmap\[string\]\[\]stringをそのまま渡してて、valueのところがスライスだったらだめなのかな？と思ってましたが、map[string]interface{}に変換すればいいだけってのを教えてもらって、次のような変換で解決しました。

<pre class="go">m := map[string][]string{
    "1":[]string{"a","b"},
    "2":[]string{"C","d"},
}
// convert map to a map[string]interface{}
mi := make(map[string]interface{}, len(m))
for k, v := range mm {
    mi[k] = v
}
</pre>

## 補足：個人的なきっかけ {.section}

<pre class="go">var r *http.Request
r.Form (= url.Values = map[string][]string)
</pre>

POSTリクエストがきた時のr.Formを構造体に一時的に格納したいのですが、格納先の構造体のフィールドがやたら多い場合、一個ずつ格納するのが面倒だなというのがきっかけでした。

## 参考 {.section}

  * <a href="https://groups.google.com/forum/#!topic/golang-nuts/2PCtqxyirVE" target="_blank">how to convert map to struct &#8211; Google グループ</a>
  * <a href="http://bit.ly/1axEplM" target="_blank">Ideone.com &#8211; XWtlo &#8211; Online Go Compiler &#038; Debugging Tool</a>（これはGo1以前のソースなのでそのままでは動かない）
  * <a href="http://bit.ly/1axEFRY" target="_blank">struct <-> map[string]interface{} conversion &#8211; Google グループ</a>