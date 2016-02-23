---
title: Galaxy Sのroot化後、sqlite3が使えなくなった時のメモ
author: kwmt
layout: post
date: 2011-02-02
url: /index.php/2011/02/03/galaxy-sのroot化後sqlite3が使えなくなった時のメモ/
categories:
  - Android
tags:
  - Android

---
昨日の記事（<a href="http://androg.seesaa.net/?1296658010" target="_blank">Galaxy Sをroot化してプチフリ対策をした</a>）でGalaxy Sのrootをとったわけですが、今日sqlite3を使おうとしたら、”ない”って言われたので、そのときのメモ。
  
注）以下の各コマンド実行後の結果は、正確な結果ではありません。忘れちゃいました。
  
$ adb shell
  
$ su
  
\# sqlite3 \***.db
  
sqlite3 not found ←あれ？なんで？
  
ググリました。
  
そしたら、下記サイトで書いてくださってました。
  
<a href="http://d.hatena.ne.jp/kamip/20101216/1292474837" target="_blank" class="broken_link">adb shell後のsqlite3がnot found!!</a>
  
TitaniumBackupに拉致られた？と。
  
ルートディレクトリで find | grep sqlite3 とすると、たしかに下記ディレクトリにありました。
  
/data/data/com.keramidas.TitaniumBackup/files/sqlite3
  
で、上記サイトのとおり、copyしようとすると・・
  
\# cp sqlite3 /system/bin
  
Can&#8217;t creat &#8230; Read-only file system
  
エラーがでました。
  
ググリました。
  
下記サイトで書いてくださってました
  
<a href="http://www.adakoda.com/adakoda/2009/03/android-read-only-file-system.html" target="_blank">[Android] Read-only file system</a>
  
\# mount
  
・・・省略
  
/dev/block/stl9 /system rfs <span style="color:#FF0000;">ro</span>,relatime,vfat,llw,check=no,gid/uid/rwx,iocharset=
  
utf8 0 0
  
・・・省略
  
※Galaxy Sでは「/dev/block/stl9 /system」のようです。
  
\# mount -o rw,remount /dev/block/stl9 /system
  
\# mount
  
・・・省略
  
/dev/block/stl9 /system rfs <span style="color:#FF0000;">rw</span>,relatime,vfat,llw,check=no,gid/uid/rwx,iocharset=
  
utf8 0 0
  
・・・省略
  
ro→rwに変わりました。
  
その後、
  
\# cp sqlite3 /system/bin
  
でコピーできました。
  
終わり。