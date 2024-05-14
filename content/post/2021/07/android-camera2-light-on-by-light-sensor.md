
+++
title= "Camera2APIでカメラプレビュー中に光センサーの値によってライトを自動でON/OFFにする"
date= 2021-07-30T18:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Camera2", "Sensor"]
keywords = ["Android", "Camera2", "Sensor", "SensorManager"]
+++

# はじめに

1. Camera2APIを使って、カメラプレビュー中に（フラッシュではなく）ライトをONにできるのか？
2. 周りが暗くなったら自動でライトをONにできるか？

というのが気になって調べたのでメモしておく。

[camera-samples](https://github.com/android/camera-samples/tree/main/Camera2Basic)をベースに試した全体的なコードはこちら。
https://github.com/kwmt/camera-samples/pull/1/

## 1. Camera2APIを使って、カメラプレビュー中に（フラッシュではなく）ライトをONにできるのか？


調べている中で、ライトのことを`Torch Mode`というらしいことを知った。


まず目に入ったのが、[`CameraMangaer#setTorchMode`](https://developer.android.com/reference/android/hardware/camera2/CameraManager#setTorchMode(java.lang.String,%20boolean))というメソッドで、これを（ドキュメントを読まずに^^;）試してみたら（カメラプレビュー中にコールする）、[`CAMERA_IN_USE`](https://developer.android.com/reference/android/hardware/camera2/CameraAccessException#CAMERA_IN_USE)という例外がスローされてしまう。


これはよくよくドキュメントを見ると

> Set the flash unit's torch mode of the camera of the given ID without opening the camera device.

> カメラデバイスを開かずに、指定されたIDのカメラのフラッシュユニットのトーチモードを設定します。

とあり、カメラプレビュー中ということはカメラをopenしている状態なので、openしてない場合に使えるメソッドであることがわかった。


別の方法を検討する。


こちらのstackoverflowで次のようなことを見つけた。
https://stackoverflow.com/a/57331014/2520998

> Once the camera is open, you can use FLASH_MODE and set it to TORCH in your preview capture request.
> This means you need to keep AE_MODE to either ON or OFF, not one of the FLASH modes, so that auto-exposure isn't controlling the flash.

> カメラを開いたら、FLASH_MODEを使用して、プレビューキャプチャ要求でTORCHに設定することができます。
> つまり、自動露出がフラッシュを制御しないように、AE_MODEをFLASHモードではなく、ONまたはOFFにしておく必要があります。


camera-samplesの[この部分](https://github.com/android/camera-samples/blob/main/Camera2Basic/app/src/main/java/com/example/android/camera2/basic/fragments/CameraFragment.kt#L215-L220)を次のように変更してみる。

```kotlin
val captureRequest = camera.createCaptureRequest(
        CameraDevice.TEMPLATE_PREVIEW).apply { addTarget(fragmentCameraBinding.viewFinder.holder.surface) }

+ captureRequest.set(CaptureRequest.CONTROL_AE_MODE, CaptureRequest.CONTROL_AE_MODE_ON)
+ captureRequest.set(CaptureRequest.FLASH_MODE, CaptureRequest.FLASH_MODE_TORCH)
// This will keep sending the capture request as frequently as possible until the
// session is torn down or session.stopRepeating() is called
session.setRepeatingRequest(captureRequest.build(), null, cameraHandler)
```

これを実行すると、カメラプレビュー中にライトをONにすることができた。


## 2. 周りが暗くなったら自動でライトをONにできるか？

つぎに、周りが暗くなったら自動でライトをONにできるか？については、問題を2つに分ける。

- 光センサーで光量を測定できるか？
- 動的にライトをON/OFFできるか？

### 光センサーで光量を測定できるか？

光センサーが搭載されているので、光センサーからデータをモニタリングすることができる。公式ドキュメントにそのままのコードがあるので、参考にする。

https://developer.android.com/guide/topics/sensors/sensors_overview?hl=ja#sensors-monitor


camera-samplesで試したコードはこちら。

{{< gistit "kwmt/camera-samples/blob/8f352a21ed4fa29b5446bf940a2495605add62a6/Camera2Basic/app/src/main/java/com/example/android/camera2/basic/fragments/CameraFragment.kt" "205:227">}}



### 動的にライトをON/OFFできるか？

[こちら](https://gist.github.com/royshil/8c760c2485257c85a11cafd958548482)の、タッチしたときにCaptureRequestBuilderをリビルドしていたのがヒントになった。

上記で光量を検知できたので、適当なしきい値を決めて、そのしきい値より小さければOFF、しきい値以上ならONにするには、次のようにCaptureRequestBuilderを設定しなおしてリビルドすればよい。


{{< gistit "kwmt/camera-samples/blob/8f352a21ed4fa29b5446bf940a2495605add62a6/Camera2Basic/app/src/main/java/com/example/android/camera2/basic/fragments/CameraFragment.kt" "295:314">}}


## まとめ
- Camera2APIを使って、カメラプレビュー中に（フラッシュではなく）ライトをONにするには、CaptureRequest.Builderで`CONTROL_AE_MODE`を`CONTROL_AE_MODE_ON`にしてから`FLASH_MODE`を`FLASH_MODE_TORCH`にセットする。
- 周りが暗くなったら自動でライトをONにするには、光センサーで光量を取得できるので、しきい値を設定してCaptureRequest.Builderに`CONTROL_AE_MODE_ON`や`CONTROL_AE_MODE_OFF`にセットし直して`build`する。


次の動画は、周りが暗い状態（夜の部屋）で、カメラプレビュー中に電気を消して暗くしたらライトがONし、電気をつけるとライトがOFFになる動画です。

{{< figure src="/images/2021/07/android-camera2-light-on-by-light-sensor/light.gif" width="300" >}}
