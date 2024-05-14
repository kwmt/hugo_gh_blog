+++
title= "Navigation Architecture ComponentでToolbarのバックボタンを非表示にするには"
date= 2019-10-11T16:40:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Navigation","Jetpack","Android"]
keywords = [""]
+++

こちらを参照 https://stackoverflow.com/a/57169105/2520998



トップレベルのDestinationだったら非表示になるから、非表示にしたいDestinationをトップレベルに設定すればいい、とのこと。


[こちら](https://github.com/android/architecture-components-samples/blob/master/NavigationAdvancedSample/app/src/main/java/com/example/android/navigationadvancedsample/MainActivity.kt#L68)のサンプルを例にした場合、次のようにするとよさそう。

{{< gist e9307d8e0f37418d0a60b28269e307f2 >}}


{{< figure src="/images/2019/10/how-to-remove-back-button-of-toolbar-with-navigation-component/home.png" width="300" >}}
{{< figure src="/images/2019/10/how-to-remove-back-button-of-toolbar-with-navigation-component/about.png" width="300" >}}

