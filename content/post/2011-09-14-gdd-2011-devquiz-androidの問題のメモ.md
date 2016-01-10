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

<pre class="brush: java; title: ; notranslate" title="">package com.google.android.apps.gddquiz;
interface IQuizService {
String getCode();
}
</pre>

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

<pre class="brush: java; title: ; notranslate" title="">package com.google.android.apps.gddquiz;
// import は省略
public class MainActivity Extends Activity {
TextView mTextView;
Button mButton;
@Override
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
mTextView = (TextView)findViewById(R.id.hello);
mButton = (Button)findViewById(R.id.startservice);
// ボタンにリスナーを設定
mButton.setOnClickListener(new startClickListener());
}
// リスナー
public class startClickListener implements OnClickListener {
@Override
public void onClick(View v) {
// bindService()をコール
serviceConnect();
}
}
public void serviceConnect(){
// public abstract boolean bindService (Intent service, ServiceConnection conn, int flags)
bindService(new Intent(com.google.android.apps.gddquiz.IQuizService.class.getName()), // Intent
conn, // ServiceConnection
BIND_AUTO_CREATE); // int:automatically create the service as long as the binding exists.
}
// 以下はServiceにバインドするとき、バインドして何がしたいのかの設定
IQuizService mQuizService;
Boolean mStartedService=false;
private ServiceConnection conn = new ServiceConnection() {
@Override
public void onServiceConnected(ComponentName name, IBinder service) {
Log.d("ServiceConnection","onServiceConnected Start");
mQuizService = com.google.android.apps.gddquiz.IQuizService.Stub.asInterface(service);
mStartedService = true;
getStringInQuizService();
}
@Override
public void onServiceDisconnected(ComponentName name) {
mQuizService = null;
mStartedService = false;
}
};
public void getStringInQuizService(){
super.onResume();
if( mQuizService != null ){
try {
String ans = mQuizService.getCode().toString();
mTextView.setText((String)ans);
Log.d("AnsCode",ans); // 答えをLogCatに出力
//Toast.makeText(this, ans, Toast.LENGTH_LONG).show(); // 先にToast表示したけど、コピペできないじゃん。。。
} catch (RemoteException e) {
}
}
}
}
</pre>

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

<pre class="brush: xml; title: ; notranslate" title="">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;manifest xmlns:android="http://schemas.android.com/apk/res/android"
package="com.google.android.apps.gddquiz"
android:versionCode="1"
android:versionName="1.0"&gt;
&lt;uses-sdk android:minSdkVersion="4" /&gt;
&lt;application android:icon="@drawable/icon" android:label="@string/app_name"&gt;
&lt;activity android:name="com.google.android.apps.gddquiz.MainActivity"
android:label="@string/app_name"&gt;
&lt;intent-filter&gt;
&lt;action android:name="android.intent.action.MAIN" /&gt;
&lt;category android:name="android.intent.category.LAUNCHER" /&gt;
&lt;/intent-filter&gt;
&lt;/activity&gt;
&lt;service android:name="TestService"&gt;
&lt;intent-filter&gt;
&lt;action android:name="com.google.android.apps.gddquiz.IQuizService"/&gt;
&lt;/intent-filter&gt;&gt;
&lt;/service&gt;
&lt;/application&gt;
&lt;/manifest&gt;
</pre>

以上です。AIDL自体は知識としては知っていたけど、初めて使ったなぁ。