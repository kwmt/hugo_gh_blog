
+++
title= "Android Studioを2.1に上げると、ActiveAndroidでエラーが出た場合の対処法"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["AndroidStudio", "Android"]
keywords = [""]
+++

## 問題

Android Studioを2.1にあげると、`com.android.tools.build:gradle`も2.1.0に上げれるのですが、上げてしまうと、以下のようなエラーがでて、Android6.0で落ちるようになってしまいました。

```
Caused by: java.lang.NullPointerException: Attempt to invoke virtual method 'java.lang.String com.activeandroid.TableInfo.getIdName()' on a null object reference at com.activeandroid.Model.<init>(Model.java:55)
```


## 対策
調べてみるとActiveAndroidのissueに書いてあったが、`AA_MODELS`に `com.activeandroid.Model` を継承しているクラスを指定したmeta-dataを追加すると良いとのとこ。
https://github.com/pardom/ActiveAndroid/issues/461#issuecomment-207252708

こんな感じになりました。

```
<meta-data
    android:name="AA_MODELS"
    android:value="com.my.db.ModelAxx, com.my.db.ModelBxx"/>

```

## 原因
まったく同じソースで、`com.android.tools.build:gradle`が2.0.0だったら問題なかったので、このバージョンアップのなにかが原因だと思うが、詳しくは調べていません。

ちなみに、 `AA_MODELS` とはなにかについては、 <a href="https://github.com/pardom/ActiveAndroid/wiki/Creating-your-database-model#speeding-up-application-startup" target="_blank">ここ</a> に書かれてあるように、AcitveAndroidは全部のファイルの中からModelクラスを探しますが、`AA_MODELS`にModelクラスを明示的に指定することで処理を早くするために指定するものとのことです。

