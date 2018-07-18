+++
date = "2018-07-18T16:00:00+09:00"
title = "Flutterでバックキーイベントを検知するには"
draft = false
categories = ["Flutter"]
tags = ["Flutter"]

+++

## 方法

Flutterでバックキー(Androidの`onBackPressed`)イベントを検知するには、[WillPopScope](https://docs.flutter.io/flutter/widgets/WillPopScope-class.html)クラスを使う。


## サンプル

```dart
  @override
  Widget build(BuildContext context) {
    return WillPopScope(
      onWillPop: () {
        // バックキーを押すとここに来る。
        // popしてあげないと、閉じてくれない。
        Navigator.of(context).pop();
      },
      child: Scaffold(
        // 省略
      ),
    );
  }
  ```


## 参考
- https://stackoverflow.com/a/50459239
- https://docs.flutter.io/flutter/widgets/WillPopScope-class.html
