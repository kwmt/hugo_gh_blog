+++
date = "2017-01-19T00:32:48+09:00"
title = "CSSで横スクロールしたい + Riot.js"
categories = ["web-front"]
tags = ["CSS","Riot.js"]

+++

## 横スクロールしたい
{{< figure src="/images/2017/01/side-scroll-sample.png" >}}


```html
<ul>
    <li>要素1</li>
    <li>要素2</li>
    <li>要素3</li>
    <li>要素4</li>
</ul>
```
とHTMLがなっているとき

```
 /*画像リストを横スクロール*/
ul {
    overflow-x: scroll;
    white-space: nowrap;
}
li {
    display: inline-block;
    padding-right: 5px;
}
```

とする。 `li`はブロック要素なので`display: inline-block;`でインラインブロック化して横な並びになります。`white-space: nowrap;`で改行も半角スペースにすると横のサイズからはみ出て、それを`overflow-x: scroll;`でスクロールさせます。


## サンプル
https://embed.plnkr.co/awNm98DSBsyTar0sLnY3/
