---
title: '#golang が簡単にインストールできるgoinstallを作りました。'
author: kwmt
layout: post
date: 2013-03-31
url: /index.php/2013/03/31/golang_goinstall/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - golang
  - Go言語

---
## はじめに

最近AWSを触ってて、インスタンス立ち上げるごとに、”Go言語環境が入っていない！&#8221; &#8220;gccも入ってない！&#8221; &#8220;Mercurialも入ってない&#8221;って思うんです。
  
それで毎回手動でコツコツいれるんですが、インスタンス立ち上げた瞬間は、Goだけならまだしもgccすら入っていない状態なので、いくら慣れたとはいっても、めんどくさくなってきました。 

## そこで

コマンド一発でそれらが全てインストールできたらいいのになぁと思ったので、[コマンド一発で（ソースから）インストールできるスクリプト][1]書きました。 

まぁAWSのAMIという機能でGo言語環境が入ったイメージを作っておくのもひとつの手かもしれませんが、AWS以外のLinux環境にも簡単にインストールできるようにしたかったので、スクリプトにしました。 

基本Linuxの対応だけでいいかなぁと思ったのですが、Macユーザーにも使ってもらえるように、Macも対応しました。ただし、下にも書きましたがMacな人はgccとMercurialがインストールされてる前提ですが・・・。 

## 対応環境

下記画像のLinuxとMacを確認しています。ただし、MacはgccとMercurialがインストールされていることが前提です。Linuxに関しては、もしgcc, Mercurialがインストールされていなかったら、インストールします。 

<img src="http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.57.54.png" alt="aws_launch_instance_ubuntu" width="846" height="136" class="alignnone size-full wp-image-521" srcset="http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.57.54-600x96.png 600w, http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.57.54-300x48.png 300w, http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.57.54-624x100.png 624w, http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.57.54.png 846w" sizes="(max-width: 846px) 100vw, 846px" />

<img src="http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.58.14.png" alt="aws_launch_instance_linux" width="837" height="104" class="alignnone size-full wp-image-522" srcset="http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.58.14-600x74.png 600w, http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.58.14-300x37.png 300w, http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.58.14-624x77.png 624w, http://kwmt27.net/images/2013/03/スクリーンショット-2013-03-29-23.58.14.png 837w" sizes="(max-width: 837px) 100vw, 837px" />

試してないですが、Cent OS,Ubuntu以外にもDebian,Fedoraなどもインストールできると思います。シェルは、bash,csh,tcshに対応しています。 

## インストール＆使い方

ダウンロードして、実行するだけ。 

gitがインストールされている場合
<pre class="go">$ git clone https://github.com/kwmt/goinstall
$ cd goinstall/
$ sudo ./goinstall.sh &lt;login_user&gt;
$ source ~/.bashrc (bashを使っている場合)
$ go version
go version gox.x.x (xは数字)
</pre>

gitがインストールされていない場合
<pre class="go">$ wget https://raw.github.com/kwmt/goinstall/master/goinstall.sh
$ wget https://raw.github.com/kwmt/goinstall/master/gosetting.sh
$ chmod +x goinstall.sh gosetting.sh 
$ sudo ./goinstall.sh &lt;login_user&gt;
$ source ~/.bashrc (bashを使っている場合)
$ go version
go version gox.x.x (xは数字)
</pre>

go versionとタイプしてみてバージョンが表示されたら、インストールおよびPath設定完了です。 

インストール先は、/usr/local/go です。 

AWSでインスタンスを立ち上げたときは、gitは入っていないと思うので、&#8221;gitがインストールされていない場合&#8221;のようにするか、gitをインストールしてもいいですね。どちらがよいか分かりませんが、少なくともgoinstallを実行する前に2手間ぐらいあるのは面倒ですので、何とかしたいですが、そこは仕方ないかもしれません。 

公式ページ[Installing Go from source][2]ではGoをソースからインストールすると、ビルドする手順となっているのですが、そのビルドに少し時間がかかります。 

AWSのAmazon LinuxとUbuntuで測ったところ、
  
Amazon Linux:37分
  
Ubutu:20分
  
でした。ご参考まで。 

## 最後に

今後はいずれMacにgccが入ってない状態から、インストールできるようにしたいです。あと、Windows対応も・・・。
  
これでgopher増えること間違いなし？！
  
フィードバックをお待ちしております。 

## 参考

[Installing Go from source][2]

 [1]: https://github.com/kwmt/goinstall
 [2]: http://golang.org/doc/install/source "Installing Go from source"