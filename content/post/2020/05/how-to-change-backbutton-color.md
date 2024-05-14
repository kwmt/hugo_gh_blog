+++
title= "Flutterでバックボタンの色を変更するには"
date= 2020-05-06T13:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Flutter"]
tags = ["Flutter"]
keywords = [""]
+++


FlutterでたとえばNavigator.pushした先の画面で、バックボタンを表示しているとき、そのボタンのカラーを変えたい。

たとえば、下図の例では白色に変えていますが、このようにしたいという話です。


{{< figure src="/images/2020/05/how-to-change-backbutton-color/target.png" >}}



何もしなかったら、下図のように黒色になります（正確には `primaryColor` によって、黒か白か決まります[^1]）。

[^1]: [theme_data.dart](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/material/theme_data.dart#L320) を参照ください。`primaryIconTheme` は `primaryIsDark` によって白か黒かを決めていて、`primaryIsDark`は `estimateBrightnessForColor(primaryColor)` と `primaryColor`によって決まっているのが分かるかと思います。

{{< figure src="/images/2020/05/how-to-change-backbutton-color/default.png" width="75" >}}


その色を変更する方法として、`AppBar` に `iconTheme` を設定できますので、そこに次のように`IconThemeData` を使って変更したいカラーを設定します。

```
    return Scaffold(
      appBar: AppBar(
        title: Text("プロフィールの編集"),
        iconTheme: IconThemeData(color: Colors.white), // ここで色を決めることができる。
```

ただ、これだと、画面ごとに毎回設定しなくてはならないので、アプリ全体として設定したくなると思います。それをするには、

```
MaterialApp(
    theme: ThemeData(
        primaryColor: AppColors.appBar,
        primaryTextTheme: TextTheme(title: TextStyle(color: Colors.white)),
        primaryIconTheme: IconThemeData(color: Colors.white), // ここを追加
```

のように、`MaterialApp` の `theme` に `ThemeData` を設定できますが、さらに `ThemeData` の `primaryIconTheme` でバックボタンのカラー設定することで、アプリ全体のカラーを変更することができます。


ちなみに、両方（`MaterialApp`で`primaryIconTheme`と、`AppBar`で`iconTheme`の両方）を設定していた場合、`AppBar`で設定した`iconTheme`が優先されます。