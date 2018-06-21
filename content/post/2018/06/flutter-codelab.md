
+++
date = "2018-06-21T15:00:00+09:00"
title = "flutter codelabをやってみた"
draft = false
categories = ["flutter"]
tags = ["flutter", "codelab"]
+++

# はじめに

flutter codelabをやりました。

そのときのメモです。やった成果はこちら

https://github.com/kwmt/flutter-codelab

# メモ

- Android StudioでiOS,Androidビルドできてインストールできる
- ⌘+sでホットリロード
    - 反映されるのが早すぎて感動
    - iOS、Android両方同時にはできないのか？
- コード上でレイアウトを作成する(xmlとかない？)
- アイコンもコードから書ける
    ```dart
    trailing: new Icon(
      alreadySaved ? Icons.favorite : Icons.favorite_border,
      color: alreadySaved ? Colors.red : null,
    ),
    ```
- タップイベントは、onTapで
    - リストのitemやcell上のタップイベントはListTileのonTapで書ける
    - リストにnofifyするのはsetStateになる
        ```dart
        onTap: () {
        setState(() {
            if (alreadySaved) {
            _saved.remove(pair);
            } else { 
            _saved.add(pair); 
            } 
        });
        },       
        ```
- AppBarにボタンをおける
- 画面遷移はNavigator.pushで遷移
    - iOSはUINavigtaionControllerの動き
    - AndroidはstartActivityの動きになる（が、少しぎこちない？ので対策はあるのだろうか・・・）
- テーマ変更が超簡単
    ```dart
    @@ -8,6 +8,9 @@ class MyApp extends StatelessWidget {
    Widget build(BuildContext context) {
        return new MaterialApp(
        title: 'Welcome to Flutter',
    +      theme: new ThemeData(
    +        primaryColor: Colors.white
    +      ),
        home: new RandomWords(),
        );
    }
    ```
    |  デフォルトテーマ  |  変更後 |
    | ---- | ---- |
    |  <img src="/images/2018/06/flutter-codelab/flutter-theme-before.png" width="300" />  |  <img src="/images/2018/06/flutter-codelab/flutter-theme-after.png" width="300" />  |
