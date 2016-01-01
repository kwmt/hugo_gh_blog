---
title: Galaxy SのメールについてDocomoショップ店員に聞いてきた
author: kwmt
layout: post
date: 2010-11-07
url: /index.php/2010/11/07/galaxy-sのメールについてdocomoショップ店員に聞いてきた/
categories:
  - Android

---
経緯

* * *

<img  alt="#galaxys でspモード契約してるんだけど、docomo.ne.jp のメールって、３G回線じゃないと届かないのかな。ネットワークモードの設定でGSMのみにしていると普通は届かない？？"
src="http://androg.up.seesaa.net/image/question-thumbnail2.png" width="320" height="166" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/question.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
という疑問がでた。（もうここで僕が勘違いしていることがわかる人にはわかるかもしれないけど、このときはまだわかっていませんでした。）
  
つまり、僕はWifi接続のみの状態でdocomo.ne.jpのメール（以下docomoメール）を送受信したいということから出た疑問です。
  
そこでいろいろGalaxy Sをいじってみたところ、ネットワークモードの設定で
  
<img  alt="ネットワークモードの設定"
src="http://androg.up.seesaa.net/image/NetworkMode-thumbnail2.png" width="192" height="320" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/NetworkMode.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
というのがあったので、GSMの意味もわからず、「これが、Wifiと3Gの切り替えなんだな」と（勝手に）思って、[GSMのみ]に設定しておいた。
  
1日ぐらいたって、docomoメールから届くはずのメールが届かない！
  
そこで、[GSMのみ]にしていた設定を、[GSM/3G（自動モード）]に設定しなおしたところ、メールが受信された。
  
Docomoショップの店員さんに聞いたこと</p> 

* * *●Docomoショップの店員さんに最初の疑問について聞いてみると、「GSM対応エリアの場合この設定を使い、日本はGSM対応エリアではないため、GSMのみの設定にしているとdocomoメールは届きません」とのこと。（

[GSM &#8211; Wikipedia][1]）
  
●また、「常時Wifi接続したいんだけど、どうしたらいい？」については、
  
2箇所設定する必要があるようです。
  
1つは、[データ通信]のチェックを外すこと。
  
もう1つは、[mopera U設定]にすることです。
  
**常時Wifi接続方法**
  
ホーム画面でメニューボタンを押し、[設定]をタップする。
  
<img  alt="ホーム画面でメニューボタンを押し、設定をタップする"
src="http://androg.up.seesaa.net/image/pressMenuButtunOnHome-thumbnail2.png" width="192" height="320" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/pressMenuButtunOnHome.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
設定画面で、[無線とネットワーク]をタップする
  
<img alt="設定画面で、無線とネットワークをタップする"。 src="http://androg.up.seesaa.net/image/SettingImage-thumbnail2.png" width="192" height="320" border="0" align="" pbsrc="http://androg.up.seesaa.net/image/SettingImage.png" class="PopBoxImageSmall" onclick="Pop(this,100,'PopBoxImageLarge');" />
  
無線とネットワーク画面で、[モバイルネットワーク]をタップする。
  
<img  alt="無線とネットワーク画面で、モバイルネットワークをタップする"
src="http://androg.up.seesaa.net/image/WirelessAndNetworkImage-thumbnail2.png" width="192" height="320" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/WirelessAndNetworkImage.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
モバイルネットワーク画面で、[データ通信]のチェックを外す。そして[アクセスポイント名]をタップする。
  
<img  alt="モバイルネットワーク画面で、データ通信のチェックを外す。そしてアクセスポイント名をタップする"
src="http://androg.up.seesaa.net/image/MobileNetworkSettings-thumbnail2.png" width="192" height="320" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/MobileNetworkSettings.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
APN画面で[mopera U設定]にチェックする
  
<img  alt="APN画面でmopera U設定にチェックする"
src="http://androg.up.seesaa.net/image/AccessPointNames-thumbnail2.png" width="192" height="320" border="0" align=""
pbsrc="http://androg.up.seesaa.net/image/AccessPointNames.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
●「常時Wifi接続した状態でdocomoメールは受信できる？」については、
  
できない。送受信するには、どうしてもパケット通信料がかかってしまうとのことです。
  
結論？</p> 

* * *つまり、Wifiルータを持っている人なんかはできるだけ費用を安く済ませたいと思っているかもしれませんが、spモードを契約してdocomoメールを使いたい人は、パケホーダイダブルの上限までいっちゃう可能性がありますね。


  
対策としては、docomoメールやめて、Gmailのみに変更するぐらいしか思いつきません。docomoメールも使いたい場合、安く済ませるいい方法ないですかね？</p>

 [1]: http://ja.wikipedia.org/wiki/GSM