---
title: TwitterのFabric SDKをAndroid Studioで使ってみる
author: kwmt
layout: post
date: 2014-11-10
url: /index.php/2014/11/10/how-to-use-twitter-fabric-sdk-with-android-studio/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - Android
  - Android Studio
  - Fabric SDK
  - Twitter

---
## はじめに

ひさびさTwitterをつかってアプリつくろうと思って<a href="https://dev.twitter.com/" target="_blank">ディベロッパーサイト</a>に行ったら、fabric?????とか出てきてなんじゃこれ？と思って調べてたら、10/22(現地時間)に発表されたTwitterのモバイル向け新SDKとやららしい。

> <a href="http://bit.ly/1CYl4HO" target="_blank">Twitter、モバイルアプリ向け新SDK「Fabric」発表　パスワード不要の次世代認証「Digits」リリース &#8211; ITmedia ニュース</a>

まぁ英語でもいいんだけど、日本語の情報ないかなぁと探してみたけど、全然ない(´・ω・\`)

ということで、Android StudioでFabric SDKのセットアップしてから、ツイートできるようになるまでを書いてみようと思います。

## Android版Fabricの公式サイト

<a href="https://dev.twitter.com/twitter-kit/android" target="_blank">https://dev.twitter.com/twitter-kit/android</a>

## 確認したバージョン

<pre class="go">Android Studio：0.9.2
Fabric SDK：1.0.1
</pre>

<!--more-->

## アプリの登録

<a href="https://apps.twitter.com/" target="_blank">https://apps.twitter.com/</a>
  
これは普通に登録するだけ（今までどおり）

## Fabric SDKのセットアップ

IDEプラグインをダウンロードすることもできるみたいだが、Gradleを使っているなら、build.gradleに書くだけで済ませたい。

基本的には下記を参考にすればよいと思うのですが、早速build.gradleの設定ではまった。
  
<a href="https://dev.twitter.com/twitter-kit/android/integrate" target="_blank" class="broken_link">https://dev.twitter.com/twitter-kit/android/integrate</a>

上記書いてあるとおりに記述してもsyncに失敗するので、Fabric SDKを使うには下記のように設定すればsyncは通っていけたっぽい。

<pre class="go">--- a/build.gradle
+++ b/build.gradle
@@ -3,17 +3,21 @@
 buildscript {
     repositories {
         jcenter()
+        maven { url 'https://maven.fabric.io/repo' }
     }
     dependencies {
         classpath 'com.android.tools.build:gradle:0.12.+'
+        classpath 'io.fabric.tools:gradle:1.+'
     }
 }
 
 allprojects {
+    apply plugin: 'io.fabric'
     repositories {
         jcenter()
+        maven { url 'https://maven.fabric.io/repo' }
     }
 }
</pre>

<pre class="go">--- a/app/build.gradle
+++ b/app/build.gradle
@@ -23,5 +37,7 @@ dependencies {
     compile fileTree(dir: 'libs', include: ['*.jar'])
     compile 'com.google.android.gms:play-services:6.1.71'
     compile 'com.android.support:support-v4:21.+'
+    compile('com.twitter.sdk.android:twitter:1.0.0@aar') {
+        transitive = true;
+    }
 }
</pre>

## API KEYとAPI Secretを追加する

<a href="https://dev.twitter.com/twitter-kit/android/integrate" target="_blank" class="broken_link">https://dev.twitter.com/twitter-kit/android/integrate</a> に「Add API Key and Build Secret」があるが、<project>/app/crashlytics.properties にAPI KEYとAPI Secretを書きます。

<pre class="go">twitterConsumerSecret=&lt;YOUR SECRET&gt;
twitterConsumerKey=&lt;YOUR KEY&gt;
</pre>

crashlytics.propertiesに書いたKEYとSECRETの使い方に関しては、下記のリポジトリのbuild.gradleが参考になった。
  
<a href="https://github.com/twitterdev/cannonball-android/blob/master/app/build.gradle" target="_blank">https://github.com/twitterdev/cannonball-android/blob/master/app/build.gradle</a>

僕が書いたのは下記です。

<pre class="go">--- a/app/build.gradle
+++ b/app/build.gradle
@@ -1,5 +1,13 @@ apply plugin: 'com.android.application'
 
+Properties props = new Properties()
+try {
+    props.load(file('./crashlytics.properties').newDataInputStream())
+} catch (Exception ex) {
+    throw new GradleException("Missing crashlytics.properties, check the crashlytics.properties file.");
+}
+
+
 android {
     compileSdkVersion 21
     buildToolsVersion "20.0.0"
@@ -12,7 +20,13 @@ android {
         versionName "1.0"
     }
     buildTypes {
+        debug {
+            buildConfigField "String", "CONSUMER_KEY", "\"${props.getProperty("twitterConsumerKey")}\""
+            buildConfigField "String", "CONSUMER_SECRET", "\"${props.getProperty("twitterConsumerSecret")}\""
+        }
         release {
+            buildConfigField "String", "CONSUMER_KEY", "\"${props.getProperty("twitterConsumerKey")}\""
+            buildConfigField "String", "CONSUMER_SECRET", "\"${props.getProperty("twitterConsumerSecret")}\""
             runProguard false
             proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
         }

</pre>

これは、buildConfigFieldを使っていて、アプリ内でBuildConfigを使ってKEYとSECRETを使えるようにしています。

次のように、ApplicationクラスのサブクラスでAPI KEYとSECRETを使ってFabric SDKの初期化します。

<pre class="go">public class App extends Application{

    @Override
    public void onCreate() {
        super.onCreate();
        TwitterAuthConfig authConfig = new TwitterAuthConfig(BuildConfig.CONSUMER_KEY, BuildConfig.CONSUMER_SECRET);
        Fabric.with(this, new TwitterCore(authConfig));
    }
}
</pre>

※AndroidManifest.xmlに`android:name=".App"`を追加することを忘れないこと。

## Twitterでログインする

<a href="https://dev.twitter.com/twitter-kit/android/twitter-login" target="_blank">https://dev.twitter.com/twitter-kit/android/twitter-login</a>

ログイン画面を作成します。

<pre class="go">&lt;com.twitter.sdk.android.core.identity.TwitterLoginButton
        android:id="@+id/login_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" /&gt;
</pre>

プレビューは「SIGN IN WITH TWITTER」と英語表記ですが、日本語設定の端末で確認すると「Twitterでログイン」と日本語になっています。
  
<img src="http://kwmt27.net/wp-content/uploads/2014/11/sign_in_with_twitter-168x300.png" alt="sign_in_with_twitter" width="168" height="300" class="aligncenter size-thumbnail wp-image-1356" />

Activityの方は公式サイトどおりでいけます。
  
ログイン成功後の`TwitterSession`を受け取る事ができるのですが、`result.data`で取得できます。 

<pre class="go">mTwitterLoginButton = (TwitterLoginButton) findViewById(R.id.login_button);
mTwitterLoginButton.setCallback(new Callback&lt;TwitterSession>() {
	@Override
        public void success(Result&lt;TwitterSession> result) {
		mTwitterSession = result.data;
	}
}	
</pre>

## TwitterのREST APIを使う

<a href="https://dev.twitter.com/twitter-kit/android/api" target="_blank">https://dev.twitter.com/twitter-kit/android/api</a>

公式サイトの例は、１つのツイートを取得しています。
  
<a href="https://dev.twitter.com/rest/reference/get/statuses/show/%3Aid" target="_blank">https://dev.twitter.com/rest/reference/get/statuses/show/%3Aid</a>

僕はツイートしたかったので、こんな感じになりました。

<pre class="go">/**
 * ツイートする。
 * @param message ツイートする内容
 * @see https://dev.twitter.com/rest/reference/post/statuses/update
 */
private void tweet(String message) {
    TwitterApiClient twitterApiClient = TwitterCore.getInstance().getApiClient();
    StatusesService statusesService = twitterApiClient.getStatusesService();

    statusesService.update(message, null, false, null, null, null, false, null, new Callback&lt;Tweet>() {
        @Override
        public void success(Result&lt;Tweet> result) {
            mHandler.post(new Runnable() {
                public void run() {
                    Toast.makeText(self, "ツイートしました。", Toast.LENGTH_SHORT).show();
                }
            });
        }

        public void failure(TwitterException exception) {
            mHandler.post(new Runnable() {
                public void run() {
                    Toast.makeText(self, "ツイートに失敗しました。", Toast.LENGTH_SHORT).show();
                }
            });

        }
    });
}
</pre>

`StatusesService#update`の引数の順番は、REST APIドキュメントの順番どおりです。
  
<a href="https://dev.twitter.com/rest/reference/post/statuses/update" target="_blank">https://dev.twitter.com/rest/reference/post/statuses/update</a>

この`tweet`メソッドを呼びたいタイミングで呼べばツイートできます。

## おわりに

以上で、Fabric SDKのセットアップからツイートまでを書きましたが、<a href="https://dev.twitter.com/twitter-kit/android/tweetui" target="_blank">TweetUi Kit</a>や <a href="https://dev.twitter.com/twitter-kit/android/compose" target="_blank">TweetComposer Kit</a>なるものがあったり、電話番号を使ってサインインする<a href="https://dev.twitter.com/twitter-kit/android/digits" target="_blank">Digits</a>なるものがあるみたいですね。