---
title: 'Android Studioを1.0にバージョンアップしたら、Gradle DSL method not found: &#8216;runProguard()&#8217;って怒られた場合'
author: kwmt
layout: post
date: 2014-12-09
url: /index.php/2014/12/09/android-studio-1-0-gradle-dsl-method-not-found-runproguard/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - Android
  - Android Studio

---
Android Studioがついに1.0になりましたね。ということで<a href="http://bit.ly/1ueoqlV" target="_blank">ダウンロード</a>して、起動したらgradleのバージョンを1.0.0にしろとかいろいろ怒られたんですが、そこらへんは自動で修正してくれたのに、下記のエラーは自動で修正してくれませんでしたのでメモ。

<pre class="go">Error:(32, 0) Gradle DSL method not found: 'runProguard()'
</pre>

対応は`runProguard`を`minifyEnabled`に変更します。

<pre class="go">diff --git a/app/build.gradle b/app/build.gradle
index 351288d..08b53f8 100644
--- a/app/build.gradle
+++ b/app/build.gradle
@@ -29,7 +29,7 @@ android {
-            runProguard false
+            minifyEnabled false
             proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
         }
     }
</pre>

詳しくは、<a href="http://bit.ly/1ueoeD6" target="_blank">Migrating Gradle Projects to version 1.0.0 &#8211; Android Tools Project Site</a> に記載してあります。