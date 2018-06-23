+++
date = "2018-06-22T09:50:00+09:00"
title = "flutter Google SignInのexampleをやってみた"
draft = false
categories = ["flutter"]
tags = ["flutter", "Google SignIn"]
+++


# はじめに

flutter で google singinをやってみた

https://github.com/flutter/plugins/tree/master/packages/google_sign_in



|  iOS  |  Android |
| ---- | ---- |
|  <img src="/images/2018/06/flutter-google-signin/flutter-google-signin-ios.png" width="300" />  |  <img src="/images/2018/06/flutter-google-signin/flutter-google-signin.png" width="300" />  |

# メモ

- Firebaseのプロジェクトつくったりgoogle-services.jsonやplistを作ってプロジェクトに格納したりするのは、[このYoutube](https://youtu.be/DqJ_KjFzL9I?list=PLOU2XLYxmsIJ7dsVN4iRuA7BT8XHzGtCr)がわかりやすいかも

- AndroidはSHA1のフィンガープリントを追加する必要がある
    - デバッグ用のフィンガープリントの確認方法(パスワードは`android`)
    ```shell
    % keytool -exportcert -list -v \-alias androiddebugkey -keystore ~/.android/debug.keystore 
    ```
    - SHA256も表示されるが、そっちじゃなくてSHA1の方なので注意

- iOSはInfo.plistに下記を設定([README](https://github.com/flutter/plugins/tree/master/packages/google_sign_in#ios-integration)に記載あり)
    ```xml
    <!-- Put me in the [my_project]/ios/Runner/Info.plist file -->
    <!-- Google Sign-in Section -->
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleTypeRole</key>
            <string>Editor</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <!-- TODO Replace this value: -->
                <!-- Copied from GoogleServices-Info.plist key REVERSED_CLIENT_ID -->
                <string>com.googleusercontent.apps.861823949799-vc35cprkp249096uujjn0vvnmcvjppkn</string>
            </array>
        </dict>
    </array>
    <!-- End of the Google Sign-in Section -->
    ```

- [google signinのexample](https://github.com/flutter/plugins/tree/master/packages/google_sign_in/example)は[People API](https://console.developers.google.com/apis/library/people.googleapis.com?q=people)を有効にしないと403が返ってくる

# おまけ

## Androidのリリースビルド方法
下記に詳細に記載されているのでAndroid知らなくてもできそう
https://flutter.io/android-release/#create-a-keystore

## リリースビルドするには

```shell
$ flutter build apk
```

`--debug`をつけるとデバッグビルドになる


## インストールするには

```shell
$ flutter install
```

