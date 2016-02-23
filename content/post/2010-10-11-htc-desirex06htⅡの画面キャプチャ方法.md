---
title: HTC Desire(X06HTⅡ)の画面キャプチャ方法
author: kwmt
layout: post
date: 2010-10-11
url: /index.php/2010/10/11/htc-desirex06htの画面キャプチャ方法/
categories:
  - Android
tags:
  - Android

---
Android好きな人たちにとっては、だいぶいまさら感はあるかもしれませんが、HTC Desire(X06HTⅡ)端末の画面をキャプチャしたくていろいろ調べましたが、PCとのUSB接続しないとキャプチャできないようです。
  
以下のサイトが大変参考になりました。
  
[八角研究所 : Android で再開する Java プログラミング（8） &#8211; docomo HT-03A を入手～実機で Android アプリを動かしてみよう][1]
  
<a href="http://www.hakkaku.net/articles/20090723-535" target="_blank">(別窓で開く)</a>
  
ですが、自分なりにまとめてみようと思い筆をとった次第です。
  
【やりたいこと】

* HTC Desire(X06HTⅡ)端末の画面をキャプチャしたい


  
【現状】</p> 

* HTC Desire(X06HTⅡ)端末をUSB接続して、Android SDKのtoolsフォルダにあるddms.batをクリックして、「Dalvik Debug Monitor」を起動しても端末を認識してくれない。


  
※Android SDKは下記サイトからダウンロード可能です。
  
<http://developer.android.com/sdk/index.html>
  
<a href="http://developer.android.com/sdk/index.html" target="_blank">(別窓で開く)</a>
  
【対策】</p> 

* * *・

<del><a href="http://www.htc.com/tw/SupportDownload.aspx?p_id=312&cat=3&dl_id=982">HTC社公式サイト（中国語）</a>からドライバをダウンロードして、インストールする。<br /> <a href="http://www.htc.com/tw/SupportDownload.aspx?p_id=312&cat=3&dl_id=982" target="_blank">(別窓で開く)</a><br /> ※HTC社公式サイトのページの最下部</del>
  
<img  alt="HTC社サイトからドライバーをダウンロードする"
src="http://androg.up.seesaa.net/image/download_driver_from_HTC-thumbnail2.JPG" width="320" height="304" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/download_driver_from_HTC.JPG"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
※Asia2を選択したのは、1度Asia1を選択してダウンロードを試みたが、僕のところの環境では遅かったため、Asia2を選択しただけです。
  
2011/01/29追記
  
上記のリンク先が変わっていたので、下記に変更しました！
  
・[HTC社公式サイト（中国語）][2]からドライバをダウンロードして、インストールする。
  
<a href="http://www.htc.com/tw/SupportDownload.aspx?p_id=312&cat=3&dl_id=1073" target="_blank">(別窓で開く)</a>
  
※HTC社公式サイトのページの最下部
  
<img  alt="HTC社サイトからドライバーをダウンロードする"
src="http://androg.up.seesaa.net/image/download_driver_from_HTC_new-thumbnail2.PNG" width="320" height="304" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/download_driver_from_HTC_new.PNG"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
&#8211;2011/01/29追記ここまで&#8211;
  
・端末を繋いでいたら、いったん外し再度接続する。
  
このとき、USBデバッグにチェックを入れておく。
  
<img  alt="端末側の設定：USBデバッグにチェックを入れる"
src="http://androg.up.seesaa.net/image/X06HT2_USBdebug-thumbnail2.png" width="192" height="320" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/X06HT2_USBdebug.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
【備考】</p> 

* * *HTC Sync（iTunesみたいな物？）という余計なものまでインストールされます。


  
<img  alt="HTC Sync 起動画面"
src="http://androg.up.seesaa.net/image/HTCSync-thumbnail2.PNG" width="320" height="212" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/HTCSync.PNG"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
【結果】</p> 

* * *もう上で結果が出ていますが、今回キャプチャしたかったのは、Android2.2にする前に、2.1の情報を記念に残しておきたかっただけですので、結果はこうなります。


  
<img  alt="HTC Sync 起動画面"
src="http://androg.up.seesaa.net/image/X06HT2_device_info-thumbnail2.png" width="192" height="320" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/X06HT2_device_info.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
以上です。</p>

 [1]: http://www.hakkaku.net/articles/20090723-535
 [2]: http://www.htc.com/tw/SupportDownload.aspx?p_id=312&cat=3&dl_id=1073