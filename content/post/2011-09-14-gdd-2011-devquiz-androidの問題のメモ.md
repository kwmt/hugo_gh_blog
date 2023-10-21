---
title: GDD 2011 DevQuiz Androidの問題のメモ
author: kwmt
layout: post
date: 2011-09-13
url: /index.php/2011/09/14/gdd-2011-devquiz-androidの問題のメモ/
categories:
  - Android
tags:
  - Android

---
GDD 2011 のDevQuizに初めてチャレンジしました。
  
スライドパズルは・・・ですが、AndroidのAIDLの問題は解けたのでそれについて書きたいと思います。

## 問題の概要

以下のAIDLで定義されているサービスを持つAndroid アプリケーションを配布します。
  
プログラム作って解答コードを取得してね。 

{{< gist 6f374b229996e27fdbfbe82ee99bd025 >}}

※AIDL（Android Interface Definition Language）とは、IDLの一種で、プロセス間通信行うためのものです。 

## 準備

Androidプロジェクトを作成する。このとき、パッケージ名は配布アプリケーションと一緒じゃないといけない。
  
IQuizService.aidl ファイルをsrcに作成し、上のコードをコピペ。 

配布されたAndroidアプリケーションを実機でも、エミュレータでもいいからインストールしたら、
  
メールアドレスとパスワードを入れる欄があったので、入力する。
  
メールアドレスはGDD 2011に登録したアドレスで、パスワードは問題文に書いてありました。 

これで準備は完了。後はプログラム書くだけ。 

## 方針は、

  1. アクティビティでボタンを押す→自分のサービスにバインド→配布アプリケーションと通信
  2. LogCatに答えを出す

以上

## Activity

{{< gist e23004c96816ef08e600d279f69468c0 >}}


## Service

bindService()で自分のサービスにバインドし、そこから配布されたアプリケーションにアクセスする。Class名にTestが付いてるけど、気にしない。

<pre class="brush: java; title: ; notranslate" title="">package com.google.android.apps.gddquiz;
//import は省略
public class TestService extends Service {
private final ITestService.Stub mITestServiceBinder = new ITestService.Stub(){
@Override
public void stopService() throws RemoteException {
}
};
@Override
public void onCreate() {
super.onCreate();
// Toastを表示する
Toast.makeText(this, "Service start", Toast.LENGTH_SHORT).show();
}
@Override
public IBinder onBind(Intent intent) {
return null;
}
}
</pre>

## マニフェスト

serviceタグを記述する。

{{< gist 5e6fc34b1daab690b30d833d65862eb6 >}}

以上です。AIDL自体は知識としては知っていたけど、初めて使ったなぁ。