---
title: 'eclipseのConsolに、「Could not find <プロジェクト名>.apk!」と出たときの対処法'
author: kwmt
layout: post
date: 2010-12-19
url: /index.php/2010/12/19/eclipseのconsolにcould-not-find-apkと出たときの対処法/
categories:
  - Android
tags:
  - Android

---
eclipseのConsolに次のように出て起動しない。

```
[2010-12-19 20:57:49 - <プロジェクト名>] Android Launch!
[2010-12-19 20:57:49 - <プロジェクト名>] adb is running normally.
[2010-12-19 20:57:49 - <プロジェクト名>] Could not find <プロジェクト名>.apk!
```

Project→Clean
  
で解決した。
  
参考

* * *

[Could not find MyApp.apk!][1]</p>

 [1]: http://groups.google.com/group/android-beginners/browse_thread/thread/38ed44f6a915ee60?pli=1