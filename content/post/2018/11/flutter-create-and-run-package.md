
+++
date = "2018-11-28T08:25:00+09:00"
title = "Flutterパッケージを作るにあたって実行の仕方がわからなかったのでメモ"
draft = false
categories = ["Flutter"]
tags = ["Flutter","package"]

+++

## 作成したい名前でパッケージプロジェクトを作成する。

```shell
% flutter create --org net.kwmt27 --template=plugin -i swift -a kotlin flutter_crashlytics
```


## ビルド確認する

`example`まで移動し、Android,iOSともにビルドして、エラーが出ないことを確認します。

```
% cd flutter_crashlytics/example
```

```
% flutter build apk  
```

```
% flutter build ios --no-codesign 
```

## 実行してみる

実行するには、`example`を開いて、Flutterプロジェクトを実行するだけです。

具体的には、最初に作成したパッケージプロジェクト(`flutter_crashlytics`)をAndroid StudioやVSCodeで開きます。

Android Studioの場合は、すでにmain.dartが指定されているはずなので、実行ボタンをおすだけ。

VSCodeの場合は、`example/lib/main.dart`を開いた状態で実行すれば実行できました。

## パッケージ開発

パッケージの開発するには、

- Androidの場合は、Android Studioで `example/androd/build.gradle`を開いて、`FlutterCrashlyticsPlugin.kt`あたりを触ればよさそう。
- iOSの場合は、Xcodeで`example/ios/Runner.xcworkspace`を開いて、`Pods/Development Pods/flutter_crashlytics/../../example/ios/.symlinks/plugins/flutter_crashlytics/ios/Classes/SwiftFlutterCrashlyticsPlugin.swift`をさわることになりそう。（こんな長いのか？？）



