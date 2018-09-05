+++
date = "2018-09-05T19:45:00+09:00"
title = "Android Studio3.1のrecentProjects.xmlファイルのパス"
draft = false
categories = ["Android"]
tags = ["Android Studio", "Android"]

+++

最近なぜかAndroid StudioのFileメニューのOpen Recentから開くと、Android Studioが固まってしまうという現象に陥ってしまっています。

プロセスをkillして再起動してもダメで、recentから起動するとダメだからそのあたりを消してしまおうということで探した結果、下記にありました。

```
~/Library/Preferences/AndroidStudio3.1/options/recentProjects.xml
```

このファイルを下記のような感じにして保存してAndroid Studioを開き直したら動いてくれました。

```xml
<application>
  <component name="RecentProjectsManager">
  </component>
</application>
```