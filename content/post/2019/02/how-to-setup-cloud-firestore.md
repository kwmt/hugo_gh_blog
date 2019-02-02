
+++
date = "2019-02-02T16:00:00+09:00"
title = "Firebaseのセットアップ方法"
draft = false
categories = ["Flutter"]
tags = ["Flutter","Firebase"]

+++

# はじめに

Cloud Firestoreを始めFirebaseのサービスを使えるようにするためには、Firebaseにプロジェクトを追加し設定する必要があります。
ここではFirebaseにプロジェクトを追加しFlutterプロジェクトでAndroidとiOSでFirebaseを使えるようになるまでの設定方法を見ていきたいと思います。


# Firebaseにプロジェクトを追加

こちらのURL([https://console.firebase.google.com/](https://console.firebase.google.com/))を開き、プロジェクトを追加します。今回の場合は`sd-2019-04-flutter`というプロジェクトを追加しました。

<img src="/images/2019/02/how-to-setup-cloud-firestore/create-new-project.png" />


# AndroidでFirebaseを使えるようにする

AndroidでFirebaseを使えるようにするためには、google-services.jsonというファイルをダウンロードし、Flutterプロジェクトに追加する必要があります。


## google-services.jsonをダウンロードする


そのためにはまず、Firebaseのプロジェクト設定からアプリをAndroidアプリを追加する必要があります。


<img src="/images/2019/02/how-to-setup-cloud-firestore/project-setting.png" />

ドロイド君のアイコンをクリックしてください。

<img src="/images/2019/02/how-to-setup-cloud-firestore/add-app.png" />


すると、Androidパッケージの入力が求められますので、パッケージを入力します。

<img src="/images/2019/02/how-to-setup-cloud-firestore/add-app-android.png" />

パッケージ名を確認するには、Flutterプロジェクトの android/app/build.gradleファイルを開き、`applicationId`を探してください。その設定値がパッケージ名になります。


パッケージを入力したら`google-services.json`がダウンロードできるようになりますので、ダウンロードしてください。


<img src="/images/2019/02/how-to-setup-cloud-firestore/add-app-android2.png" />


## Flutterプロジェクト(Android)でセットアップする

### Flutterプロジェクトにgoogle-services.jsonを格納する

`Flutterプロジェクトルート/android/app`以下に格納してください。

### build.gradleファイルを編集する

`Flutterプロジェクトルート/android/` にある `build.gradle`ファイルを開いて、google-serviceプラグインを追加します。

```gradle
dependencies {
    classpath 'com.android.tools.build:gradle:3.2.1'
    classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    classpath 'com.google.gms:google-services:4.1.0' // ←この行を追加
}
```

次に`Flutterプロジェクトルート/android/app`にある `build.gradle`ファイルを開いて、最下行に下記を追記してください。

```gradle
apply plugin: 'com.google.gms.google-services'
```

これでAndroidに関してはFirebaseのセットアップは終了です。



# iOSでFirebaseを使えるようにする

iOSでFirebaseを使えるようにするためには、GoogleService-Info.plistというファイルをダウンロードし、Flutterプロジェクトに追加する必要があります。

## GoogleService-Info.plistをダウンロードする

そのためにはまず、Firebaseのプロジェクト設定からアプリをAndroidアプリを追加する必要があります。

<img src="/images/2019/02/how-to-setup-cloud-firestore/project-setting.png" />

iOSと書かれたアイコンをクリックしてください。

<img src="/images/2019/02/how-to-setup-cloud-firestore/add-app.png" />


すると、iOSのバンドルIDの入力が求められますので、バンドルIDを入力します。

<img src="/images/2019/02/how-to-setup-cloud-firestore/add-app-ios.png" />

バンドルIDを確認するには、XcodeでFlutterプロジェクトを開いて確認するのが簡単だと思います。

XcodeでFlutterプロジェクトを開くには、Android Studioを使っている場合、Toolsメニューから開くことができます。

dartファイル(例えばmain.dart)を開いている状態で、`Tools->Flutter->Open iOS module in Xcode`をクリックしてください。

<img src="/images/2019/02/how-to-setup-cloud-firestore/open-xcode-from-android-studio.png" />

Xcodeが開いたら、左側のProject Navigatorの`Runner`を選択すると右側に`Bundle Identifier`という項目があるのでその設定値がバンドルIDになります。



バンドルIDを入力する画面に戻って、バンドルIDを入力してアプリを登録します。その後、`GoogleService-Info.plist`がダウンロードできるようになりますので、ダウンロードしてください。


<img src="/images/2019/02/how-to-setup-cloud-firestore/add-app-ios2.png" />


## Flutterプロジェクト(iOS)でセットアップする

### FlutterプロジェクトにGoogleService-Info.plistを格納する

上記でも書きましたが、XcodeでFlutterプロジェクトを開いてください。

Project Navigatorで`Runner/Runner`以下に`GoogleService-Info.plist`ファイルを追加してください。

<img src="/images/2019/02/how-to-setup-cloud-firestore/add-googleservice-info-plist.png" />

これでiOSに関してFirebaseのセットアップは終了です。