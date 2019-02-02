
+++
date = "2019-02-02T19:55:00+09:00"
title = "Cloud Firestoreプラグインのバージョン0.9.0でビルドエラーが出たのでその対策メモ"
draft = false
categories = ["Flutter"]
tags = ["Flutter","Firestore"]

+++

# はじめに

FlutterのCloud Firestoreプラグインのバージョン0.9.0でBreaking changeな変更が入りました。
新規Flutterプロジェクトにバージョン0.9.0を入れてAndroidのビルドでいくつかビルドエラーになったので、そのときの対応したのをメモしておきたいと思います。



# cannot find symbol import androidx.annotation.NonNull;　と出た

## 現象

次のようなビルドエラーが出ました。

```
<HOME>/.pub-cache/hosted/pub.dartlang.org/cloud_firestore-0.9.0/android/src/main/java/io/flutter/plugins/firebase/cloudfirestore/CloudFirestorePlugin.java:9: error: cannot find symbol
import androidx.annotation.NonNull;
                          ^
  symbol:   class NonNull
  location: package androidx.annotation
```

## 対策

`<Flutterプロジェクトルート>/android/gradle.properties`ファイルに下記を追加

```
android.useAndroidX=true
android.enableJetifier=true
```

## 参考リンク

- https://github.com/flutter/flutter/issues/27156#issuecomment-457951736



# Android resource linking failedと出た

## 現象

```
`FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:processDebugResources'.
> Android resource linking failed
  Output:  <プロジェクトルート>/build/app/intermediates/incremental/mergeDebugResources/merged.dir/values/values.xml:197: error: resource android:attr/fontVariationSettings not found.
  <プロジェクトルート>/build/app/intermediates/incremental/mergeDebugResources/merged.dir/values/values.xml:198: error: resource android:attr/ttcIndex not found.
  error: failed linking references.

  Command: /Users/kwmt/.gradle/caches/transforms-1/files-1.1/aapt2-3.2.1-4818971-osx.jar/c9038cd6a0dd96d07836f000c4b5bb18/aapt2-3.2.1-4818971-osx/aapt2 link -I\
          /Users/kwmt/Library/Android/sdk/platforms/android-27/android.jar\
          --manifest\
          <プロジェクトルート>/build/app/intermediates/merged_manifests/debug/processDebugManifest/merged/AndroidManifest.xml\
          -o\
          <プロジェクトルート>/build/app/intermediates/processed_res/debug/processDebugResources/out/resources-debug.ap_\
          -R\
          @<プロジェクトルート>/build/app/intermediates/incremental/processDebugResources/resources-list-for-resources-debug.ap_.txt\
          --auto-add-overlay\
          --java\
          <プロジェクトルート>/build/app/generated/not_namespaced_r_class_sources/debug/processDebugResources/r\
          --custom-package\
          net.kwmt27.flutterapp\
          -0\
          apk\
          --output-text-symbols\
          <プロジェクトルート>/build/app/intermediates/symbols/debug/R.txt\
          --no-version-vectors
  Daemon:  AAPT2 aapt2-3.2.1-4818971-osx Daemon #0
  Output:  <HOME>/.gradle/caches/transforms-1/files-1.1/core-1.0.0-rc01.aar/a9da9faebecdd1384e093a3d879ee44a/res/values/values.xml:89:5-125:25: AAPT: error: resource android:attr/fontVariationSettings not found.

  <HOME>/.gradle/caches/transforms-1/files-1.1/core-1.0.0-rc01.aar/a9da9faebecdd1384e093a3d879ee44a/res/values/values.xml:89:5-125:25: AAPT: error: resource android:attr/ttcIndex not found.
```

## 対策

`<Flutterプロジェクトルート>/android/app/build.gralde`のcompileSdkVersionを28にする


## 参考リンク

- https://github.com/crosswalk-project/cordova-plugin-crosswalk-webview/issues/207#issuecomment-424038966


# D8: Cannot fit requested classes in a single dex file と出た

## 現象

```
D8: Cannot fit requested classes in a single dex file (# methods: 69621 > 65536)

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:transformDexArchiveWithExternalLibsDexMergerForDebug'.
> com.android.builder.dexing.DexArchiveMergerException: Error while merging dex archives: /Users/kwmt/work/personal/SD-2019-04-flutter/flutter_app/build/app/intermediates/transforms/dexBuilder/debug/6.jar, 
〜省略〜
  The number of method references in a .dex file cannot exceed 64K.
  Learn how to resolve this issue at https://developer.android.com/tools/building/multidex.html

```



## 対策

`<Flutterプロジェクトルート>android/app/build.gradle`のdefaultConfigにmultiDexEnabled trueを追加する。