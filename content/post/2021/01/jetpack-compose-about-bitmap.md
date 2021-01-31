
+++
title= "Jetpack ComposeでBitmapを表示するには"
date= 2021-01-31T22:50:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","Bitmap"]
keywords = ["Android", "Jetpack", "Compose","Bitmap"]
+++

Jetpack ComposeでBitmapを表示するには、`Image`関数を使い、引数にBitmapを渡せばいいのですが、そのとき`ImageBitmap`に変換する必要があるようで、
そのために[`asImageBitmap()`](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:compose/ui/ui-graphics/src/androidMain/kotlin/androidx/compose/ui/graphics/AndroidImageBitmap.kt;l=45?q=asImageBitmap)という拡張関数が生えてるので、それを使えばよさそうです。


```
import androidx.compose.foundation.Image
import androidx.compose.ui.graphics.asImageBitmap

Image(
    bitmap.asImageBitmap(),
)
```
