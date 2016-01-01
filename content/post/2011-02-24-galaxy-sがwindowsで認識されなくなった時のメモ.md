---
title: Galaxy SがWindowsで認識されなくなった時のメモ
author: kwmt
layout: post
date: 2011-02-24
url: /index.php/2011/02/24/galaxy-sがwindowsで認識されなくなった時のメモ/
categories:
  - Android
tags:
  - Android

---
症状

* * *Galaxy SをWindowsにUSB接続して、cygwinでadb devicesとすると、いままでシリアルナンバーを表示してくれてたのに、下記のように表示されなくなった。


  
$ adb devices
  
List of devices attached
  
原因</p> 

* * *おそらく原因は、Galaxy Sを2.2.1にアップグレードしたときに起動しなくなったので初期化したからだろう。（起動しなくなったときは焦りましたね(^^ゞ初期化したら起動してくれました。）


  
はっきりとは分からないが、ドライバさんが「初期化前と後で違うからわかんない」っていってたのかもしれない。
  
対策</p> 

* * *デバイスマネージャの[Android USB Devices]の[SAMSUNG Android Composite ADB Interface]を削除して、実機を接続しなおした。</p>