+++
date = "2018-06-24T16:30:00+09:00"
title = "Building Layouts in Flutterを読んだときのメモ"
draft = false
categories = ["flutter"]
tags = ["flutter", "layout"]
+++

# はじめに

Building Layouts in Flutterを読みながら実際にコードを書いていったときのメモです。

# メモ

- Containerはその子供のWidgetをカスタマイズすることができる
    - パディング、マージン、ボーダー、背景色などを追加したいとき、Containerを使う

- レイアウトを基本要素に分割
    - RowとCoumnを見分ける
    - レイアウトにグリッドはあるか？
    - オーバーラップする要素はあるか？
    - UIはタブが必要か？
    - アライメントやパディング、ボーダーが必要なエリアに注目

## プロジェクト内の画像の使うには
- 画像はプロジェクトルートに`images`ディレクトリを作ってその中に配置し、pubspec.yamlに`assets`項目を追加する。
    ```yaml
    assets:
      - images/lake.jpg
    ```

- 画像を使いたいところで、たとえば`Image.asset()`を使う

    ```dart
    body: new ListView(
    children: <Widget>[
        new Image.asset(
        'images/lake.jpg',
        width: 600.0,
        height: 240.0,
        fit: BoxFit.cover,
        ),
    ```

- fitプロパティの指定は、ドキュメントに画像付きで書かれてるいるのでわかりやすい
    - https://docs.flutter.io/flutter/painting/BoxFit-class.html
    - `BoxFit.cover`はAndroidの`ImageView.ScaleType`でいうところの`CENTER_CROP`かな？！


- Image Classのドキュメント
    - https://docs.flutter.io/flutter/widgets/Image-class.html

## 左寄せにするには

```dart
crossAxisAlignment: CrossAxisAlignment.start,
```

- ドキュメント
    - https://docs.flutter.io/flutter/rendering/CrossAxisAlignment-class.html#constants

## 余白を追加するには

Containerでラップして、`padding`に`EdgeInsets`を使う。

例えば、'Oeschinen Lake Campground'というテキストと'Kandersteg, Switzerland'というテキストの間に8px余白を取りたい場合は次のようになる。

```dart
child: new Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    children: <Widget>[
        new Container(
        padding: const EdgeInsets.only(bottom: 8.0),
        child: new Text('Oeschinen Lake Campground'),
        ),
        new Text('Kandersteg, Switzerland')
    ],
)
```

これをビルドすると、下図の赤枠の部分の余白ができる。

<img src="/images/2018/06/building-layouts-in-flutter/fultter-margin.png" width="400" />

## テキストのスタイルを設定するには

`Text`のプロパティの`style`で設定する。たとえばテキストをボールドにしたい場合は、次のようになる。

```dart
child: new Text(
    'Oeschinen Lake Campground',
    style: new TextStyle(
        fontWeight: FontWeight.bold
    ),
),
```

`FontWeight`は太さ`w100`から`w900`の9段階あり、`bold`は`w700`の太さ。ちなみにnormalは`w400`

- フォントサイズやカラーを変更する場合もTextStyleを使用する。以下はフォントサイズを12ptにして、色をblueに設定している。

```dart
child: new Text(
    'CALL',
    style: new TextStyle(
    fontSize: 12.0,
    color: Colors.blue,
),

```

- TextStyle
    - https://docs.flutter.io/flutter/painting/TextStyle-class.html

- FontWeight
    - https://docs.flutter.io/flutter/dart-ui/FontWeight-class.html
    
## Flutterはコードフォーマットをサポート

https://flutter.io/formatting/

flutterコマンドで

```shell
$ flutter format lib/main.dart
```
とすればフォーマットされる。

書く開発環境でのプラグインでもサポートされていて、Android Studionならいつもの`Command + Option + L`でフォーマットできる。

※`Command + Option + L`でフォーマットしたあと、コマンドラインの`flutter format`を実行すると、`Unchanged lib/main.dart`と出たので同じこと。

## 均等に配置するには

`MainAxisAlignment.spaceEvenly`を使う。

<img src="/images/2018/06/building-layouts-in-flutter/space-evenly.png" width="400" />


## ThemeのprimaryColorを取得するには

```dart
Color color = Theme.of(context).primaryColor;
```

## 長いテキストを折り返すには

`Text`クラスの`softWrap`プロパティを`true`に設定する。ただデフォルトtrueなので、明示的にtrueにしなくてもよい。

```dart
child: new Text(
    '''
Lake Oeschinen lies at the foot of the Blüemlisalp in the Bernese Alps. Situated 1,578 meters above sea level, it is one of the larger Alpine Lakes. A gondola ride from Kandersteg, followed by a half-hour walk through pastures and pine forest, leads you to the lake, which warms to 20 degrees Celsius in the summer. Activities enjoyed here include rowing, and riding the summer toboggan run.
''',
softWrap: true,
),
```

<img src="/images/2018/06/building-layouts-in-flutter/text-softwrap-true.png" />

`softWrrap`を`false`にすると、折返さない。

<img src="/images/2018/06/building-layouts-in-flutter/text-softwrap-false.png" />

こうなると、`...`を表示したくなるので、それをするには、Textクラスの`overflow`プロパティに`TextOverflow.ellipsis`を設定する。

```dart
overflow: TextOverflow.ellipsis,
```

<img src="/images/2018/06/building-layouts-in-flutter/text-ellipsis.png" />


## マテリアルデザインを使わなくてもいい

```dart
class NoMaterialApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new Container(
      decoration: new BoxDecoration(color: Colors.white),
      child: new Center(
        child: new Text('Hello World',
            textDirection: TextDirection.ltr,
            style: new TextStyle(fontSize: 40.0, color: Colors.black87)),
      ),
    );
  }
}
```

<img src="/images/2018/06/building-layouts-in-flutter/no-material.png" width="300" />

## mainAxisAlignment and crossAxisAlignment

いまいちmainAxisとcrossAxisの違いがわからなかったけど、これでわかった。

<img src="/images/2018/06/building-layouts-in-flutter/main-cross-axis.png"  />

https://flutter.io/tutorials/layout/#alignment

## Stackとは

- 画像のようなWidgetに別のWidgetをのせたいときに使う
- childrenの最初のWidgetがベースnoWidgetになって、次のWidgetがベースWidgetの上にのっかる
- Stackのcontentはスクロールできない

- コード例

```dart
class _MyHomePageState extends State<MyHomePage> {
  @override
  Widget build(BuildContext context) {
    var stack = new Stack(
      alignment: const Alignment(0.6, 0.6),
      children: [
        new CircleAvatar(
          backgroundImage: new AssetImage('images/pic.jpg'),
          radius: 100.0,
        ),
        new Container(
          decoration: new BoxDecoration(
            color: Colors.black45,
          ),
          child: new Text(
            'Mia B',
            style: new TextStyle(
              fontSize: 20.0,
              fontWeight: FontWeight.bold,
              color: Colors.white,
            ),
          ),
        ),
      ],
    );

    return new Scaffold(
      appBar: new AppBar(
        title: new Text(widget.title),
      ),
      body: new Center(
        child: stack,
      ),
    );
  }
}
```

<img src="/images/2018/06/building-layouts-in-flutter/stack-sample.png"  width="300"/>

このコードの`Alignment`は、オーバーラップしているText(Container)の位置を決定しています。
引数の値は-1~1の範囲で動き、画像の真ん中が0,0で左上が-1,-1となります。
0,0などのよく使われそうなやつは、`Alignment.center`などのように定義されています。

- Alignment
    -  https://docs.flutter.io/flutter/painting/Alignment-class.html


# おわりに

上からざっと読みました。[Building a layout](https://flutter.io/tutorials/layout/#building)がレイアウトの基本的なことが書かれていて、[Lay out multiple widgets vertically and horizontally](Lay out multiple widgets vertically and horizontally)は応用という感じでしたので、さらっと目を通したぐらいですが、これを読んだおかけで、レイアウトの基本がわかった気がします。