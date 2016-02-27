+++
categories = ["Android"]
date = "2016-02-27T12:50:27+09:00"
title = "Androidプロジェクトの新規作成からプッシュ通知受信確認まで"
tags = ["Android", "GCM"]

+++


# はじめに

1年ぐらい前にプッシュ通知を実装したときから設定方法が若干変わっていたので、この記事がいつまで有効かはわかりませんが、Androidプロジェクトの新規作成からプッシュ通知受信確認までを書いておこうと思います。

この記事を書いている時点で使用しているAndroid Studioのバージョンは`1.5.1`です。(AS2.0.0beta6が出ててバージョンアップしようか迷ってるという時期です。)


さて、最初にAndroid StudioでEmptyActivityを指定して、PushNotificationSampleという名前のNewProjectを作成しておきます。その前提で以下、話を進めていきます。


## サンプルプロジェクトリポジトリ

全体のプロジェクトは<a href="https://github.com/kwmt/PushNotificationSample" target=_blank>kwmt/PushNotificationSample</a>に置いています。




# Configurationファイル(JSONファイル)を準備する
<a href="https://developers.google.com/mobile/add?platform=android&cntapi=gcm&cnturl=https:%2F%2Fdevelopers.google.com%2Fcloud-messaging%2Fandroid%2Fclient&cntlbl=Continue%20Adding%20GCM%20Support&%3Fconfigured%3Dtrue" target=_blank>Add Google Services</a>
にアクセスして、Configuration fileを作成します。

<img src="http://kwmt27.net/images/2016/02/get-configuration-file.png" width="396" height="300" >

作成したgoogle-services.jsonを`app/`の直下に置きます。

※Flavor毎に異なったgoogle-services.jsonを使いたい場合は、 <a href="https://github.com/googlesamples/google-services/issues/54#issuecomment-165824720" target=_blank>google-services:2.0.0-bata3からできそうという話があった</a>が、実際動かないらしいので、betaとれるの待つしかなのかなぁ。

※google-services-gcmを追加したあと、gradleをsyncする時にgoogle-services.jsonファイルがないと下記のようなエラーが出ますのでご注意下さい。

```
Error:Execution failed for task ':app:processDebugGoogleServices'.
> File google-services.json is missing from module root folder. The Google Services Plugin cannot function without it.
```



# build.gradleを編集

## google-servicesを追加する

### プロジェクトトップの`build.gradle`を編集します。

```
--- a/build.gradle
+++ b/build.gradle
@@ -5,8 +5,9 @@ buildscript {
         jcenter()
     }
     dependencies {
-        classpath 'com.android.tools.build:gradle:1.5.0'
-
+        classpath 'com.android.tools.build:gradle:2.0.0-beta6'
+        // https://develop  ers.google.com/cloud-messaging/android/client
+        classpath 'com.google.gms:google-services:2.0.0-beta6'
         // NOTE: Do not place your application dependencies here; they belong
         // in the individual module build.gradle files
     }
```

このままビルドすると2.10が必須と言われるので、

```
Warning:Gradle version 2.10 is required. Current version is 2.8. If using the gradle wrapper, try editing the distributionUrl in /PushNotificationSample/gradle/wrapper/gradle-wrapper.properties to gradle-2.10-all.zip
```

gradle-wrapper.propertiesファイルを2.10にします。

```
--- a/gradle/wrapper/gradle-wrapper.properties
+++ b/gradle/wrapper/gradle-wrapper.properties
@@ -3,4 +3,4 @@ distributionBase=GRADLE_USER_HOME
 distributionPath=wrapper/dists
 zipStoreBase=GRADLE_USER_HOME
 zipStorePath=wrapper/dists
-distributionUrl=https\://services.gradle.org/distributions/gradle-2.8-all.zip
+distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip
```

ビルドに失敗するようなら、Preferences(⌘,)で、 `Build, Exectuion, Deployment -> Build Tools -> Gradle`に行き、`Project-level settings`のパスが2.8のままになっている可能性があるので、`Use default gradle wrapper`にチェックを選択しておくとよいと思います。

### `app/build.gradle`を編集します


`dependencies`に`play-services-gcm`を追加し、最下部にapply pluginを記入します。
`play-services`にしてないのは、65k method limit対策です。
ただでさえライブラリをたくさん使用するので、余計なライブラリを入れたくないためです。

```
--- a/app/build.gradle
+++ b/app/build.gradle
@@ -23,4 +23,7 @@ dependencies {
     compile fileTree(dir: 'libs', include: ['*.jar'])
     testCompile 'junit:junit:4.12'
     compile 'com.android.support:appcompat-v7:23.1.1'
+    compile 'com.google.android.gms:play-services-gcm:8.4.0'
 }
+
+apply plugin: 'com.google.gms.google-services'
\ No newline at end of file
```


# AndroidManifestを編集

## パーミッションを追加

* `${applicationId}.permission.C2D_MESSAGE`は、他のAndroidアプリからの送信や受信ができないようにするパーミッションです。`${applicationId}`としているのは、これは`app/build.gradle`に記載しているパッケージ名になりますが、開発用のパッケージ名に`applicationIdSuffix`をつけたりするので、`${applicationId}`とすれば直に書き換えなくて良いので楽になるからです。

* `WAKE_LOCK`パーミッションは、オプションで付けても付けなくても良いです。詳細は Techboosterさんの記事<a href="http://techbooster.org/android/application/4429/" target=_blank>WAKELOCKを取得し、SLEEP状態からWAKE状態へ遷移する</a>などを参考にするとよいと思います　。


```
--- a/app/src/main/AndroidManifest.xml
+++ b/app/src/main/AndroidManifest.xml
@@ -2,6 +2,19 @@
 <manifest package="net.kwmt27.pushnotificationsample"
           xmlns:android="http://schemas.android.com/apk/res/android">
 
+    <uses-permission android:name="android.permission.INTERNET"/>
+
+    <!-- GCM permission -->
+    <!--
+        Android: Is it possible to read package name inside AndroidManifest.xml file
+        http://stackoverflow.com/a/25186503
+    -->
+    <permission android:name="${applicationId}.permission.C2D_MESSAGE"
+                android:protectionLevel="signature" />
+    <uses-permission android:name="${applicationId}.permission.C2D_MESSAGE"/>
+    <uses-permission android:name="android.permission.WAKE_LOCK" />
+
+
     <application
         android:allowBackup="true"
         android:icon="@mipmap/ic_launcher"
```


## 必要なクラスを宣言します

`application`タグの中に下記を宣言します。

GCMからアプリに送ったメッセージを扱うGcmReceiver、
RegitrationTokenを取得と端末個別情報登録の担当するRegistrationIntentService、
Registration tokenが更新されたときの処理を担当するInstanceIDListenerService、
Push受信時の処理を担当する(実際にPush通知を受け取ってからの処理を担当する)GcmListenerService。

また、Android4.4KitKat以前をサポートしたい場合は、GcmReceiverにREGISTRATIONパーミッションを付ける必要があります。


```
--- a/app/src/main/AndroidManifest.xml
+++ b/app/src/main/AndroidManifest.xml
@@ -15,6 +28,51 @@
                 <category android:name="android.intent.category.LAUNCHER"/>
             </intent-filter>
         </activity>
+
+        <!-- GCM receiver -->
+        <receiver
+            android:name="com.google.android.gms.gcm.GcmReceiver"
+            android:exported="true"
+            android:permission="com.google.android.c2dm.permission.SEND" >
+            <intent-filter>
+                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
+
+                <!--
+                    If you want to support pre-4.4 KitKat devices, add the following action to the intent filter declaration for the receiver:
+                    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
+                    https://developers.google.com/cloud-messaging/android/client#manifest
+                -->
+                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
+                <category android:name="${applicationId}" />
+            </intent-filter>
+        </receiver>
+
+        <service
+            android:name=".RegistrationIntentService"
+            android:exported="false">
+            <intent-filter>
+                <action android:name="com.google.android.gms.iid.InstanceID"/>
+            </intent-filter>
+        </service>
+
+        <!-- GCM Listener -->
+        <service
+            android:name=".MyGcmListenerService"
+            android:exported="false" >
+            <intent-filter>
+                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
+            </intent-filter>
+        </service>
+
+        <!-- GCM InstanceId listener -->
+        <service
+            android:name=".MyInstanceIDListenerService"
+            android:exported="false">
+            <intent-filter>
+                <action android:name="com.google.android.gms.iid.InstanceID"/>
+            </intent-filter>
+        </service>
+
     </application>
 
 </manifest>
```

# 宣言したクラスを作成します

IntentServiceを継承したRegistrationIntentService、GcmListenerServiceを継承したMyGcmListenerService、InstanceIDListenerServiceを継承したMyInstanceIDListenerServiceを作成します。

以下は基本的に、<a href="https://github.com/googlesamples/google-services/blob/master/android/gcm/app/src/main/java/gcm/play/android/samples/com/gcmquickstart/" target=_blank>googleのsample</a>を参考にしています。

## RegistrationIntentService.java

RegistrationIntentServiceは、GoogleのGCMコネクションサーバーからRegistration Tokenを受け取って、自分のプロジェクトのプッシュ通知のメッセージを送信するAPIサーバーなどにRegistration Tokenを送信するのを担当します。

Registration Tokenを受け取るには `instanceID.getToken(SENDER_ID)`を使用します。`SENDER_ID`はstringリソースID`R.string.gcm_defaultSenderId`を指定していますが、google-services.jsonから取得することができ、`gcm_defaultSenderId`を自分で追加する必要はありません。

```
InstanceID instanceID = InstanceID.getInstance(this);
String token = instanceID.getToken(getString(R.string.gcm_defaultSenderId),
        GoogleCloudMessaging.INSTANCE_ID_SCOPE, null);
sendRegistrationToServer(token);
```

`sendRegistrationToServer`メソッドはprivateで、自分のAPIサーバーにRegistration Tokenを登録し、登録完了したコールバックでPrefereceに、”Registration Tokenをサーバーに登録した"という情報を保存しています。

アプリ起動時に呼ばれることになるため、`MainActivity`で`startService`します。


## MyInstanceIDListenerService.java

`InstanceIDListenerService`を継承した`MyInstanceIDListenerService#onTokenRefresh`メソッドは、Registration tokenが更新された時に呼ばれます。更新されるとAPIサーバーも更新する必要があるため、`onTokenRefresh`メソッド内でRegistration Tokenを受け取ってAPIサーバーに登録する`RegistrationIntentService`を`startService`しています。

```
Intent intent = new Intent(this, RegistrationIntentService.class);
startService(intent);
```

ちなみに、このとき`RegistrationIntentService`はすでにstartしていますが、<a href="http://developer.android.com/intl/ja/reference/android/app/Service.html#ServiceLifecycle" target=_blans>`startService`はネストしない(つまり動いてるサービスはストップしてからstartする)</a>ので、複数サービスはstartされませんので安心です。


## MyGcmListenerService.java

以上でプッシュ通知を受け取る設定ができた感じで、あとは実際にプッシュ通知を受けてどうするかの処理が必要です。それは`GcmListenerService`を継承した`MyGcmListenerService`が担当し、プッシュ通知が来たら`onMessageReceived`メソッドが呼ばれます。


```
@Override
public void onMessageReceived(String from, Bundle data) {
    String message = data.getString("message");
    sendNotification(message);
}
```

`data.getString("message")`で指定している`message`のキーはプッシュ通知を送るサーバーとインターフェースを決める必要があります。
`sendNotification`メソッドはprivateで、Notificationに表示しています。

# プッシュ通知の受信確認

以上で基本的なプッシュ通知の設定は完了ですので、通知が受信できるか確認してみます。
プッシュ通知の送信サーバーを書くのもいいですが、以下のcurlでも確認できます。

```
curl --header "Authorization: key=<Server API Key>" --header Content-Type:"application/json" https://android.googleapis.com/gcm/send -d "{\"registration_ids\":[\"<Registration Token>\"],\"data\":{\"message\":\"Hello\"}}"
```

<img src="http://kwmt27.net/images/2016/02/push-notification.png" >

通知バーにHelloというメッセージが表示されたら成功です。

# PlayServicesのチェック

プッシュ通知設定の最後に、GCMはGooglePlayServicesライブラリに依存していて、このライブラリのバージョンが古かったりすると正しくプッシュ通知が受信できないなどの不具合を生む可能性がありますので、バージョンチェックは必要になるでしょう。
`PlayServicesUtils`というクラスにバージョンを確認する`checkGooglePlayServices`メソッドを記述しています。もしバージョンが古かったりすると、更新してください的なメッセージを表示してくれます。ここでは、更新されなかったらActivityを終了するようにしています。

```
public static boolean checkGooglePlayServices(final Activity activity) {
    GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
    final int resultCode = apiAvailability.isGooglePlayServicesAvailable(activity);
    if (resultCode == ConnectionResult.SUCCESS){
        return true;
    }

    apiAvailability.getErrorDialog(activity, resultCode, 0, new DialogInterface.OnCancelListener() {
        @Override
        public void onCancel(DialogInterface dialogInterface) {
            activity.finish();
        }
    }).show();
    return  false;
}
```

このチェックを利用して、チェックOKなら`RegistrationIntentService`を開始するように`MainActivity`を編集します。

```
-- a/app/src/main/java/net/kwmt27/pushnotificationsample/MainActivity.java
+++ b/app/src/main/java/net/kwmt27/pushnotificationsample/MainActivity.java
     protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
         setContentView(R.layout.activity_main);
+        // GooglePlayServicesのバージョンチェック
+        if (PlayServicesUtils.checkGooglePlayServices(this)) {
+            Intent intent = new Intent(this, RegistrationIntentService.class);
+            startService(intent);
+        }
+    }
+
+    @Override
+    protected void onResume() {
+        super.onResume();
+        // GooglePlayServicesのバージョンチェック
+        PlayServicesUtils.checkGooglePlayServices(this);
     }
 }
```


# 補足

プッシュ通知に関しては以上で終わりです。ただ、Registration TokenをServerに送信したり、送信したことをPreferenceに保存したりしていて、その部分も実装する必要がありますので、その部分についても書いておきます。

## Registration TokenをServerに送信

`RegistrationIntentService`で`sendRegistrationToServer`というメソッドを作成しました。その中身は、HTTP通信で自分のサーバーにRegistration Tokenを送信するという内容になります。
実装方法はAsyncTaskを使ったり、HTTPクライアントライブラリを使ったりいろいろありますが、ここでは <a href="http://square.github.io/okhttp/" target=_blank>okhttp3</a>というHTTPクライントライブラリを使いました。

MVCを想定しているので(<a href="http://konifar.hatenablog.com/entry/2015/04/17/010606" target=_blank>MVPがよさそうという記事</a>もありますが)、モデルからリクエストします。HTTPリクエスト部分を共通化したいため、`ApiRequest`というモデルに実際のHTTPリクエスト処理を記述しています。


## Preferenceに保存する

また、Registration TokenをAPIサーバーに送信したかどうかをPreferenceに保存しますので、保存する処理を`GcmModel`に記述しています。


## Dagger

この記事のサンプルプロジェクトでは <a href="http://square.github.io/dagger/" target=_blank>Dagger</a>も導入していますが、直接プッシュ通知には関係ないため説明は省略します。

## 参考
* <a href="https://developers.google.com/cloud-messaging/android/client" target=_blank>Set up a GCM Client App on Android</a>
* <a href="https://github.com/googlesamples/google-services/tree/master/android/gcm" target=_blank>googlesamples/google-services</a>




