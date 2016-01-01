---
title: UbuntuにGalaxy Sを認識させるには
author: kwmt
layout: post
date: 2011-02-23
url: /index.php/2011/02/23/ubuntuにgalaxy-sを認識させるには/
categories:
  - Android
tags:
  - Android

---
Ubuntu と Galaxy Sを初めてつないで、Eclipseからrunしようとしたら、Serial Numberが「??????」になってたのでググったときのメモ
  
\# echo &#8216;SUBSYSTEM==&#8221;usb&#8221;, SYSFS{idVendor}==&#8221;04e8&#8243;, MODE=&#8221;0666&#8243;&#8216; >> /etc/udev/rules.d/51-android.rules
  
として、USBを離して再接続でいけた。
  
関係ないが、suだけじゃ認証失敗になるので、その時のメモ

<pre class="terminal">****@ubuntu:~$ sudo su
[sudo] password for ****:
root@ubuntu:/home/****#
</pre>

\****はuser名
  
2011/02/24追記
  
<a href="http://developer.android.com/guide/developing/device.html" target="_blank">Using Hardware Devices | Android Developers</a> によると、
  
上記の&#8221;04e8&#8243;は、USB Vendor IDというものだそうだ。Samsungの場合は&#8221;04e8&#8243;だが、HTCの場合は&#8221;0bb4&#8243;のようだ。ほかのベンダーのIDも決まっている。