
+++
title= "macOSのダイアログでフォーカスをタブで移動させるには"
date= 2020-12-13T19:24:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["macOS"]
tags = ["macOS", "ショートカットキー"]
keywords = ["macOS", "ショートカットキー", "ダイアログ", "タブ"]
+++

macOSで保存していないときなど下のようなダイアログが出ると思いますが、`save`にフォーカスがあたっていてEnterキーを押すと保存されてしまいます。
しかし保存したくないときは`Dont't Save`をクリックすればいいのですが、マウスやトラックパッドを使わずキーボードだけで`Dont't Save`を押したい場合ないですか？
それがしばらく分からなかった（調べようともしてませんでしたが）のですが、ようやく調べたのでメモしておこうと思います。

<img src="/images/2020/12/shortcut-for-moving-daialog-focus/mac-dialog.png">


```
System Preferences > Keyboard -> Shortcutタブ
```
といき、下部に
```
Use Keyboard navigation to move focus between controls
```
というチェックボックスがあるので、そこをチェックするだけです。

<img src="/images/2020/12/shortcut-for-moving-daialog-focus/move-focus.png">

これでダイアログが出ている状態でタブを押すとフォーカスが移動してくれて、冒頭の例だと`Dont't Save`の位置にタブで移動し、Enterキーを押せばキーボードだけで保存しないようにできます。