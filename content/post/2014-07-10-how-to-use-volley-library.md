---
title: Androidプロジェクトを新規作成してVolleyが使えるようになるまで
author: kwmt
layout: post
date: 2014-07-10
url: /index.php/2014/07/10/how-to-use-volley-library/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - Android
  - Volley

---
サンプルプロジェクトは、<a href="https://github.com/kwmt/volleysample" target="_blank">github</a>におきました。

## Androidプロジェクトを新規作成する

Android StudioでNew Projectを作成する (例プロジェクト名：VolleySample)

## volleyプロジェクトを追加する

volleyプロジェクトを作成したAndroidプロジェクトにsubmoduleとして追加・設定する

  1. 作成したプロジェクトに移動してgit init
<pre class="go">$ cd VolleySample
$ git init
</pre>

  2. 必要なら.gitignoreに追加してコミット
  3. volleyプロジェクトを追加する
<!--more-->

<pre class="go">$ git submodule add https://android.googlesource.com/platform/frameworks/volley modules/volley
</pre>

  * VolleySample/modules/volley にcloneされる
  * .gitmudulesに自動的に追加される

<pre class="go">$ git diff --cached
--- /dev/null
+++ b/.gitmodules
@@ -0,0 +1,3 @@
+[submodule "modules/volley"]
+       path = modules/volley
+       url = https://android.googlesource.com/platform/frameworks/volley

--- /dev/null
+++ b/volley
@@ -0,0 +1 @@
+Subproject commit 0e406xxxxxxxx
</pre>

  4. setting.gradleに、volleyプロジェクト追加した時のパスを追加する
<pre class="go">$ git diff
--- a/settings.gradle
+++ b/settings.gradle
@@ -1 +1 @@
-include ':app'
+include ':app',':modules:volley'
</pre>

  5. volleyを使いたいプロジェクトにvolleyの依存設定を追加
<pre class="go">$ git diff
--- a/app/build.gradle
+++ b/app/build.gradle
@@ -21,4 +21,5 @@ android {
 
 dependencies {
     compile fileTree(dir: 'libs', include: ['*.jar'])
+    compile project(':modules:volley')
 }
</pre>

  6. 以下はvolleyがアップデートされるまでに必要な対応
volleyの SDK build tools revisionを19.1.0に修正する

本来は上記の依存設定まででいいはずだが、それだけだと下記のエラーが出るので仕方がなさそう

<pre class="go">Error:The SDK Build Tools revision (19.0.0) is too low for project ':modules:volley'. Minimum required is 19.1.0
</pre>

## Volleyを使ってみる

この記事 <a href="http://bit.ly/1n8SAca" target="_blank">Android working with Volley Library</a> を参考にvolleyを使ってみます。

この記事は簡単に言うと、Applicationを継承したシングルトンクラスを使うといい感じに使えるよって感じです。

### まずApplicationを継承したAppControllerを作成します

{{<gist 5bb307d224a5d7324890>}}

ここでは画像は扱っていません。

### AndroidManifest.xmlに追記

アプリが起動すると自動的に起動させるように、AndroidManifest.xmlにシングルトンクラスを追記します。あと、ネットワークを使用しますので、INTERNET権限を追加します。

<pre class="go">$ git diff
--- a/app/src/main/AndroidManifest.xml
+++ b/app/src/main/AndroidManifest.xml
@@ -2,7 +2,9 @@
 &lt;manifest xmlns:android="http://schemas.android.com/apk/res/android"
     package="net.kwmt27.volleysample" &gt;
 
+    &lt;uses-permission android:name="android.permission.INTERNET" /&gt;
     &lt;application
+        android:name="net.kwmt27.volleysample.app.AppController"
         android:allowBackup="true"
         android:icon="@drawable/ic_launcher"
         android:label="@string/app_name"
</pre>

### 実際に使ってみます

適当に`MainActivity`の`onCreate()`に適当なリクエストをなげて、レスポンスを確認します。

<pre class="go">@Override
     protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
         setContentView(R.layout.activity_main);
+
+        request();
+    }
+
+    private void request() {
+        // Tag used to cancel the request
+        String tag_json_obj = "json_obj_req";
+
+        String url = "http://api.androidhive.info/volley/person_object.json";
+
+        JsonObjectRequest jsonObjReq = new JsonObjectRequest(Request.Method.GET,
+                url, null,
+                new Response.Listener&lt;JSONObject>() {
+
+                    @Override
+                    public void onResponse(JSONObject response) {
+                        Log.d(TAG, response.toString());
+
+                    }
+                }, new Response.ErrorListener() {
+
+            @Override
+            public void onErrorResponse(VolleyError error) {
+                VolleyLog.d(TAG, "Error: " + error.getMessage());
+            }
+        });
+
+        // Adding request to request queue
+        AppController.getInstance().addToRequestQueue(jsonObjReq, tag_json_obj);
     }

</pre>

### あとはビルド・起動して確認

<pre class="go">D/MainActivity﹕ {"email":"ravi8x@gmail.com","phone":{"home":"08947 000000","mobile":"9999999999"},"name":"Ravi Tamada"}
</pre>

こんな感じでlogcatに出ていれば使えています。

## エラーハンドリング

先ほどの`request`メソッドに書いてますが、正常にレスポンスが返ってくれば`onResponse`が呼ばれます。では、エラーの場合はどうなるのか？それは`onErrorResponse`が呼ばれます。上記のコードでは、ログ出力しているだけですが、エラーハンドリングする必要が出てくると思います。その時は、下記のようにして、それぞれに対応する処理を書いてあげれば良いです。

<pre class="go">if( error instanceof NetworkError) {
    } else if( error instanceof ClientError) {
    } else if( error instanceof ServerError) {
    } else if( error instanceof AuthFailureError) {
    } else if( error instanceof ParseError) {
    } else if( error instanceof NoConnectionError) {
    } else if( error instanceof TimeoutError) {
    }
</pre>

## 参考

・<a href="http://bit.ly/1iXgweq" target="_blank">Android Studio で Volley を使う &#8211; ひだまりソケットは壊れない</a>
  
・<a href="http://bit.ly/1oVwQNI" target="_blank">Volleyを使うのがかなり簡単になってた &#8211; Islands in the byte stream </a>
  
・<a href="http://bit.ly/1fDRoJt" target="_blank">Git submodule の基礎 &#8211; Qiita</a>
  
・<a href="http://bit.ly/1oVzEdN" target="_blank">Git diff says subproject is dirty &#8211; Stack Overflow </a>
  
・<a href="http://bit.ly/1n8SAca" target="_blank">Android working with Volley Library</a>
  
・<a href="http://bit.ly/1oFnBR1" target="_blank">Android: How handle message error from the server using Volley? &#8211; Stack Overflow</a>