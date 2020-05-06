+++
date = "2018-07-29T03:50:00+09:00"
title = "Flutterのキーボードを閉じるには"
draft = false
categories = ["Flutter"]
tags = ["Flutter"]

+++

## はじめに

FlutterでLINEのようなチャットアプリを作っていて、TextFormでキーボードが表示されたら、Androidはバックキーでキーボードを閉じれるけど、iOSはバックキーないので閉じることができなかったので、キーボードを閉じる方法をメモ。

## キーボードを閉じるには


```dart
GestureDetector(
    onTap: () => FocusScope.of(context).requestFocus(FocusNode()),
    child: listView,
)
```

このようにタップイベントでキーボードのフォーカスを外すことで、キーボードを閉じるようです。
listViewだけにGestureを設定しているのは、TextFormFieldを含むWidgetに対してGestureを設定すると、キーボードが上がってる状態でTextFormFiledをタップすると、一旦閉じてまたキーボードが立ち上がってしまうため、listViewのみに設定しています。

## キーボードを閉じるには 別の方法(2020/04/07追記)
```dart
FocusScope.of(context).unfocus()
```

## 参考

- https://github.com/flutter/flutter/issues/7247#issuecomment-406178161
- https://stackoverflow.com/questions/44991968/how-can-i-dismiss-the-on-screen-keyboard#44991969