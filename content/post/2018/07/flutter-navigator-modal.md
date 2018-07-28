+++
date = "2018-07-28T23:43:00+09:00"
title = "Flutterの画面遷移をモーダルにするには"
draft = false
categories = ["Flutter"]
tags = ["Flutter"]

+++

## 結論

[PageRoute](https://docs.flutter.io/flutter/widgets/PageRoute-class.html)クラスに、`fullscreenDialog`プロパティがあるので、`true`を設定するだけ。


これで、iOSのUINavivationControllerのpushの画面遷移の仕方でなく、モーダルで立ち上がるような遷移になる。

左上のボタンも`←`から`☓`に変わってくれる。

## 実装例

いま作ってるアプリでは、こんな感じのメソッドを自分のAppNavigatorみたいなクラスに書いてます。

```dart
  Future<T> _showScreen<T extends dynamic>(
      BuildContext context, String routePath, Widget destinationScreenWidget,
      {bool modal = false}) {
    return Navigator.push(
      context,
      MaterialPageRoute<T>(
          settings: new RouteSettings(name: routePath),
          builder: (BuildContext context) {
            return new Theme(
              data: AppConfig.kTheme
                  .copyWith(platform: Theme.of(context).platform),
              child: destinationScreenWidget,
            );
          },
          fullscreenDialog: modal),
    );
```

## 参考

- Flutter GalleryのMaterial -> Dialogs -> FULLSCREENで動きを確認できる
- Flutter Galleryのソースは[こちら](https://github.com/flutter/flutter/blob/master/examples/flutter_gallery/lib/demo/material/dialog_demo.dart#L196)

## 最後に

"flutter modal"とググっていて、全然引っかからなくて、しばらく諦めてたんですが、絶対あるはずということで根気よく調べたら、["flutter popup screen"](https://www.google.co.jp/search?q=flutter+popup+screen&oq=flutter+popup+screen&aqs=chrome..69i57.9311j1j7&sourceid=chrome&ie=UTF-8)で検索したら、こちらの[ブログ](https://marcinszalek.pl/flutter/flutter-fullscreendialog-tutorial-weighttracker-ii/)を見つけて、「あー、モーダルってダイアログでしたね。。」となっているところです。。。