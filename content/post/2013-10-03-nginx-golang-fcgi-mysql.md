---
title: '#Nginx + #golang (FastCGI)+ #MySQL を使ったWebアプリケーションを書くための準備'
author: kwmt
layout: post
date: 2013-10-02
url: /index.php/2013/10/03/nginx-golang-fcgi-mysql/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - 1.2 rc1
  - golang
  - Go言語
  - MySQL
  - net/http/fcgi
  - Nginx

---
## はじめに {.section}

下記の記事をみたら分かりやすいかもしれませんが、英語なので、実際にやった方法も交えてメモしておこうと思います。（似てるところもありますが、翻訳ではありません。）
  
<a href="http://mwholt.blogspot.jp/2013/05/writing-go-golang-web-app-with-nginx.html" target="_blank">Writing a Go (&#8220;golang&#8221;) Web App with nginx, FastCGI, MySQL, JSON</a>

前提として、Go言語でWebサーバーをつくって動かすと言うと２種類の方法があります。
  
１つは、Go言語の<a href="http://golang.org/pkg/net/http/" target="_blank">&#8220;net/http&#8221;</a>パッケージというのがありますので、これを使ってGo自体でWebサーバーを作ってしまうことができます。
  
もう1つは、ApacheやNginxなどのWebサーバー上で、CGI(正確にはFastCGI)としてGo言語を動かす方法です。これはNginx+PHP-FPMのようなもに近いイメージだと思います。

Nginxを使うか使わないかは、こちらの<a href="http://stackoverflow.com/questions/17776584/webserver-for-go-golang-webservices-using-nginx-or-not" title="Webserver for Go (golang) webservices: using NGINX or not?" target="_blank">Stackoverflow</a>をみると、

> Go HTTPサーバーはいいんだけど、Webサーバーを一から作るのは大変だよ、Nginxを使った方が簡単だよ

みたいな感じで、Nginxつかった方がいいよという結論になってます。

この記事では、2つ目の方法のNginx+golang(FastCGI)でWebアプリケーションを作る準備をメモしたいと思います。
  
データベースとしては、MySQLを使いたいと思います。

## 使用環境 {.section}

<pre class="go">サーバOS:CentOS release 6.4
Webサーバ:nginx-1.4.1
データベース:mysql  Ver 14.14 Distrib 5.5.27
</pre>

<!--more-->

## 1.Nginxをインストールする {.seciton}

まずは、CentOSにNginxをソースからインストールしたいと思います。(<a href="http://wiki.nginx.org/Install" target="_blank">Nginx installリンク</a>)
  
なぜソースからインストールするのかは、Web+DBにおすすめと書かれていたから(^o^;
  
ちゃんというとモジュール追加するには、make時にくっつけとかないといけないからで、必要なければ、rpmとかからインストールしてもいいと思います。

<pre class="go"># wget http://nginx.org/download/nginx-1.4.1.tar.gz
# tar zxvf nginx-1.4.1.tar.gz
# ls
nginx-1.4.1
# cd nginx-1.4.1
# ./configure
</pre>

雑誌Web+DB pressのVol12.2013には下記のように指定してましたが、個人的にはデフォルトの方が分かりやすいため、特にオプション指定はしていません。

<pre class="go"># ./configure --prefix=/usr/local --conf-path=/etc/nginx/nginx.conf 
</pre>

デフォルトでは、Nginxは/usr/local/nginxにインストールされます。
  
nginxのサービスを起動したりするコマンドは、/usr/local/nginx/sbinの中にあるnginxコマンドなので、PATHを設定しておくと楽になるかと思います。

<pre class="go">bashの場合 .bashrcに下記を記入
export PATH=/usr/local/nginx/sbin:$PATH
</pre>

さて、`./configure`の時点に少し戻ります。私の環境では、`./configure`時に下記の2つのエラーが出ましたので、それらについてのエラー内容と対策も書いておきたいと思います。

<pre class="go">【./configure時のエラー１】
./configure: error: the HTTP rewrite module requires the PCRE library.
【対策】
# yum -y install pcre pcre-devel

【./configure時のエラー２】
./configure: error: the HTTP gzip module requires the zlib library.
【対策】
# yum -y install zlib-devel

※参考
<a href="http://bit.ly/19V1ahY" target="_blank">64bit版CentOS 5.3にnginxをインストールしてみた - Umeyashikiの日記</a> 
</pre>

これで`./configure`が正常に終了したと思いますので、下記のようにしてインストールしましょう。

<pre class="go"># make
# make install
</pre>

以上で、Nginxのインストールは終了です。

次に、Nginxの設定に行きたいところですが、インストールを終わらせてしまいましょう。

## 2.Go Toolsをインストール {.section}

基本的には本家サイトの<a href="http://golang.org/doc/install" target="_blank">Getting Started</a>をみてください。

簡単に言うと、こちらから環境にあったものをインストールするか、
  
<a href="https://golang.org/dl/" target="_blank">https://golang.org/dl/</a>

ソースからインストールする場合には、拙作ですが、gitが入っていれば数回のコマンド入力でgoがインストールできます。「Mercurialが入ってなければ、Mercurialをインストールする」というような工程はもう必要ありません。
  
<a href="https://github.com/kwmt/goinstall" title="goinstall" target="_blank">https://github.com/kwmt/goinstall</a>
  
こちらもお勧めです。(Linux,Macのみ対応しています。)

## 3.MySQLをインストールする {.section}

ググりましょう。

## 4.Nginxの設定をする {.section}

FastCGIを使うには、locationディレクティブに次のような感じで設定するようです。
  
この例だと、ポート8080にアクセスがきたら、ポート9000にアクセスするようになります。（作るアプリによっては実用的ではないかもしれません）

<pre class="go">server {
    listen       8080;
    server_name  localhost;

    location / {
        fastcgi_pass 127.0.0.1:9000;
        include fastcgi.conf;
    }
}
</pre>

Basic認証を掛ける方法は、<a href="http://bit.ly/17eWMgm" target="_blank">【Nginx】Basic認証をかける</a>を参考にさせて頂きました。

実際の設定としては次のようになります。

<pre class="go">location /admin {
            auth_basic "members onry";
            auth_basic_user_file "/etc/nginx/.htpasswd";
            fastcgi_pass 127.0.0.1:9000;
            include fastcgi.conf;
        }
</pre>

設定が終わったので、Nginxを起動させましょう。起動するには、

<pre class="go"># nginx
</pre>

です。

再起動と停止は次のようにします。

<pre class="go"># nginx -s reload //再起動
# nignx -s stop   //停止
</pre>

## 5.Go言語プログラムをFastCGIとして動かすためには {.section}

何も出力しませんが、localhost:9000で接続要求を待ってくれます。この&#8221;9000&#8243;はNginx側の設定の9000と合わせる必要があります。

<pre class="go">package main
import (
	"net/http"
	"net/http/fcgi"
)
func main(){
	l,_:= net.Listen("tcp",":9000")
	mux := http.NewServeMux()
	fcgi.Serve(l,mux)
}
</pre>

ちなみに、下記は<a href="http://golang.org/pkg/net/http/fcgi/#Serve" target="_blank">&#8220;net/http/fcgi&#8221;</a>パッケージのServe関数の翻訳です。Serve関数で何をやってるのかが分かるかと思います。

> Serve関数はリスナー`l`(エル)上で、FastCGI接続を待ち受け、それぞれに対して新しいゴルーチンを作ります。
  
> そのゴルーチンはリクエストを解釈し、リクエストに応答する`handler`をコールします。`l`(エル)が`nil`の場合、`Serve`関数は、`os.Stdin`からの接続を受け入れます。`handler`が`nil`の場合、`http.DefaultServeMux`が使われます。 

## 6.Go言語からMySQLを使うには {.section}

ドライバを下記からインストールする必要があります。
  
<a href="http://golang.org/s/sqldrivers" target="_blank">http://golang.org/s/sqldrivers</a>

上のリストをみると、MySQLのドライバは2つあるのですが、たとえば、go-sql-driver/mysqlをインストールするには次のようにします。

<pre class="go">$ go get github.com/go-sql-driver/mysql
</pre>

使い方については、下記が参考になると思います。

  * <a href="https://github.com/go-sql-driver/mysql/wiki/Examples" target="_blank">wikiにあるExamples</a>
  * <a href="http://qiita.com/tenntenn/items/dddb13c15643454a7c3b" target="_blank">[Go言語] database/sqlパッケージを使ってみた</a>

もう1つのドライバmymysqlについては、過去記事をご参照ください。
  
<a href="http://kwmt27.net/index.php/2012/09/06/golang%e3%81%8b%e3%82%89mysql%e3%82%92%e4%bd%bf%e3%81%86%e3%81%ab%e3%81%af/" title="golangからMySQLを使うには" target="_blank">golangからMySQLを使うには</a>

## 補足：mysqlから取得した日本語が文字化けする件 {.section}

※Mac OSのみかもしれない.
  
【原因】
  
Server側とClient側の文字コードが異なっていたため。

<pre class="go">Server characterset:	latin1
Db     characterset:	latin1
Client characterset:	utf8
Conn.  characterset:	utf8
</pre>

【対策】
  
macでは

<pre class="go">/etc/my.conf
</pre>

<pre class="go">[client]
default-character-set=utf8
...
[mysqld]
character-set-server=utf8 #ここだけ 違うので注意
...
[mysqldump]
...
default-character-set=utf8	
[mysql]
...
default-character-set=utf8
</pre>

あとはmysqlを再起動する
  
（macの場合はシステム環境設定->MySQL->Stop MySQL Server ->Start MySQL Server）
  
※参考
  
[fedora15 mysql5.5 default-character-setが原因で起動できない][1]

## 補足：Go言語でクロスコンパイルするには {.section}

まだ試したことないですが、とりあえずメモがてら。ためして何か分かったら更新するかもです。
  
<a href="http://bit.ly/1g3egQA" target="_blank">Go言語でクロスコンパイルする &#8211; memoメモ</a>

## おわりに {.section}

これでひとまずNginx+golang(fcgi)+MySQLの環境が整ったとおもいますので、あとは
  
<a href="http://golang.org/doc/articles/wiki/" target="_blank">Writing Web Applications</a>(<a href="https://github.com/kwmt/golangwiki/blob/master/doc/articles/wiki.md" target="_blank">日本語訳</a>)(スライド：<a href="http://www.slideshare.net/yasi_life/goweb-16448500" target="_blank">Go言語によるwebアプリの作り方</a>)
  
のような記事を読むと良いと思います。

## 参考リンク {.section}

  * <a href="http://mwholt.blogspot.jp/2013/05/writing-go-golang-web-app-with-nginx.html" target="_blank">Writing a Go (&#8220;golang&#8221;) Web App with nginx, FastCGI, MySQL, JSON</a>
  * <a href="http://stackoverflow.com/questions/17776584/webserver-for-go-golang-webservices-using-nginx-or-not" title="Webserver for Go (golang) webservices: using NGINX or not?" target="_blank">Stackoverflow:Webserver for Go (golang) webservices: using NGINX or not?</a>
  * <a href="http://golang.org/pkg/net/http/" target="_blank">&#8220;net/http&#8221;</a>パッケージ
  * <a href="http://golang.org/pkg/net/http/fcgi/#Serve" target="_blank">&#8220;net/http/fcgi&#8221;</a>パッケージ
  * <a href="http://wiki.nginx.org/Install" target="_blank">Nginx installリンク</a>
  * <a href="http://bit.ly/19V1ahY" target="_blank">64bit版CentOS 5.3にnginxをインストールしてみた &#8211; Umeyashikiの日記</a> 
  * <a href="http://bit.ly/17eWMgm" target="_blank">【Nginx】Basic認証をかける</a>
  * <a href="http://golang.org/doc/install" target="_blank">Getting Started</a>
  * <a href="https://golang.org/dl/" target="_blank">https://golang.org/dl/</a>
  * <a href="https://github.com/kwmt/goinstall" title="goinstall" target="_blank">goinstall</a>
  * <a href="http://golang.org/s/sqldrivers" target="_blank">SQLDrivers &#8211; go-wiki &#8211; SQL database drivers &#8211; Go Language Community Wiki &#8211; Google Project Hosting</a>
  * <a href="https://github.com/go-sql-driver/mysql/wiki/Examples" target="_blank">wikiにあるExamples</a>
  * <a href="http://qiita.com/tenntenn/items/dddb13c15643454a7c3b" target="_blank">[Go言語] database/sqlパッケージを使ってみた</a>
  * <a href="http://kwmt27.net/index.php/2012/09/06/golang%e3%81%8b%e3%82%89mysql%e3%82%92%e4%bd%bf%e3%81%86%e3%81%ab%e3%81%af/" title="golangからMySQLを使うには" target="_blank">golangからMySQLを使うには</a>
  * [fedora15 mysql5.5 default-character-setが原因で起動できない][1]
  * <a href="http://bit.ly/1g3egQA" target="_blank">Go言語でクロスコンパイルする &#8211; memoメモ</a>

 [1]: http://d.hatena.ne.jp/nightmare_tim/20110530/1306704112