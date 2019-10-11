+++
title= "Android Studio3.6からの追加されたView Bindingを使ってみた"
date= 2019-10-11T19:40:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["View Binding","Android Studio 3.6"]
keywords = [""]
+++

## View Bindingとは

View Bindingは、Viewの参照をかんたんにアクセスできる機能です。
これを使うには、build.gradleファイルに次の設定が必要です。

```gradle
android {
   viewBinding.enabled = true
}
```

これにより、各レイアウトクラスがコードからアクセスできるようにコンパイル時に自動生成されます。
自動生成されたファイルは下記のパスに生成されます。

```
build/generated/data_binding_base_class_source_out/
```


自動生成されないようにするには、レイアウトに次のプロパティを設定すれば自動生成されません。

```xml
<LinearLayout
        ...
        tools:viewBindingIgnore="true" >
    ...
</LinearLayout>
```

## 使い方

`activity_maps.xml`というファイル名で、内容が次のような場合、

```xml
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    ...>
    

    <Button
        android:id="@+id/searchAreaButton"
        ...
         />
</androidx.constraintlayout.widget.ConstraintLayout>
```

Activity側で、次のように置き換えます。

```kotlin
private lateinit var binding: ActivityMapsBinding

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = ActivityMapsBinding.inflate(layoutInflater)
    setContentView(binding.root)
```

すると、次のように参照できます。

```
    binding.searchAreaButton.setOnClickListener {
        ....
    }
```

## DataBindingとの違い

- `<layout>`タグで囲む必要はありません。
- レイアウト変数とレイアウト式がサポートされてません。
