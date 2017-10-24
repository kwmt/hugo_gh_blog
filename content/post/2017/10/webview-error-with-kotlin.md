+++
date = "2017-10-24T23:20:00+09:00"
title = "Kotlinプロジェクトで`Parameter specified as non-null is null: method kotlin.jvm.internal.Intrinsics.checkParameterIsNotNull, parameter favicon`というエラーが出たときの対処法"
draft = false
categories = ["Android"]
tags = ["Android", "Kotlin", "Databinding]

+++

## 現象

Kotlin+DatabindingでViewModel側でwebview.loadURLをしてロードしようとしたら上記のエラーが出た。

```
E/AndroidRuntime: FATAL EXCEPTION: main
Process: <package>, PID: 29932
java.lang.IllegalArgumentException: Parameter specified as non-null is null: method kotlin.jvm.internal.Intrinsics.checkParameterIsNotNull, parameter favicon
    at <package>.HomeViewModel$setupWebView$webViewClient$1.onPageStarted(HomeViewModel.kt:0)at com.android.webview.chromium.WebViewContentsClientAdapter.onPageStarted(WebViewContentsClientAdapter.java:190)
    at org.chromium.android_webview.AwContentsClientCallbackHelper$MyHandler.handleMessage(AwContentsClientCallbackHelper.java:20)
    at android.os.Handler.dispatchMessage(Handler.java:102)
    at android.os.Looper.loop(Looper.java:148)
    at android.app.ActivityThread.main(ActivityThread.java:5417)
    at java.lang.reflect.Method.invoke(Native Method)
    at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:726)
    at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:616)
```

 

## 原因
`WebViewClient`の`onPageStarted`メソッドの引数のBitmapのところが、`favicon:Bitmap` と、non-nullが指定されていたことだった。


## 対策

```
override fun onPageStarted(view: WebView, url: String, favicon: Bitmap?) {
```

Bitmap?をnull許容にしてあげればいい。


## 最後に

ある別プロジェクトからjavaソースをコピペして、Android Studioの自動変換機能にまかせてたので、大丈夫だろうと思っていたことで、気づくのに時間をとってしまった。。
よくエラー内容を見ると`parameter favicon`と書いてはいるんだけど、コピペしているからフレームワークの内部のことかと思ってしまって見過ごしていた。

Android Studioのkotlinへの自動変換を信じすぎないようにしよう。