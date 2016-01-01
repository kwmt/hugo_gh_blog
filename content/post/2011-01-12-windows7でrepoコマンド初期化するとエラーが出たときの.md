---
title: Windows7でrepoコマンド初期化するとエラーが出たときの対処方法
author: kwmt
layout: post
date: 2011-01-11
url: /index.php/2011/01/12/windows7でrepoコマンド初期化するとエラーが出たときの/
categories:
  - Android
tags:
  - Android

---
経緯

* * *Androidのソースコードを落とそうと、下記サイトを参考にして、「Androidソースコード ダウンロード手順」の「５．repoコマンド初期化」まできたときに、エラーが出た。


  
[あんどろサイト][1]{.broken_link}
  
エラー内容</p> 

* * *

<pre class="terminal">$ ~/bin/repo init -u git://android.git.kernel.org/platform/manifest.git
としたとき、
～
OSError: [Errno 11] Resource temporarily unavailable
とエラーが出る。
</pre>

原因

* * *下記サイトによると、Python2.6であることが原因


  
<http://survivant.wordpress.com/2010/09/06/git-repository-server-gitosis-on-win7-troubleshooting/></p> 

> If you had this error, it’s because you installed Python 2.6 instead of 2.5. Reinstall Python 2.5 and Setuptool for Python 2.5. 

対策

* * *cygwinをインストールした時のsetup.exeを使用して、Python>python:Python language interpreterが2.6.\*となっているため、&#8221;Keep&#8221;とかになっているところをクリックして、2.5.\*に変更してインストールしなおす。


  
インストールが終わったら、再度repoコマンド初期化（~/bin/repo init -u git://android.git.kernel.org/platform/manifest.git）を実行する。
  
なぜか分からないが、このとき、初期化1回目は失敗し、2回目の実行でうまく初期化できた。
  
ついでに</p> 

* * *repo syncするときも次のようなエラーが出たので、それも書いておこう。</p> 

<pre class="terminal">cygwin\bin\python2.5.exe: *** fatal error - unable to remap
</pre>

これの対処方法は、冒頭のあんどろサイトさんにも書かれてあるが、rebaseallをするとうまくいった。

<pre class="terminal">D:\ProgramFiles\cygwin\bin>ash.exe
$ /usr/bin/rebaseall
/usr/lib/cygicudata.dll: skipped because nonexistent
/usr/lib/cygicui18n.dll: skipped because nonexistent
/usr/lib/cygicuio.dll: skipped because nonexistent
/usr/lib/cygicule.dll: skipped because nonexistent
/usr/lib/cygiculx.dll: skipped because nonexistent
/usr/lib/cygicutu.dll: skipped because nonexistent
/usr/lib/cygicuuc.dll: skipped because nonexistent
FixImage (/usr/x86_64-w64-mingw32/sys-root/mingw/bin/libgcc_s_sjlj-1.dll) failed
with last error = 13
$ exit
</pre>

なんかfailedになったけど、気にせずrepo syncしたら動いてくれたので、見なかったことにしようと思う。
  
そうそう、rebaseallを実行する時は、ash以外にCygwinのプロセスが動いていないことに注意するとのことでした。
  
備考

* * *repoコマンド初期化でエラーが出たOSはWindows 7で、Windows XPでやったときは出ませんでした。もちろんXPでのpythonのバージョンは2.6.5です。2.5.*ではなくてエラーが出なかったのです。</p>

 [1]: http://lucanus.daa.jp/wpbg/android-predev/cygwin