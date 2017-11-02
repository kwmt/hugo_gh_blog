+++
date = "2017-11-02T00:07:00+09:00"
title = "Android Studio 2.3.3 から3.0にしたときのメモ"
draft = false
categories = ["Android Studio3.0"]

+++

## はじめに

```
     dependencies {
-        classpath 'com.android.tools.build:gradle:2.3.3'
+        classpath 'com.android.tools.build:gradle:3.0.0'
         classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
 
```

と3.0.0にverupしたさいにエラーが出たのでその原因と対策をメモ

## All flavors must now belong to a named flavor dimension.

### エラー内容
app/build.gradle
Error:All flavors must now belong to a named flavor dimension. Learn more at https://d.android.com/r/tools/flavorDimensions-missing-error-message.html

### 原因

> The plugin now requires that all flavors belong to a named flavor dimension—even if you intend to use only a single dimension.

すべてのフレーバーに

### 対策

> To resolve this error, you need to first declare one or more dimensions using the flavorDimensions property. After that, assign each flavor to one of the dimensions you declared, as shown in the sample below. Because the plugin automatically matches dependencies for you, you should name your flavor dimensions carefully. Doing so gives you more control over which code and resources from your local dependencies are matched with each version of your app.

1. `flavorDimensions`プロパティを宣言
1. 各フレーバーに宣言したdimensionを割り当てる

```
// Specifies two flavor dimensions.
flavorDimensions "tier", "minApi"

productFlavors {
     free {
      // Assigns this product flavor to the "tier" flavor dimension. Specifying
      // this property is optional if you are using only one dimension.
      dimension "tier"
      ...
    }

    paid {
      dimension "tier"
      ...
    }

    minApi23 {
        dimension "minApi"
        ...
    }

    minApi18 {
        dimension "minApi"
        ...
    }
}
```


## Error:(32, 54) Type mismatch: inferred type is LayoutInflater? but LayoutInflater was expected

### 原因
```
'com.android.databinding:compiler:3.0.0'
```
にすることで、
```
ViewDataBinding.inflate(LayoutInflater?)
```
だったのが、
```
ViewDataBinding.inflate(LayoutInflater)
```
になったっぽい

### 対策

```
-    override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?,
+    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
```

としました。

## More than one file was found with OS independent path 'META-INF/app_stagingDebug.kotlin_module'

### エラー詳細
Error:Execution failed for task ':app:transformResourcesWithMergeJavaResForStagingDebug'.
> More than one file was found with OS independent path 'META-INF/app_stagingDebug.kotlin_module'

### 対策

```
./gradlew clean
```

でエラーがでなくなったので、verup前のビルドで生成されたものが残ってたのかなぁ。

## Could not find com.android.tools.build:gradle:3.0.0. Searched in the following locations

### エラー詳細

```
* What went wrong:
> Could not resolve all files for configuration ':classpath'.
   > Could not find com.android.tools.build:gradle:3.0.0.
     Searched in the following locations:
         https://jcenter.bintray.com/com/android/tools/build/gradle/3.0.0/gradle-3.0.0.pom
         https://jcenter.bintray.com/com/android/tools/build/gradle/3.0.0/gradle-3.0.0.jar
     Required by:
         project :
```

## 対策

```
--- a/build.gradle
+++ b/build.gradle
@@ -5,6 +5,7 @@ buildscript {
     ext.dokka_version = '0.9.15'
     repositories {
         jcenter()
+        google()
     }
     dependencies {
         classpath 'com.android.tools.build:gradle:3.0.0'
 ```
 