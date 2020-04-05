+++
title= "Flutter製チャットアプリを支える技術"
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

今年はGoogle I/Oに行ってFlutterを知って6月ぐらいからFlutterを触りだし、いろいろ勉強会に行ったり[Flutter温泉](https://gdg.connpass.com/event/98013/)に行ったり、今年の後半はFlutter三昧でした。

そんな中チャットアプリを作ったので、それぞれ機能をどのように実装したかコードを交えつつ少しずつご紹介したいと思います。

開発環境は下記のとおりです。

```
% flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel beta, v1.0.0, on Mac OS X 10.14.1 18B75, locale en-JP)
[✓] Android toolchain - develop for Android devices (Android SDK 28.0.3)
[✓] iOS toolchain - develop for iOS devices (Xcode 10.1)
[✓] Android Studio (version 3.2)
[✓] VS Code (version 1.29.1)
[✓] Connected device (2 available)
```


# まずどんな機能を作ったか？

大きく分けると次のようになるかなと思います。

* 会員登録・ログイン
* チャットルーム（以下、ルーム）作成できる
* ルーム一覧を見ることができる
* ルームでチャット(テキスト、画像の送信)ができる
* 既存のルームにユーザーが参加できる
* 自分のプロフィールを見ることができる
* アプリ内課金で定期購読できる 

それぞれ見ていきます。


## 会員登録・ログイン

チャットアプリなので、だれが投稿したか知りたいので、会員登録・ログインをできるようにしました。
これは、[firebase_auth](https://pub.dartlang.org/packages/firebase_auth)プラグインを使っていて、現在はGoogleログインと、Facebookログインに対応しています。

まずこんな感じで表示されるGoogleログインボタンのUIの実装を見てみます。



<img width="400" src="https://qiita-image-store.s3.amazonaws.com/0/22161/78e8c0fa-8cc5-1f77-be7e-5a0552a08cf9.png" />


signInButtonというWidgetを作成し、

```signin_button.dart
import 'package:flutter/material.dart';

Widget signInButton(title, uri,
    [color = const Color.fromRGBO(68, 68, 76, .8)]) {
  return Container(
    padding: const EdgeInsets.all(16.0),
    child: Center(
      child: Row(
        crossAxisAlignment: CrossAxisAlignment.center,
        children: <Widget>[
          Image.asset(
            uri,
            width: 25.0,
          ),
          Padding(
            child: Text(
              title,
              style: TextStyle(
                fontFamily: 'Roboto',
                color: color,
              ),
            ),
            padding: EdgeInsets.only(left: 15.0),
          ),
        ],
      ),
    ),
  );
}
```

使う側は例えば次のように使います。

```dart
MaterialButton(
  child: signInButton(Strings.of(context).signInWithGoogle,
      'assets/images/google.png'),
  onPressed: _isAgree ? _handleSignInWithGoogle : _showError,
  color: Colors.white,
),
````


ログインとは直接関係ないですが`_isAgree`はプライバシーポリシーと利用規約に同意したかどうかで、同意していれば`_handleSignInWithGoogle`を呼び、同意してなければエラーにしています。

また、`Strings.of(context).signInWithGoogle`の部分は多言語対応していて、Android のstringsリソースをイメージしています([こちら](https://qiita.com/jiro-aqua/items/fd9896682ca09018fdd3)が参考になりました)。

ボタンができたので、Googleログインの処理は次のような感じにしています。

`_googleSignIn`を準備して、

```dart
final GoogleSignIn _googleSignIn = GoogleSignIn(
    scopes: <String>['email'],
);
```
ログインボタンが押されたタイミングで、下記のようにします。

```dart
Future<FirebaseUser> signInWithGoogle() async {
  GoogleSignInAccount googleUser = await _googleSignIn.signIn();
  if (googleUser == null) {
    return null;
  }
  GoogleSignInAuthentication googleAuth = await googleUser.authentication;
  final AuthCredential credential = GoogleAuthProvider.getCredential(
    accessToken: googleAuth.accessToken,
    idToken: googleAuth.idToken,
  );
  FirebaseUser user = (await _auth.signInWithCredential(credential)).user;
  return user;
}
```

プラグインが[`PlatformException`](https://docs.flutter.io/flutter/services/PlatformException-class.html)を投げることがあるので、try catchして、PlatformExceptionにキャストすると[`code`](https://docs.flutter.io/flutter/services/PlatformException/code.html)が取得できるので、それでエラー判定するとよいかと思います。ただし、プラグインによっては、違うエラーだけど`code`が全部一緒だったり、iOSとAndroidで`code`が異なっていたりする場合があるので、プラグインによって対応を変えなければいけないことがあります。

```dart
try {
  // ↑のsignIn処理のため省略
} catch (error) {
  if (error is PlatformException) {
    switch (error.code) {
      case GoogleSignIn.kSignInFailedError:
        // something
      case GoogleSignIn.kSignInCanceledError:
        // something
      case GoogleSignIn.kSignInRequiredError:
        // something
      case GoogleSignInAccount.kFailedToRecoverAuthError:
        // something
      case GoogleSignInAccount.kUserRecoverableAuthError:
        // something
      default:
        // something
    }
  }
  return null;
}
```

あと、[flutter_firebase_ui](https://pub.dartlang.org/packages/flutter_firebase_ui)を使うと少しだけ楽に作れますが、ボタンを押したイベントが取れずプログレスを出すことができないため、それぞれ[google_sign_in](https://pub.dartlang.org/packages/google_sign_in)と[flutter_facebook_login](https://pub.dartlang.org/packages/flutter_facebook_login)をそれぞれ使っています。

## ルーム作成機能

ルームを作成するのに、下図のようにルーム画像とルーム名を入力してもらうようにしました。

<img width="400" src="https://qiita-image-store.s3.amazonaws.com/0/22161/f8a45a66-d7e6-f575-8667-f3beccedd880.png" />

黄色の円をタップすると画像を選択するようにし、TextFieldにはルーム名を入力できます。

これをコードで見ると次のようにしています。

```dart
Widget roomImageWidget = GestureDetector(
  child: Stack(
    alignment: const Alignment(0.6, 0.6),
    children: [
      ImageUtils.circleImageProvider(_previewImage(_imageFile),
          radius: 100.0, text: _inputtingRoomName),
      Icon(Icons.camera_alt),
    ],
  ),
  onTap: () => _onTapRoomImage(context),
);
```

円のWidgetとカメラアイコンのWidgetを重ねていますが、このように2つ以上のWidgetを重ねるには、`Stack`([こちら](https://flutter.io/docs/development/ui/layout#stack)が参考になるかと思います)を使います。
また、全体をタップできるようにしたいため、Stackを[`GestureDetector`](https://docs.flutter.io/flutter/widgets/GestureDetector-class.html)でWrapしています。

画像を円形に表示するには、circleImageProviderの自作メソッドの中でやっているのですが、次のように[`CircleAvatar`](https://docs.flutter.io/flutter/material/CircleAvatar-class.html)を使うと便利かと思います。

```dart
CircleAvatar(
  child: child,
  backgroundColor: Colors.yellow,
  backgroundImage: imageProvider,
  radius: radius,
);
```

### BottomSheetを使って選択

ルーム画像を設定するために先程の黄色の画像をタップすると、「ギャラリー」か「カメラ」をBtoomSheetを使って選択できるようにしています。[^1]

[^1]: safe areaにかぶっているので調整が必要かもしれません。


<img width="400" src="https://qiita-image-store.s3.amazonaws.com/0/22161/976166ab-c3f9-f626-2dc9-666eae82296d.png" />

これを実装するには、次のように[`showModalBottomSheet`](https://docs.flutter.io/flutter/material/showModalBottomSheet.html)([こちら](https://flutterdoc.com/bottom-sheets-in-flutter-ec05c90453e7)が参考になるかと思います)を使います。

```dart
Future<void> showCameraSelector(BuildContext context,
        VoidCallback onTapGallery, VoidCallback onTapCamera) =>
    showModalBottomSheet<void>(
        context: context,
        builder: (BuildContext context) => Column(
              mainAxisSize: MainAxisSize.min,
              children: <Widget>[
                ListTile(
                  leading: Icon(Icons.photo_library),
                  title: Text(Strings.of(context).gallery),
                  onTap: onTapGallery,
                ),
                ListTile(
                  leading: Icon(Icons.camera_alt),
                  title: Text(Strings.of(context).camera),
                  onTap: onTapCamera,
                ),
              ],
            ));
```

`onTapGallery`や`onTapCamera`では、好みの写真プラグインを使えばよいかと思います。今回は[image_picker](https://pub.dartlang.org/packages/image_picker)を使いました。

### 画像の圧縮

画像のアップロード前に圧縮しているのですが、プラグインを調べると最初に目についたのが[Image](https://pub.dartlang.org/packages/image)プラグインだったのでこちらを使っていました。が、Image.decodeが4,5分かかって使い物にならないので、[flutter_native_image](https://github.com/btastic/flutter_native_image)を使っています。こちらはだいたい100ms以内で終わってくれるので早いです！

実装例は次のとおりです。

```image.dart
import 'dart:io';

import 'package:flutter_native_image/flutter_native_image.dart';

class Image {
  /// 画像を圧縮する
  /// https://stackoverflow.com/a/50998865
  /// ↓に変更
  /// https://github.com/flutter/flutter/issues/19383#issuecomment-405130684
  /// https://github.com/btastic/flutter_native_image
  /// @param file オリジナルファイル
  /// @return 圧縮されたfile
  static Future<File> compress(File file) async {
    ImageProperties properties =
        await FlutterNativeImage.getImageProperties(file.path);
    return await FlutterNativeImage.compressImage(file.path,
        quality: 80,
        targetWidth: 600,
        targetHeight: (properties.height * 600 / properties.width).round());
  }
}
```

### 画像のアップロード

画像のアップロード先はCloud Storageにしていますので、[firebase_storage](https://pub.dartlang.org/packages/firebase_storage)プラグインを使用しています。
画像をアップロードして、アップロード先のURLを知りたいのでコードは次のようにしています。

```dart
Future<String> uploadImage(File file) async {
  int timestamp = DateTime.now().millisecondsSinceEpoch;
  String subDirectoryName = 'images';
  final StorageReference ref = storage
      .ref()
      .child(subDirectoryName)
      .child('${timestamp}');
  final StorageUploadTask uploadTask = ref.putFile(
      file,
      StorageMetadata(
        contentType: "image/jpeg",
      ));
  StorageTaskSnapshot snapshot = await uploadTask.onComplete;
  if (snapshot.error == null) {
    return await snapshot.ref.getDownloadURL();
  }
  switch (snapshot.error) {
    case StorageError.unknown:
      // something
    case StorageError.objectNotFound:
      // something
    case StorageError.bucketNotFound:
      // something
    case StorageError.projectNotFound:
      // something
    case StorageError.quotaExceeded:
      // something
    case StorageError.notAuthenticated:
      // something
    case StorageError.notAuthorized:
      // something
    case StorageError.retryLimitExceeded:
      // something
    case StorageError.invalidChecksum:
      // something
    case StorageError.canceled:
      // something
  }
  return null;
}
```
画像のアップロードはルーム作成時だけでなく、チャットのメッセージとしてアップロードできるようにしていますし、プロフィール画像を変更できるようにもしているので、実際のコードはアップロード先を変更する処理とか入ったり、戻りの型が違ったりしてますが、だいたいこのような感じです。[^2]

[^2]: firebase_storageプラグインのv1.0.3で[使い方が変わり](https://github.com/flutter/plugins/pull/531/files#diff-64e1f7ca1659a9f092f80ba64377fbefL17)、取り急ぎ修正した感じなのでもしかしたらもっと良いやり方があるかも知れません。また、プラグインのexampleには`StreamBuilder`の使い方が書いてありますが、`StreamBuilder`を使うと表示とアップロード処理の分離できなさそうだったので使いたくありませんでした。

### Firestoreに格納する

ルームを保存する場所はCloud Firestoreを使っていますので、[cloud_firestore](https://pub.dartlang.org/packages/cloud_firestore)プラグインを使います。


```dart
Future<void> createNewRoom(Room room) async {
  return _firestore
      .collection('rooms')
      .document(room.id)
      .setData(<String, dynamic>{'id': room.id, 'name': room.name});
}
```

## ルーム一覧

ルーム一覧画面でfirestoreのroomsコレクションを監視していて、roomsコレクションにドキュメントが変更されたら更新されるようにしています。

```dart
_firestore
    .collection('rooms')
    .where(/** 条件 */) 
    .snapshots()
    .listen((querySnapshot) async {
        // 'rooms'に変化があったら通知される
    });
```


## ルームでチャット(テキスト、画像の送信)ができる

ルームのイメージです。

<img width="300" alt="room.png" src="https://qiita-image-store.s3.amazonaws.com/0/22161/1b817812-d68e-8d5b-53b3-7daf65e5bc1a.png">

詳細を書くとここだけで1記事になりそうなのであまり細かくは書きませんが、簡単に。

### チャットメッセージの表示部分

チャットメッセージの表示部分はListを逆順で表示しています。これは`ListView.builder`の`reverse`を`true`にすることで逆順にできます。

```dart
Widget _buildListView() => ListView.builder(
      controller: _scrollController,
      reverse: true,
      itemCount: messages != null ? messages.length : 0,
      itemBuilder: (BuildContext context, int position) =>
          _buildMessageRow(context, position),
    );
```

### 画像を横幅いっぱいかつ角丸

```dart
Widget _buildImageWidget(
    BuildContext context, Message message, int position) {
  return SizedBox(
      height: 180.0,
      child: Container(
          padding: EdgeInsets.only(top: 8.0, right: 8.0, bottom: 8.0),
          child: GestureDetector(
            onTap: () {
              _onTapImage(position);
            },
            child: message.downloadImageUrl != null
                ? ImageUtils.roundedImage(message.downloadImageUrl.toString())
                : Text(Strings.of(context).uploading),
          )));
}
```

ImageUtils.roundedImageの部分は以下です。

```dart
static Widget roundedImage(String imageUrl) {
  return ClipRRect(
      borderRadius: BorderRadius.circular(8.0),
      child: cachedImage(imageUrl, fit: BoxFit.cover));
}
```


### 画像のキャッシュ

画像のキャッシュには[cached_network_image](https://pub.dartlang.org/packages/cached_network_image)プラグインを使用しています。

```dart
static CachedNetworkImage cachedImage(String imageUrl, {fit}) {
  return CachedNetworkImage(
    imageUrl: imageUrl,
    errorWidget: Icon(Icons.error),
    fit: fit,
  );
}
```

### メッセージ入力部分

#### 送信タイミングでテキストをクリアするには
送信ボタンを押すタイミングで入力テキストをクリアしたいと思ったとき、次のような実装がパッと思いつくかと思います。

```dart
_texEditingController.crear();
```

これだと日本語などの文字を入力中（確定前）に、クリアするとクラッシュしてしまいます。仕様など見たりいろいろ調べたところ、次のようにするとクラッシュしなくなりました。

```dart
_texEditingController
  ..clearComposing()
  ..clear();
```

#### キーボードを閉じるには

TextField部分をタップするとキーボードが表示されますが、iOSの場合はなにもしないとキーボードを閉じることができません。たとえば、チャットメッセージの表示部分をタップしてキーボードを閉じるには、チャットメッセージリスト部分を`GestureDetector`でWrapして、onTapでフォーカスを変えることで閉じることができます。

```dart
GestureDetector(
    onTap: () => FocusScope.of(context).requestFocus(FocusNode()),
    child: _buildListView(),
)
```

focusまわりは[こちら](https://flutter.io/docs/cookbook/forms/focus)が参考になるかと思います。


## 既存のルームにユーザーが参加できる

既存のルームに他のユーザーが参加するためには、そのルームのQRコードを読み取って参加します。

### QRコードを生成するには

ルームから下図のような画面を出して読み取ってもらうことにしました。

<img width="300" alt="room.png" src="https://qiita-image-store.s3.amazonaws.com/0/22161/3202190c-70f9-9581-764b-9d8624fa7ef0.png">

QRコード生成には[qr_flutter](https://pub.dartlang.org/packages/qr_flutter)プラグインを使用していて、この画面の全体のコードは下記のように実装しました。

```dart
/// QRコードを表示するための画面
class DisplayQrCodeScreen extends StatelessWidget implements Screen {
  final String _qrCodeData;

  const DisplayQrCodeScreen(this._qrCodeData);

  @override
  Widget build(BuildContext context) {
    final bodyHeight = MediaQuery.of(context).size.height -
        MediaQuery.of(context).viewInsets.bottom;

    return Scaffold(
      appBar: AppBar(
        title: Text(Strings.of(context).qrCode),
        backgroundColor: Colors.black,
      ),
      backgroundColor: Colors.black,
      body: Container(
        color: Colors.white,
        child: Center(
          child: QrImage(
            version: 10,
            data: _qrCodeData,
            size: 0.2 * bodyHeight,
          ),
        ),
      ),
    );
  }

  @override
  String get name => "/qr";
}
```

QRコードのサイズを画面の高さの20%になるように設定しました。デバイス画面サイズを取得するために、[MediaQuery](https://docs.flutter.io/flutter/widgets/MediaQuery-class.html)を使っています。




### QRコード読み取り
QRコードの表示ができたので、今度は読み取りです。本アプリでルームに参加するためには、ルームを表すQRコードを読み取る必要があります。QR読み取る方法として2種類用意しました。


<img width="300"  src="https://qiita-image-store.s3.amazonaws.com/0/22161/e47a79ea-79f1-6379-c47f-c206b5776db6.png">

- QRコードをカメラで読み取る
- QRコード画像を開いて読み取る

の2種類です。

#### QRコードをカメラで読み取る

使用しているプラグインは[barcode_scan](https://pub.dartlang.org/packages/barcode_scan)です。（QRコードリーダーに関して、以前[ブログを書きました](https://kwmt27.net/2018/08/16/flutter-workshop/)のでご興味あればぜひ！)

```dart
/// カメラを使ってQRコードをスキャンしてRoomに参加する
void _joinRoomWithCamera(BuildContext context) async {
  try {
    _roomId = await BarcodeScanner.scan();
  } catch (error) {
    if (error is PlatformException &&
        error.code == BarcodeScanner.CameraAccessDenied) {
       // something
    }
  }
}
```

ポイントは`BarcodeScanner.scan()`だけでQRコード読み取り画面を呼び出せます。カメラアクセス許可されてない場合にエラーコードが`BarcodeScanner.CameraAccessDenied`で来るので、メッセージを出すとかするとよいかと思います。

#### QRコード画像を開いて読み取る

こちらは最近実装したのですが、[firebase_ml_vision](https://pub.dartlang.org/packages/firebase_ml_vision)プラグインを使って実現しました。学習モデルをダウンロードするためアプリ容量が増えてしまうのが難点なのですが(20MBぐらい増えました)。

[image_picker](https://pub.dartlang.org/packages/image_picker)プラグインでギャラリーからQRコード画像を開けるようにしておき、選択した画像をMLKitのBarcodeDetectorを使って検出しするという流れです。

```dart
Future<void> _onImageButtonPressed(ImageSource source) async {
  File selectedImageFile = await ImagePicker.pickImage(source: source);
  if (selectedImageFile == null) {
    return;
  }
  FirebaseVisionImage visionImage =
      FirebaseVisionImage.fromFile(selectedImageFile);
    final BarcodeDetectorOptions options = BarcodeDetectorOptions()
  final BarcodeDetector barcodeDetector =
      FirebaseVision.instance.barcodeDetector();
  final List<Barcode> barcodes =
      await barcodeDetector.detectInImage(visionImage);
  for (Barcode barcode in barcodes) {
    final BarcodeValueType valueType = barcode.valueType;
    switch (valueType) {
      case BarcodeValueType.text:
        final String value = barcode.displayValue;
        _roomId = value;
        break;
      default:
        break;
    }
  }
}
```

実装としてはこれで可能ですが、Androidでアプリ起動時に次のような例外が出るようになってしまいました。

```
2-06 17:02:11.288  9895  9895 E AndroidRuntime: java.lang.UnsatisfiedLinkError: dalvik.system.PathClassLoader[DexPathList[[zip file "/system/framework/org.apache.http.legacy.boot.jar", zip file "/data/app/com.instantonnection.app-zIN5KpTkwZQRngy5QLP6ig==/base.apk"],nativeLibraryDirectories=[/data/app/com.instantonnection.app-zIN5KpTkwZQRngy5QLP6ig==/lib/arm64, /data/app/com.instantonnection.app-zIN5KpTkwZQRngy5QLP6ig==/base.apk!/lib/arm64-v8a, /system/lib64]]] couldn't find "libflutter.so"
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at java.lang.Runtime.loadLibrary0(Runtime.java:1012)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at java.lang.System.loadLibrary(System.java:1669)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at io.flutter.view.FlutterMain.startInitialization(Unknown Source:32)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at io.flutter.view.FlutterMain.startInitialization(Unknown Source:5)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at io.flutter.app.FlutterApplication.onCreate(Unknown Source:3)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at android.app.Instrumentation.callApplicationOnCreate(Instrumentation.java:1154)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at android.app.ActivityThread.handleBindApplication(ActivityThread.java:5871)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at android.app.ActivityThread.access$1100(ActivityThread.java:199)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1650)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at android.os.Handler.dispatchMessage(Handler.java:106)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at android.os.Looper.loop(Looper.java:193)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at android.app.ActivityThread.main(ActivityThread.java:6669)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at java.lang.reflect.Method.invoke(Native Method)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:493)
12-06 17:02:11.288  9895  9895 E AndroidRuntime: 	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:858)
```

この対策としては、

![image.png](https://qiita-image-store.s3.amazonaws.com/0/22161/cb38beed-5f59-91ed-05d6-21ae768fb6ec.png)

と設定し、ビルド時に以下のオプションをつけることで解決しました。([参考](https://github.com/azihsoyn/flutter_mlkit/issues/36#issuecomment-421541533))

```
--target-platform android-arm
```


## 自分のプロフィールを見ることができる

下のように、スクロールするとヘッダーが変化するWidgetを使っています。[^3]

[^3]: 使ってみたかっただけです。

![profile.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/2ef503df-0dcc-605e-d653-bfc95a2b778c.gif)

コードはこんな感じです。

```dart
Widget screen = Scaffold(
  body: CustomScrollView(slivers: <Widget>[
    SliverAppBar(
      expandedHeight: _appBarHeight,
      pinned: true,
      elevation: 4.0,
      actions: actions,
      flexibleSpace: FlexibleSpaceBar(
        title: Text(widget.user.name),
        background: Stack(
          fit: StackFit.expand,
          children: <Widget>[
            ImageUtils.cachedImage(widget.user.photoUrl.toString(),
                fit: BoxFit.cover),
            // This gradient ensures that the toolbar icons are distinct
            // against the background image.
            const DecoratedBox(
              decoration: BoxDecoration(
                gradient: LinearGradient(
                  begin: Alignment(0.0, -1.0),
                  end: Alignment(0.0, -0.4),
                  colors: <Color>[Color(0x60000000), Color(0x00000000)],
                ),
              ),
            ),
          ],
        ),
      ),
    ),
    SliverList(
        delegate: SliverChildListDelegate(<Widget>[
      AnnotatedRegion<SystemUiOverlayStyle>(
          value: SystemUiOverlayStyle.dark, child: body),
    ]))
  ]),
);
```

[こちらの動画](https://www.youtube.com/watch?v=R9C5KMJKluE&vl=en)で雰囲気つかめるかと思います。


## アプリ内課金で定期購読できる

このアプリの目玉とも言える（笑）アプリ内課金を実装しました。使用したプラグインは[flutter_inapp_purchase](https://pub.dartlang.org/packages/flutter_inapp_purchase)です。

<img width="375" src="https://qiita-image-store.s3.amazonaws.com/0/22161/340c8a58-ad25-d263-747c-99763c539014.png">


このプラグインの使い方は[README](https://pub.dartlang.org/packages/flutter_inapp_purchase#-readme-tab-)を見てもらうとわかるのですが、このプラグインから投げられる例外が微妙で、エラー処理がちょっと大変でした。

たとえば、下図はいったん購入しようとしたけどキャンセルした場合の例ですが、PlatformExceptionが投げれるのは良いのですが、[codeがTAG](https://github.com/dooboolab/flutter_inapp_purchase/blob/a249e5fe7563a958c19484db7988362cad3bcb1e/android/src/main/java/com/dooboolab/flutterinapppurchase/AndroidInappPurchasePlugin.java#L432)になっていて、どの例外もcodeで判断できません。
![image.png](https://qiita-image-store.s3.amazonaws.com/0/22161/5f697635-f2c2-b1b4-70ee-8d431e50b970.png)

よく見るとdetailsの「responseCode: 1」の[1がCancelを表すエラーコード](https://developer.android.com/reference/com/android/billingclient/api/BillingClient.BillingResponse.html#USER_CANCELED)になっていますので、それで判断するしか無さそうです。

先程のはAndroidでしたが、同じキャンセルでもiOSでキャンセルすると違うcodeが返ってきます。。

![image.png](https://qiita-image-store.s3.amazonaws.com/0/22161/2235b4d3-caa5-1613-4a06-718232a87857.png)

なので、アプリのコードとしては以下のようにしました。

```dart
Future<MyPurchaseItem.PurchasedItem> buySubscription(String productId) =>
    FlutterInappPurchase.buySubscription(productId)
        .then((item) => _purchasedTranslator
            .toModel(_translateToPurchasedItemEntity(item)))
        .catchError((error) {
      // エラーコード統一してほしい・・・
      if (Platform.isAndroid) {
        if (error.code == "InappPurchasePlugin" && error.details is String) {
          // https://developer.android.com/reference/com/android/billingclient/ap
          String details = error.details as String;
          String USER_CANCELED = "1";
          if (details.endsWith(USER_CANCELED)) {
            return;
          }
        }
      } else if (Platform.isIOS) {
        // https://github.com/dooboolab/flutter_inapp_purchase/blob/a249e5fe7563a
        if (error.code == "E_USER_CANCELLED") {
          return;
        }
      }
      throw error;
    });
```

このプラグインが使えないとか言いたいわけではなくて、自分がプラグインを作るときは以下の点に気をつけたいなぁと思いました。

- error codeは適切なコードにする
- error codeはiOS、Androidで統一する
- アプリケーション側でエラーコードを変数で扱えるようにする
    - たとえば[こんなイメージ](https://github.com/flutter/plugins/blob/master/packages/firebase_storage/lib/src/error.dart)です。

あ、アプリ内課金そのものについて触れてませんでしたね。。とはいえ、Flutterというよりそれぞれのプラットフォームの話なので詳細には触れませんが、参考リンクだけ張っておきます。

- [自動購読課金について【Android編】](https://ameblo.jp/principia-ca/entry-12071725733.html)
- [自動購読課金について【iOS編】](https://ameblo.jp/principia-ca/entry-12071724382.html?frm=theme)
- [In-App Purchaseプログラミングガイド](https://developer.apple.com/jp/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Chapters/Subscriptions.html)
- [In-app Billing API](https://developer.android.com/google/play/billing/api)
- [go-iap](https://github.com/awa/go-iap)[^4]

[^4]: 定期購読が停止されてるかどうかをチェックするためのサーバーをGoで作成しましたが、そのときに使わせてもらったライブラリです。


# その他 細かいけど必要な機能

細かいけど必要だったり便利な機能を見ていきたいと思います。

- メッセージを送信したら通知が届く
- メッセージ入力中に画面を戻ってしまったなどのメッセージが消えないようにする
- チャットメッセージにURLがあるとタップできてリンクを開くことができる
- チャットメッセージの画像を拡大できる
- チャットメッセージの画像を横スワイプで切り替えることができる
- チャットメッセージの画像を上下スワイプで閉じることができる
- 開発者のための機能
    - analytics
    - crashlytics
    - AdMob
- リリースまでに必要なの
    - ランチャーアイコン
    - スクリーンショット


## メッセージを送信したら通知が届く

[firebase_messaging](https://pub.dartlang.org/packages/firebase_messaging)プラグインを使って、roomIdとuserIdをトピックに登録しています。

subscribe,unsubscribeは簡単です。

```dart
FirebaseMessaging _firebaseMessaging = FirebaseMessaging();
_firebaseMessaging.subscribeToTopic(topic);
_firebaseMessaging.unsubscribeFromTopic(topic);
```

Cloud Functionsがfirestoreのメッセージコレクションを監視して、追加があったらCloud Functionsがプッシュを投げるようにしています。(Cloud FunctionsのFCMの[サンプル](https://github.com/firebase/functions-samples/tree/Node-8/fcm-notifications)が参考になるかと思います)

## メッセージ入力中に画面を戻ってしまったなどのメッセージが消えないようにする

長文を書いてて間違って戻ってしまった場合、長文のメッセージが消えてしまったとかはいやなので、[shared_preferences](https://pub.dartlang.org/packages/shared_preferences)プラグインを使って`dispose`のタイミングでローカルストレージに保存しています。

```dart
Future<bool> saveMessage(Message message, String roomId) async {
  SharedPreferences prefs = await SharedPreferences.getInstance();
  return prefs.setString(
      _createMessageKey(roomId), message.content);
}

String _createMessageKey(String roomId) =>
    "${SharedPreferenceType.message.toString()}/$roomId";
```

画面に戻ってきたときは、`initState`のタイミングで保存したメッセージを読み込んでTextFieldにセットしています。

```dart
Future<String> getMessage(String roomId) async {
  SharedPreferences prefs = await SharedPreferences.getInstance();
  return prefs.getString(_createMessageKey(roomId));
}
```

```dart
getMessage(widget.room.id).then((message) {
  setState(() {
    _texEditingController.text = message;
  });
});
```


## チャットメッセージにURLがあるとタップしてリンクを開くことができる

![link.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/37b4116e-14fc-da30-e43a-d43acf8bc124.gif)


あんまりスマートではないのですが、実装例です。

```dart
class Message {
  String id;
  String content;

  List<LinkText> get linkTextList {
    if (content == null) {
      return null;
    }

    List<LinkText> results = List();
    // https://stackoverflow.com/a/6041965
    RegExp exp = RegExp(
        r'(http|https)://([\w_-]+(?:(?:\.[\w_-]+)+))([\w.,@?^=%&:/~+#-]*[\w@?^=%&/~+#-])?');

    String _content = content;

    do {
      if (_content.length == 0) {
        return results;
      }

      Match match = exp.firstMatch(_content);
      if (match == null) {
        results.add(LinkText(_content));
        return results;
      }

      // urlが見つかるまでにテキストがあれば追加する
      String str = _content.substring(0, match.start);
      if (str.length > 0) {
        results.add(LinkText(str));
      }
      String url = match.group(0);
      results.add(LinkText(url, url: url));
      _content = _content.substring(match.end, _content.length);
    } while (_content.length > 0);
    return results;
  }

  Message(this.id, this.content);
}

class LinkText {
  String text;
  String url;

  LinkText(this.text, {this.url});
}
```

Messageクラスのcontentに入力したメッセージが入る想定で、Widget側で次のように使っています。

```dart
Widget _buildMessageWidget(BuildContext context, Message message) {
  final TextStyle aboutTextStyle = Theme.of(context).textTheme.body2;
  List<TextSpan> children = message.linkTextList.map((linkText) {
    // textがnullではなくurlがnullならリンクにしない。
    // textもurlもnullではない場合、urlに遷移するリンクにする。
    if (linkText.url == null) {
      return TextSpan(style: aboutTextStyle, text: linkText.text);
    }
    return LinkTextSpan(
        context: context, text: linkText.text, url: linkText.url);
  }).toList();
  return Container(
    child: RichText(
      text: TextSpan(
        style: TextStyle(fontSize: 16.0),
        children: children,
      ),
    ),
  );
}
```
LinkTextSpanクラスは次のとおりです。

```dart
import 'package:flutter/gestures.dart';
import 'package:flutter/material.dart';
import 'package:url_launcher/url_launcher.dart';

// https://github.com/flutter/flutter/blob/master/examples/flutter_gallery/lib/gallery/about.dart#L11-L33
class LinkTextSpan extends TextSpan {
  LinkTextSpan(
      {BuildContext context,
      TextStyle style,
      String url,
      String text,
      bool inAppWebView = false})
      : super(
            style: style ?? url != null
                ? TextStyle(
                    color: Colors.blue,
                    decoration: TextDecoration.underline,
                  )
                : Theme.of(context).textTheme.body2,
            text: text ?? url,
            recognizer: TapGestureRecognizer()
              ..onTap = () {
                launch(url, forceWebView: inAppWebView);
              });
}
```

`launch`は[url_launcher](https://pub.dartlang.org/packages/url_launcher)プラグインで、URLを開くことができます。

## チャットメッセージの画像を拡大できる

画像の拡大には[photo_view](https://pub.dartlang.org/packages/photo_view)プラグインを使っています。

```dart
Container(
  child: Center(
    child: PhotoView(
      imageProvider: NetworkImage(imageUrl),
      minScale: PhotoViewComputedScale.contained * 1.0,
      maxScale: 2.0,
    ),
  ),
);
```
## チャットメッセージの画像を横スワイプで切り替えることができる

![swipe.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/30e120ef-cde9-d9fb-8371-86b7ea1e59eb.gif)


画像を横スワイプで切り替えるのに、[DefaultTabController](https://docs.flutter.io/flutter/material/DefaultTabController-class.html)と[TabBarView](https://docs.flutter.io/flutter/material/TabBarView-class.html)クラスを組み合わせて使っています。

全体のコードはこちらの[gist](https://gist.github.com/kwmt/ba4701b2b80b3675e7c58ea9b841f45f)を見ていただければと思いますが、雰囲気は下記のような感じです。


```dart
DefaultTabController(
      initialIndex: position,
      length: imageUrls.length,
      child: _PageSelector(imageUrls: imageUrls, position: position),
    );
```

`_PageSelector`はStatelessWidgetになっていて、下記を実装しています。

```dart
child: TabBarView(
    children: imageUrls.map((String imageUrl) {
  return Container(
    child: Center(
      child: PhotoView(
        imageProvider: NetworkImage(imageUrl),
        minScale: PhotoViewComputedScale.contained * 1.0,
        maxScale: 2.0,
      ),
    ),
  );
}).toList()),
```


## チャットメッセージの画像を上下スワイプで閉じることができる

![dismissable.gif](https://qiita-image-store.s3.amazonaws.com/0/22161/abf30870-8ecf-e758-bcfe-736ec74e1d96.gif)


画面全体のWidget(ここではScaffold)を[`Dismissable`](https://docs.flutter.io/flutter/widgets/Dismissible-class.html)でWrapするだけです。[^5]

[^5]: 簡単すぎて感動しました!

```dart
 Dismissible(
  key: key,
  direction: DismissDirection.vertical,
  onDismissed: (direction) {
    Navigator.pop(context);
  },
  child: Scaffold(appBar: AppBar(省略...
);
```




## 開発者のための機能


### analytics

GoogleAnalyticsも[firebase_analytics](https://pub.dartlang.org/packages/firebase_analytics)プラグインがあって、スクリーン名やイベントを取ることができます。

<img height="300" src="https://qiita-image-store.s3.amazonaws.com/0/22161/672aab2e-1280-0297-30c9-6d3d607f54f9.png">

<img width="300" src="https://qiita-image-store.s3.amazonaws.com/0/22161/7d28f14b-5d89-3895-ef55-6777e9ebae78.png">




### crashlytics

crashlyticsはGoogle公式ではありませんが、[flutter_crashlytics](https://pub.dartlang.org/packages/flutter_crashlytics)プラグインがあります。[^6][^7]

[^6]: [issue](https://github.com/flutter/flutter/issues/14765)があります。公式サポートしてほしいと思っている人たちは多そうなので期待しています。

[^7]: Flutterプロジェクトが古いとiOSでビルドできない状態になってました。Podfileが少し変わった？ようで、その部分を変えたらビルドできるようになりました。新しいプロジェクトなら動くかと思います。


<img height="300" src="https://qiita-image-store.s3.amazonaws.com/0/22161/b3ac6874-1ec3-0ece-a7f5-7abcd5be6142.png">
このようにスタックトレースを表示してくれています。



### AdMob

広告を表示するために、[firebase_admob](https://pub.dartlang.org/packages/firebase_admob)を使用しました。バナー、インタースティシャル、リワードに対応していますが、READMEの[limitations](https://github.com/flutter/plugins/tree/master/packages/firebase_admob#limitations)に書いてあるようにいくつか制限があります。
バナー広告は画面の上か下しか配置できなかったり、リストの途中に広告を挟むことができないのは使えないと思うかも知れませんが、[PlatformView](https://medium.com/flutter-community/flutter-platformview-how-to-create-flutter-widgets-from-native-views-366e378115b6)がAndroid、iOSに対応したので今後に期待しています。([先日のアドベントカレンダーにも投稿されましたね](https://qiita.com/kikuchy/items/397b7664ad32656748bf))

今回バナーはデザイン的に使いたくなったので、インタースティシャル広告を実装しました。
ルームやプロフィールを編集する画面があるのですが、その編集画面に遷移するタイミングで20%の確率で広告を表示しています。

プロフィール画面のinitStateでloadします

```dart
Future<bool> loadInterstitial() {
  _interstitialAd?.dispose();
  _interstitialAd = _createInterstitialAd()..load();
  return Future.value(true);
}

InterstitialAd _createInterstitialAd() => InterstitialAd(
      adUnitId: _appConfig.adMobIds.interstitialUnitId,
      listener: (MobileAdEvent event) {},
    );
```

編集画面へ遷移するボタンタップ時のタイミングで広告を表示しています。

```dart
Future<bool> showInterstitial() {
  return _interstitialAd.isLoaded().then((loaded) {
    if (loaded) {
      _interstitialAd?.show();
      return true;
    }
    return false;
  });
}
```

ちなみに20%の確率の実装は[`Random`](https://api.dartlang.org/stable/2.1.0/dart-math/Random-class.html)を使うとよさそうです。[^8]

```dart
Random().nextInt(100) <= 20
```

[^8]: `import 'dart:math';`が必要です。


## リリースまでに必要なの
### ランチャーアイコン

ランチャーアイコン（アプリアイコン）はリリースまでには必要になるのですが、サイズが細かく決まってたりしてなれてないと（なれてても？）アイコン作成は大変です。それを便利にするのが、[flutter_launcher_icons](https://pub.dartlang.org/packages/flutter_launcher_icons)プラグインです。

(私の場合は)1024x1024の画像を1つだけ作ってpubspec.yamlに次のように書いて実行すると、Android,iOSの解像度別のアイコンをすべて作成してくれます。

```yaml
  flutter_launcher_icons: "^0.6.0"

flutter_icons:
  android: true
  ios: true
  image_path: "assets/images/launcher-icons/icon-1024x1024.png"
  adaptive_icon_background: "#FFFFFF"
  adaptive_icon_foreground: "assets/images/launcher-icons/icon-1024x1024.png"
```

### スクリーンショット

これもリリースまでには必要になります。こちらはプラグインではないのですが、`flutter screenshot`コマンドが便利だったのでご紹介。

いつもならAndroid Studioと端末をつなげてLogCatからScreen Captureするとか、端末側でスクショとって画像をPCに送るとかしていたのが不要になります。

PCと端末つなげて`flutter screenshot`を実行するだけでスクショを撮ってくれます。これは普通にAndroid,iOS開発中にも使えるので、オススメです。

```sh
% flutter screenshot
Screenshot written to flutter_03.png (160kB).
```


# 紹介した時点でのプラグインのバージョン一覧


| プラグイン      |       　バージョン |
|:-----------------|:------------------|
| [google_sign_in](https://pub.dartlang.org/packages/google_sign_in) | 3.2.4 |
| [firebase_auth](https://pub.dartlang.org/packages/firebase_auth) | 0.15.4 |
| [cloud_firestore](https://pub.dartlang.org/packages/cloud_firestore) | 0.8.2+3 |
| [firebase_messaging](https://pub.dartlang.org/packages/firebase_messaging) | 2.1.0 |
| [flutter_facebook_login](https://pub.dartlang.org/packages/flutter_facebook_login) | 1.1.1 |
| [firebase_storage](https://pub.dartlang.org/packages/firebase_storage) | 1.0.4 |
| [firebase_analytics](https://pub.dartlang.org/packages/firebase_analytics) | 1.0.6 |
| [firebase_admob](https://pub.dartlang.org/packages/firebase_admob) | 0.7.0 |
| [firebase_ml_vision](https://pub.dartlang.org/packages/firebase_ml_vision) | 0.2.0+2 |
| [flutter_crashlytics](https://pub.dartlang.org/packages/flutter_crashlytics) | 0.1.1 |
| [image_picker](https://pub.dartlang.org/packages/image_picker) | 0.4.10 |
| [flutter_native_image](https://github.com/btastic/flutter_native_image) | commit 20947f19[^9] |
| [photo_view](https://pub.dartlang.org/packages/photo_view) | 0.1.0 |
| [cached_network_image](https://pub.dartlang.org/packages/cached_network_image) | 0.4.2 |
| [barcode_scan](https://pub.dartlang.org/packages/barcode_scan) | 0.0.8 |
| [qr_flutter](https://pub.dartlang.org/packages/qr_flutter) | 1.1.5 |
| [url_launcher](https://pub.dartlang.org/packages/url_launcher) | 4.0.2 |
| [flutter_inapp_purchase](https://pub.dartlang.org/packages/flutter_inapp_purchase) | 0.8.5 |
| [shared_preferences](https://pub.dartlang.org/packages/shared_preferences) | 0.4.3 |
| [flutter_launcher_icons](https://pub.dartlang.org/packages/flutter_launcher_icons) | 0.7.0 |

[^9]: https://pub.dartlang.org/ に公開されていないので、コミット番号にしています。


# 本アプリのリリースについて
ここまでお読みいただいた方はもしかしたら触ってみたくなったかもしれませんので、リリース状況についてお伝えします。

## Android
本日バージョン1.0.0リリースです:tada: [^10]
[^10]: と、言ってみたかった。
<a href='https://play.google.com/store/apps/details?id=com.instantonnection.app&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1'><img width="300" alt='Google Play で手に入れよう' src='https://play.google.com/intl/en_us/badges/images/generic/ja_badge_web_generic.png'/></a>

## iOS

iOSはAppStoreに審査出しました。
![image.png](https://qiita-image-store.s3.amazonaws.com/0/22161/f52a10ae-334b-3092-e60c-7afb994479a2.png)

が、[Designガイドライン](https://developer.apple.com/jp/app-store/review/guidelines/#design)に引っかかってリジェクトされました。
おそらくオリジナリティがないとのことだと思うので、AppStoreへのリリースは当分先になるかもしれません。

2019/08/02追記
iOSは3月にAppStoreにリリースできました。リンク貼るの遅くなりました。。。

<a href="https://apps.apple.com/jp/app/%E5%8D%B3%E5%B8%AD%E3%83%81%E3%83%A3%E3%83%83%E3%83%88%E3%82%A2%E3%83%97%E3%83%AA-%E3%82%A4%E3%83%B3%E3%82%B3%E3%83%8D/id1423069453"><img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/22161/dc0e452f-341f-9abf-f8cf-7b19b06238e3.png"/></a>

ちなみに、上記のオリジナリティがないというリジェクトの対応は、オンボーディングをつけることで通りました。



# ソースコードについて ※2020/02/29追記
ここで紹介したチャットアプリのコードをオープンソースにしましたので、全体的なコードを知りたい方はこちらをご覧ください。
https://github.com/kwmt/flutter-inconne
PR、issue、**スター☆**はWelcomeです！


# おわりに

以上、いかがでしたでしょうか。おそらくチャットアプリでの主要な機能については一通りご紹介できたんじゃないかなと思っていますので、この記事読んでチャットアプリが作れそう！と思ってもらえたら嬉しく思います。

チャット機能のみのアプリだとapple様にリジェクトされるようなので、なにかメイン機能+チャット機能とするのがいいのかもしれません。

この記事がFlutter開発者のみなさんの助けになれば幸いです。
ここまでお読み頂きありがとうございました。









