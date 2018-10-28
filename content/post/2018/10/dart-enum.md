+++
date = "2018-10-28T17:30:00+09:00"
title = "Dartのenumからの文字列取得について"
draft = false
categories = ["Dart"]
tags = ["Flutter","Dart", "enum"]

+++


```dart
enum Type { Hoge, Fuga }
```
というenumがあったとき、このenumから`“Hoge”`という文字列を取得したい場合、次のようにする。

```dart
Type.Hoge.toString().split('.')[1]
```

splitしなかったら、`"Type.Hoge"`という文字列が返る。

## dartpud(sample)

https://dartpad.dartlang.org/a4a24c9a210cb0779e74c615d5ef25dd

