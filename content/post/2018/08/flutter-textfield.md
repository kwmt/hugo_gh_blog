+++
date = "2018-08-22T23:35:00+09:00"
title = "Flutterでテキストフィールドが最大3行になるようにするには"
draft = false
categories = ["Flutter"]
tags = ["Flutter","TextFormField"]

+++

# やりたいこと
Flutterで、LINEでメッセージ入力するときのように、テキストフィールドが最大3行になるようにしたい。
つまり、下の動画のようにしたい。

<iframe width="560" height="315" src="https://www.youtube.com/embed/ALKp97TTAMM" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>


# コード例とポイント説明

ポイントとなる部分だけのコードと説明します。

<script src="https://gist.github.com/kwmt/2cdaebf8ffe8f8453b1e9ea0beca8b34.js"></script>

<a href="https://docs.flutter.io/flutter/widgets/TextEditingController-class.html" target="_blank" >TextEditingController</a>を使って、TextFieldに入力されたテキスト取得できるようにしておきます。

また、TextFieldになにか変更があったら通知されるlistenerを登録します。

```dart
_textEditingController.addListener(_textEditListener);
```

このとき通知される関数はここでは_textEditListenerとしていて、変更があるたびに _maxLinesにセットされます。

次に、TextFormFieldのプロパティの`keyboardType`は複数行に最適な`multiline`を設定し、キーボードのエンターキーに当たる部分の`改行`できるように変更します。

```dart
keyboardType: TextInputType.multiline,
textInputAction: TextInputAction.newline,
```

`maxLines`は`null`を指定すると、どこまででも改行します。

```dart
maxLines: _maxLines
```

これらを組み合わせて、テキストが入力されるたびに改行コードを数え、改行コードが2つ以上(全体としては3行になっている)の場合は、`maxLines`を`3`に設定すればいいので、_textEditListener関数で次のようにしています。

```dart
void _textEditListener() {
  setState(() {
    // TextFieldに改行が2つ以上入っていたら、3行以上になるので、3行までに止める。
    _maxLines = '\n'.allMatches(_textEditingController.text).length >= 2 ? 3 : null;
  });
}
```

これで最初の動画のような動きになります。

