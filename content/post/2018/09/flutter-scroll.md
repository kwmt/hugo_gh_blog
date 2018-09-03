+++
date = "2018-09-03T11:15:00+09:00"
title = "Flutterでスクロールを検知し、ある位置までスクロールしたらWidgetを表示するには"
draft = false
categories = ["Flutter"]
tags = ["Flutter","スクロール"]

+++

## やりたいこと


Flutterで、下図のように上にスクロールしてある一定の位置まで来たらWidgetを表示させ、下にスクロールしてある一定の位置まで来たら表示したWidgetを消すというのをやりたい。

<img src="/images/2018/09/flutter-scroll/scroll-test.gif" width="320" />


## どうやって？

1. スクロールを検知するListenerをセットする
2. そのListenerでスクロール位置を計算してWidgetの表示・非表示を切り替える。


### 1. スクロールを検知するListenerをセットする

Flutter標準の<a href="https://docs.flutter.io/flutter/widgets/ScrollController-class.html" target="_blank">`ScrollController`クラス</a>を使って、`ScrollController _scrollController;`と変数宣言し、`initState()`で初期化、`dispose()`で後処理しておきます。

```dart
ScrollController _scrollController;
@override
void initState() {
  super.initState();
  _scrollController = ScrollController();
}
@override
void dispose() {
  _scrollController.dispose();
  super.dispose();
}
```

`ListView.builder`に`ScrollController`をセットできるので、先程宣言した`_scrollController`をセットします。

```dart
Widget _buildListView() {
  return ListView.builder(
    controller: _scrollController,
    reverse: true,
    itemCount: messages != null ? messages.length : 0,
    itemBuilder: (BuildContext context, int position) {
      return _buildMessageRow(context, position);
    },
  );
}
```

`_scrollController`にスクロールを検知したときに処理をしたいリスナー(ここでは`_scrollListener`)を追加します。

```dart
@override
void initState() {
  super.initState();
  _scrollController = ScrollController();
  _scrollController.addListener(_scrollListener); // ←追加
}

void _scrollListener() {
  // スクロールを検知したときに呼ばれる      
}
```

これで、スクロールを検知することができるようになりました。

### 2. そのListenerでスクロール位置を計算してWidgetの表示・非表示を切り替える。

スクロール位置を計算する方法ですが、先に実装を書くと次のように書けます。

```dart
void _scrollListener() {
  double positionRate =
      _scrollController.offset / _scrollController.position.maxScrollExtent;
}
```

<a href="https://docs.flutter.io/flutter/widgets/ScrollController/offset.html" target="_blank">`_scrollController.offset`</a>は、現在のスクロール位置を表し、

<a href="https://docs.flutter.io/flutter/widgets/ScrollPosition/maxScrollExtent.html" target="_blank">`_scrollController.position.maxScrollExtent`</a>は、スクロールできる最大の位置を表します。

`offset`も`maxScrollExtent`もpixel単位で、たとえばですが`maxScrollExtent`が1203.45で、`offset`が605.2とかの場合は、スクロール位置は代替真ん中あたりにいることになります。(数値は適当です)

ですので、`offset`割る`maxScrollExtent`(`offset / maxScrollExtent`)としてあげれば、スクロール位置の割合がでますので、その割合が`0`なら一番下、`1`なら一番上ということになります(注意：ここではListViewは`reverse: true`と逆にしていますので、`reverse: false`なら`0`が一番上`1`が一番下となります)。

あとはスクロール位置の割合を使って、適当な閾値でWidgetの表示・非表示を切り替えればよいだけです。

```dart
void _scrollListener() {
  double positionRate =
      _scrollController.offset / _scrollController.position.maxScrollExtent;
  // ↓追加ここから    
  const threshold = 0.8;
  if (positionRate > threshold) {
    setState(() {
      _isShow = true;
    });  
  }
  if (positionRate < threshold - 0.05) {
    setState(() {
      _isShow = false;
    });  
  }
  // ↑追加ここまで   
}
```

`_isShow`はWidgetを切り替えるフラグです。参考までに次のように使っています。

```dart
Widget _buildSample() {
  return Container(
    padding: EdgeInsets.all(8.0),
    color: Colors.orange[100],
    child: Text(
        'To see this room\'s full history, upgrade one of our paid plans.',
        style: TextStyle(color: Colors.red),
      ),
  );
}

Widget _buildBody() {
  List<Widget> children = List.from([ _buildListView()]);
  if (_isShow) {
    children.add(_buildSample());
  }
  return Stack(
    children: children,
  );
}
```

ちなみに、先程の適当な閾値でWidgetの表示・非表示を切り替えているところで、

```dart
if (positionRate > threshold) {
    // setState
} else {
   // setState
}
```
このように`if~else~`ではなく

```dart
if (positionRate > threshold) {
    // setState
} 
if (positionRate < threshold - 0.05) {
   // setState
}
```

と`positionRate < threshold - 0.05`としているのは、`if~else~`のときだと`threshold`ギリギリの位置でWidgetの表示・非表示がちらつくためです。非表示時は少し余裕を持って非表示にしています。なお`0.05`という値は適当です。

以上です。

Happy Fluttering!



