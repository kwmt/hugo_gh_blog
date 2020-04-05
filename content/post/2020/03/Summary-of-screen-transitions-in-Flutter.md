+++
title= "Flutterでの画面遷移まとめ"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Flutter"]
tags = ["Flutter"]
keywords = [""]
+++

# はじめに

先日[Flutter製チャットアプリを支える技術](https://qiita.com/kwmt@github/items/2e81b46d62beb091d18b)でチャットアプリを作ったときの内容を書いて今年は終わりかなと思っていたら、こちらの枠が空いていたので前回触れなかった画面遷移について書きたいと思います。

Flutterの画面遷移については[Navigation & routing](https://flutter.io/docs/development/ui/navigation)を見ると基本的には良いと思います。

ですが、実際アプリを作った際にこれだけでは足りないと思いますので、画面遷移をiOS・Androidのそれぞれの動画＋実装という形でまとめておきたいと思います。


## 別の画面に遷移したい


| iOS       |       Android |
|:---------:|:------------------:|
| ![ios-push.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/0fe069a7-b19c-a8fd-7fc3-363756800596.gif)  |  ![android-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/0090a9a6-8096-660f-b4a8-6aed41aae445.gif)  |


たとえば、ルーム一覧画面からパスを"/rooms/\<roomId\>"とするルーム詳細画面(RoomScreen)に遷移したいときは次のようにします。[^1]



```dart:room_list_screen.dart
Navigator.push(
  context,
  MaterialPageRoute(
      settings: RouteSettings(name: "/rooms/<roomId>"),
      builder: (BuildContext context) => RoomScreen(roomId)),
);
```

[^1]: この例ではRoomScreenのコンストラクタでroomIdを渡せるようにしています。


## 前の画面に戻りたい


| iOS       |  Android |
|:---------:|:------------------:|
| ![ios-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/9c7ddd7b-aeb3-a47c-cead-8f402c89a7aa.gif) |  ![android-nav2.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/9938c67c-6dd2-612a-2793-aa679b4b4340.gif) |

`Navigator.push`を使って画面遷移したあと、左上のバックボタンをタップして戻る場合は特に処理は必要ありませんが、それ以外のボタンから戻りたい場合もあるかと思います。その場合は戻りたいタイミングで次のようにします。(上の動画では、下部のバツボタンをタップしています）

```dart
Navigator.pop(context);
```

## ダイアログを出したい


| iOS       |  Android |
|:---------:|:------------------:|
| <img width="375" alt="IMG_923E967FABA9-1.jpeg" src="https://qiita-image-store.s3.amazonaws.com/0/22161/8c9793dd-ce7a-65fb-8e05-9d4aa69769dc.jpeg"> | <img width="375" alt="IMG_923E967FABA9-1.jpeg" src="https://qiita-image-store.s3.amazonaws.com/0/22161/9dc0d6a9-e305-be0c-4cc3-4dc15250b84f.png">  |

[showDialog](https://docs.flutter.io/flutter/material/showDialog.html)と[AlertDialog](https://docs.flutter.io/flutter/material/AlertDialog-class.html)の組み合わせです。AlertDialogの方のドキュメントにもサンプルが載っています。

```dart
Future<bool> showDialogMessage(BuildContext context,
    {String title, String message, bool isOkOnly = false}) {
  return showDialog<bool>(
    context: context,
    builder: (context) =>
        _buildDialog(context, title, message, isOkOnly: isOkOnly),
  );
}
Widget _buildDialog(BuildContext context, String title, String message,
    {bool isOkOnly = false}) {
  if (title == null && message == null) {
    throw ArgumentError("titleとmessageのどちらともnullです。どちらかは指定してください。");
  }
  List<Widget> actions = List();
  if (!isOkOnly) {
    actions.add(FlatButton(
      child: Text(“Cancel”),
      onPressed: () {
        Navigator.pop(context, false);
      },
    ));
  }
  actions.add(FlatButton(
    child: Text(“OK”),
    onPressed: () {
      Navigator.pop(context, true);
    },
  ));
  return AlertDialog(
    title: title != null ? Text(title) : null,
    content: message != null ? Text(message) : null,
    actions: actions,
  );
}
```



## 前の画面にデータを渡したい

AndroidのonActivityResult的なことをしたいわけですが、まずawaitを付けて別の画面に遷移します。


```dart
void _showProfileEditPage(BuildContext context) async {
  User user = await Navigator.push(/* 省略 */).;
  if (user == null) {
    // something
    return;
  }
  // something
}
```

そして、遷移した先の前の画面にデータを渡したいタイミングで`Navigator.of(context).pop(_user)` とすると前の画面にデータを渡せます。

この例ではUser型の_userを返してます。


## 前の画面に戻る前に、戻っていいか確認ダイアログを出しOKの場合のみ戻りたい

| iOS       |  Android |
|:---------:|:------------------:|
| ![ios-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/b861ca4b-df1a-c1e6-7d5f-d6c81f08a4a2.gif) | ![a-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/79bbce99-b932-13a7-b58f-2ed9a7c8d4d1.gif) |






編集画面などでなにか編集して確定する前に間違えてとかで前に戻ろうとしてしまう場合があるかと思います。
そのようなときは、編集中ですが戻っていいか確認ダイアログを出すこともあると思います。



上の動画は、ダイアログを出したあと「Cancel」をタップしたらなにもせず、「OK」をタップしたら前の画面に戻る動画になります。

これをするには、[`WillPopScope`](https://docs.flutter.io/flutter/widgets/WillPopScope-class.html)でWrapします。

WillPopScopeの`onWillPop`はバックキーイベントを検知してくれるような動作になるので、そこでダイアログを出したりするとよさそうです。

```dart
Widget body = WillPopScope(
  onWillPop: _willPopCallback,
  child: Form(
      key: _formKey,
      child: SafeArea(
        child: Container(
          padding: EdgeInsets.all(16.0),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: <Widget>[
              roomImageWidget,
              Expanded(
                child: TextFormField(
                  controller: _controller,
                  onSaved: (String name) {
                    debugPrint(name);
                    _editingRoom.name = name;
                  },
                ),
              )
            ],
          ),
        ),
      )),
);
```

`onWillPop`の`_willPopCallback`は下記のようにするとできます。

```dart
Future<bool> _willPopCallback() async {
  if (_textEditController.text != <画面遷移時にTextFieldにセットした値>) {
    return showDialogMessage(context,
            title: <タイトル>,
            message: <メッセージ>) ??
        false;
  }
  // trueで戻る。falseで戻らない
  return true;
}
```

`showDialogMessage`は先述した「[ダイアログを出したい](#ダイアログを出したい)」で紹介した自前のメソッドです。


## モーダルで遷移したい

| iOS       |  Android |
|:---------:|:------------------:|
| ![ios-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/16a9dcef-1b39-3ed1-9bea-0bb65dc4c970.gif) | ![a-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/938aba45-f46c-f241-55ee-9a03cecd9a7f.gif) |



左上のボタンをバックボタン(← or \<)はなくバツボタン(x)にするには、[`MaterialPageRoute`](https://docs.flutter.io/flutter/material/MaterialPageRoute-class.html)に[`fullscreenDialog`](https://docs.flutter.io/flutter/widgets/PageRoute/PageRoute.html)プロパティがある[^2]ので、それを`true`にするだけです。

[^2]: 正確にはMaterialPageRouteの親クラスの[PageRoute](https://docs.flutter.io/flutter/widgets/PageRoute-class.html)が`fullscreenDialog`プロパティを持っています。



```dart
Navigator.push(
  context,
  MaterialPageRoute(
      settings: RouteSettings(name: "/rooms/<roomId>/edit"),
      builder: (BuildContext context) => RoomScreen(roomId),
      fullscreenDialog: true,
   ),
);
```


## 今までの画面を破棄して新しく画面を表示したい

| iOS       |  Android |
|:---------:|:------------------:|
| ![ios-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/d3e10058-9d91-de89-f929-4083f6e81b0c.gif) | ![a-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/ec1983f2-7480-0ad4-3b29-e053364a2215.gif) |

上の動画はサインイン後にサインイン画面は不要なので破棄しホーム画面に遷移しています。左上にバックボタンやバツボタンがないことがわかるかと思います。

これを実現するには[pushReplacement](https://docs.flutter.io/flutter/widgets/Navigator/pushReplacement.html)を使います。

```dart
Navigator.of(context).pushReplacement(
  MaterialPageRoute(
      settings: RouteSettings(name: "/home"),
      builder: (BuildContext context) => HomeScreen(),
   ),
);
```



## BottomSheetで選択させたい

| iOS       |  Android |
|:---------:|:------------------:|
| ![ios-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/1cde11ef-93b7-f4fb-fdc8-142589ba2db4.gif) | ![a-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/6913f028-60b3-cdde-49f4-f3901609042c.gif) |

[showModalBottomSheet](https://docs.flutter.io/flutter/material/showModalBottomSheet.html)を使います。[^3]

```dart
showModalBottomSheet<void>(
    context: context,
    builder: (BuildContext context) => Column(
          mainAxisSize: MainAxisSize.min,
          children: <Widget>[
            ListTile(
              leading: Icon(Icons.photo_library),
              title: Text("Gallery"),
              onTap: onTapGallery,
            ),
            ListTile(
              leading: Icon(Icons.camera_alt),
              title: Text("Camera"),
              onTap: onTapCamera,
            ),
          ],
        ));
```


[^3]: `onTapGallery`と`onTapCamera`は`VoidCallback`型です。

## SnackBarを表示したい

| iOS       |  Android |
|:---------:|:------------------:|
| ![ios-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/1d0403d5-556c-e031-13c8-b1e9e22a3a5d.gif) | ![a-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/e1622101-bdc4-73a3-58ba-f3dad7939e49.gif) |

```dart
Scaffold.of(context).showSnackBar(SnackBar(content: Text(text)));
```



## 画面遷移時にアニメーションしたい

| iOS       |  Android |
|:---------:|:------------------:|
| ![ios-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/b84296ba-6a81-21ee-40fb-ad4413fd530f.gif) | ![a-nav.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/2fa53ce7-8bee-6032-41bd-4b3a0aa66922.gif) |


`MaterialPageRoute`を継承したクラスを作って、`buildTransitions`メソッド内でアニメーションさせたいTransitionウィジェットを返すようにします。[^4]

[^4]: チャットアプリの現バージョンでは使っていませんが、次のバージョンに入れる予定です。

そして、上で紹介した`MaterialPageRoute`の部分を継承先のクラスに置き換えてやれば画面遷移時にアニメーションします。

```dart
class _FadeAnimationCustomRoute<T> extends MaterialPageRoute<T> {
  _FadeAnimationCustomRoute(
      {WidgetBuilder builder, RouteSettings settings, bool fullscreenDialog = false})
      : super(
            builder: builder,
            settings: settings,
            fullscreenDialog: fullscreenDialog);

  @override
  Widget buildTransitions(BuildContext context, Animation<double> animation,
      Animation<double> secondaryAnimation, Widget child) {
    if (settings.isInitialRoute) return child;
    return FadeTransition(opacity: animation, child: child);
  }
}
```

上の動画はこれを実装したものですが、フェードしてるのわかりますかね:sweat_smile:

# おわりに
以上、実際に作成したアプリで必要だった画面遷移に関してまとめてみました。
ご参考になれば幸いです。

