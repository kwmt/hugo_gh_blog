
+++
title= "Android targetSdkVersionを28から30への変更したときのメモ"
date= 2021-06-12T18:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "targetSdkVersiion"]
keywords = ["Android", "targetSdkVersiion"]
+++



## Activity

### エラー内容
```
e: /Users/kwmt/work/personal/QRCodeReader/core/presentation/src/main/java/net/kwmt27/presentation/MainActivity.kt: (61, 5): 'onRestoreInstanceState' overrides nothing
```

### 原因

onRestoreInstanceStateメソッドの引数のsavedInstanceStateに@NonNullアノテーションがついたため、
protected void onRestoreInstanceState(@NonNull Bundle savedInstanceState)

### 対策

```
-    override fun onRestoreInstanceState(savedInstanceState: Bundle?) {
+    override fun onRestoreInstanceState(savedInstanceState: Bundle) {
         super.onRestoreInstanceState(savedInstanceState)
```

### 該当箇所

- API level 28
https://cs.android.com/android/platform/superproject/+/android-9.0.0_r8:frameworks/base/core/java/android/app/Activity.java;bpv=1;bpt=1;l=1131

- API Level 29
https://cs.android.com/android/platform/superproject/+/android-10.0.0_r30:frameworks/base/core/java/android/app/Activity.java;l=1566


## ConnectivityManager
### エラー内容

```
e: /Users/kwmt/work/personal/QRCodeReader/core/presentation/src/main/java/net/kwmt27/presentation/WifiLifeCycle.kt: (66, 17): 'onAvailable' overrides nothing
e: /Users/kwmt/work/personal/QRCodeReader/core/presentation/src/main/java/net/kwmt27/presentation/WifiLifeCycle.kt: (71, 17): 'onLost' overrides nothing
```

### 原因

これもNonNullアノテーションがついたため

### 対策


### 該当箇所
- onAvailable
https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/net/ConnectivityManager.java;l=3409?q=ConnectivityManager&ss=android%2Fplatform%2Fsuperproject


- onLost
https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/net/ConnectivityManager.java;l=3449?q=ConnectivityManager&ss=android%2Fplatform%2Fsuperproject


## ClipboardManager

### エラー内容
```
e: /Users/kwmt/work/personal/QRCodeReader/core/presentation/src/main/java/net/kwmt27/presentation/common/Navigator.kt: (41, 9): Val cannot be reassigned
```

```kt
val clipboardManager =
             fragment.context?.getSystemService(Context.CLIPBOARD_SERVICE) as? ClipboardManager ?: return
clipboardManager.primaryClip = ClipData.newPlainText("", text)
```

### 原因

そもそもprimaryClipフィールドがなかったのか？
ドキュメントみてもソース見てもない・・・
https://developer.android.com/reference/android/content/ClipboardManager

### 対策

```kt
         val clipboardManager =
             fragment.context?.getSystemService(Context.CLIPBOARD_SERVICE) as? ClipboardManager ?: return
-        clipboardManager.primaryClip = ClipData.newPlainText("", text)
+        clipboardManager.setPrimaryClip(ClipData.newPlainText("", text))
         fragment.toast(fragment.getString(R.string.copied))
     }
```