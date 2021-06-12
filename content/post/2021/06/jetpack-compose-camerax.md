
+++
title= "Jetpack ComposeでCameraXを実装する"
date= 2021-06-12T21:25:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","camerax"]
keywords = ["Android", "Jetpack", "Compose","camerax"]
+++

# はじめに

composeバージョンは下記です。

{{< gistit "kwmt/JetpackComposePlayGround/blob/main/build.gradle" "3">}}


Jetpack ComposeでのCameraXの実装を試したのでメモしておきたいと思います。
下の動画のようなイメージです。

<img src="/images/2021/06/jetpack-compose-camerax/camera.gif" />

ソースはGitHubにおいてます。
https://github.com/kwmt/JetpackComposePlayGround/tree/main/camerax/src/main/java/net/kwmt27/camerax

実行するときはConfigurationをcameraxを選択してから実行してみてください。

## CameraXはAndroidViewを使う

現在のところ、CameraXを使うにはAndroidViewを使うしかなさそうです。
以下のようにしました。

{{< gistit "kwmt/JetpackComposePlayGround/blob/main/camerax/src/main/java/net/kwmt27/camerax/CameraPreviewScreen.kt" "39:79">}}


## usecaseをバインドする
usecaseをバインドしてるところは下記のところです。
使用しているUseCaseは、Preview、ImageAnalysisです。
この部分はいままでのcomposeを使わない場合と同じですね。

{{< gistit "kwmt/JetpackComposePlayGround/blob/main/camerax/src/main/java/net/kwmt27/camerax/CameraPreviewScreen.kt" "39:79">}}

## RuntimePermissionの実装
カメラはもちろんRuntimePermissionの実装が必要でそれは次のようにやっています。

まず、カメラパーミッションが拒否されたときの画面を作成します。

{{< gistit "kwmt/JetpackComposePlayGround/blob/main/camerax/src/main/java/net/kwmt27/camerax/PermissionNotGrantedView.kt" "27:69">}}

このとき、設定画面にstartActivityResultで行って、戻ってきたときのイベントを伝えるために `callback: () -> Unit`を渡していますので、設定画面から戻ってきたら `handler.request`がよばれます。

{{< gistit "kwmt/JetpackComposePlayGround/blob/main/camerax/src/main/java/net/kwmt27/camerax/CameraXActivity.kt" "19:32">}}

`handler`は`PermissionHandler`という自前のクラスを作っていて、そこでカメラパーミッションが許可されてなければ、設定画面に遷移するし、許可されていれば、`onGranted`というFlowに通知します。

{{< gistit "kwmt/JetpackComposePlayGround/blob/main/camerax/src/main/java/net/kwmt27/camerax/PermissionHandler.kt" "34:47">}}


以上です。