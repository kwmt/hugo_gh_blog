+++
date = "2018-10-27T16:15:00+09:00"
title = "FlutterのAndroid Studio3.2.1対応したらエラーが出たので修正した"
draft = false
categories = ["Flutter"]
tags = ["Flutter","Android"]

+++

# 現象
gradleプラグイン(android/build.gradle)のバージョンを3.2.1にしたら、

```
'Could not find lint-gradle-api.jar'
```
と出て、Androidでビルドができなくなった。。



# 原因
直接的な原因はFlutterがAndroid Stdio3.2に対応していないから。


# 対策
FlutterSDKにある

```
flutter/packages/flutter_tools/gradle/flutter.gradle
```

を開いて次のように変更するだけでよさそう。

```
index 390bb2deb..1e0f35c47 100644
--- a/packages/flutter_tools/gradle/flutter.gradle
+++ b/packages/flutter_tools/gradle/flutter.gradle
@@ -18,10 +18,8 @@ import org.gradle.api.tasks.bundling.Jar
 
 buildscript {
     repositories {
+        google()
         jcenter()
-        maven {
-            url 'https://dl.google.com/dl/android/maven2'
-        }
     }
     dependencies {
         classpath 'com.android.tools.build:gradle:3.1.2'
```

あるいは、[flutterリポジトリのmasterブランチは修正されている](https://github.com/flutter/flutter/pull/23397/files)ので、Flutter channelをmasterにするといいかもしれいない。



# 参考

- 関節的な原因: https://developer.android.com/studio/releases/#aapt2_gmaven