---
title: あなたがGOROOTを本当に設定しなくていい理由
author: kwmt
layout: post
date: 2013-06-14
url: /index.php/2013/06/14/you-dont-need-to-set-goroot-really/
pdrp_attributionLocation:
  - end
fb_author_post_id:
  - 539439249452720
categories:
  - golang
tags:
  - golang
  - Go言語

---
<font color="red">この記事は、Dave Cheneyさんの記事<a href="http://dave.cheney.net/2013/06/14/you-dont-need-to-set-goroot-really">You don’t need to set GOROOT, really</a>の非公式翻訳です。詳細は上記記事をご参照下さい。</p> 

<p>
  This post is the Japanese translated post of <a href="http://dave.cheney.net/2013/06/14/you-dont-need-to-set-goroot-really">Dave Cheney&#8217;s post</a>.<br /> </font>
</p>

<h2 class="section">
  はじめに
</h2>

<p>
  これは、Goをコンパイルしたりするとき、なぜ $GOROOTを設定しておく必要がないかを説明するという短いポストになります。
</p>

<h2 class="section">
  TL;DR
</h2>

<p>
  Go1.0とそれ以降のバージョンでコンパイルして使うとき、一般には[1]$GOROOT環境変数を設定する必要はありません。
</p>

<p>
  実際には、使うコンピュータにGoの複数のバージョンがあるなら、$GOROOTを設定すると、プログラムをデバッグしにくくなります。
</p>

<p>
  $GOPATHを設定する必要があります。Go1.0以降強く推奨されていまして、Go1.1リリースでは、必須となりました。
</p>

<h2 class="section">
  なぜGOROOTは、もはや必要とされないのでしょうか？
</h2>

<p>
  まだこの記事読んでますか？すばらしい。さて、いくつかの経緯がありました。
</p>

<h3>
  Goの環境変数について
</h3>

<p>
  Goを昔からやっていた人は、$GOROOTだけなく、$GOOSと$GOARCHの環境変数が必要だった時代を覚えているかもしれません。これらはMakefileがビルドシステムの基盤となっていたため必要でした。たくさんのインクルードが使われており、そのベースパスとして$GOROOTが使われていました。
</p>

<p>
  go toolが導入されたことによって、Go 1.0以降、$GOOSと$GOARCHはオプションとなりました。ビルドスクリプトがホストのOSやCPUアーキテクチャを検出できるようにしました。Go1.0のリリースで、cmd/distのビルドツールの導入で<br /> $GOOS,$GOARCHが真のオプションになりました。これらは今は<a href="http://dave.cheney.net/2012/09/08/an-introduction-to-cross-compilation-with-go">クロスコンパイル</a>するときだけ使われます。
</p>

<p>
  Go1.0は、ワークスペースをベースとなった$GOPATHを導入しました。ここまで読んできた方は、$GOPATH ワークスペースが何であるかをおそらくわかっていると思います。しかし、分からない場合は、<a href="http://golang.org/doc/code.html">golang.org</a>のドキュメントか<a href="http://www.youtube.com/watch?v=XCsL89YtqCs">こちらの動画</a>を参照して下さい。
</p>

<h3>
  $GOOSや$GOARCHが必要ないことは分かった。けど、$GOROOTについては？
</h3>

<p>
  $GOROOTはいつも、Goインストールしたルートへの参照として定義されてきました。ビルドシステムに基づいた古いMakefileでは、これはベースパスとして使われていました。Go1.0から($GOPATH/pkg/tool/$GOOS_$GOARCH/に保存された)コンパイラや($GOPATH/pkg/$GOOS_$GOARCH/にある)標準ライブラリを探すgo toolで使われています。もしあなたがjavaユーザーなら、$GOROOTは$JAVA_HOMEに似ています。
</p>

<p>
  Goをソースからコンパイルするとき、$GOROOTの値は自動的に発見され[2]、ソースツリーからgo toolの中に埋め込まれました。go envを実行するとこのことを確認することができます。
</p>

<pre class="go">
% echo $GOROOT

% go env GOROOT
/home/dfc/go
</pre>

<p>
  あなたがウェブサイト<a href="https://golang.org/dl/">https://golang.org/dl/</a>からダウンロードしたのがバイナリディストリビューションかあるいは、あなたのOSディストリビューションからインストールした場合、go toolのバイナリへ埋め込まれた正しい$GOROOTの設定値を持っています。
</p>

<pre class="go">
% dpkg -l golang-{go,src} | grep ^ii
ii golang-go 2:1-5 Go programming language compiler
ii golang-src 2:1-5 Go programming language compiler - source files
% which go
/usr/bin/go
% go env GOROOT
/usr/lib/go
</pre>

<p>
  go toolが /usr/bin/goにインストールされ、$GOROOTが /usr/lib/goとして埋め込まれているのが分かります。
</p>

<h3>
  それで、なんで $GOROOTは設定すべき出ないの？
</h3>

<p>
  $GOROOTはもはや設定すべきではありません。なぜなら、正しい設定値がすでに go toolに埋め込まれているからです。
</p>

<p>
  $GOROOTを設定することは、go toolに保存されている値を上書きすることになります。別のバージョンからコンパイラと標準ライブラリを参照するGoのバージョンからgo toolへと移行しています。
</p>

<p>
  あなたが$GOROOTの環境変数を設定しなければならいかもしれないところは、２つの場合のみです。両方のケースともにgolang.orgのウェブサイトの<a href="http://golang.org/doc/install#install">インストレーションページ</a>に記載されています。完全性のためにここでそれらをおさらいします。
</p>

<ul>
  <li>
    golang.org.のウェブサイトから、zipやtarballバイナリをダウンロードをしているLinux,FreeBSD,OS Xユーザーの場合、<br /> これらはのバイナリは /usr/local/go の$GOROOTの設定値を持っており、その場所に解凍することをおすすめします。<br /> もし、そこに解凍しなかたっら、あなたの選んだ場所に$GOROOTを設定しなければなりません。
  </li>
  <li>
    golan.grgのウェブサイトから、zipバイナリをダウンロードしたWindowsユーザーの場合、<br /> そのバイナリは C:\Goとして$GOROOTの設定値を持っています。もしあなたのシステム上の他の場所に置いたらなら、<br /> その選んだ場所に$GOROOTを設定しなければなりません。
  </li>
</ul>

<hr />

<ol>
  <li>
    Goのバイナリディストリビューションを使う場合、必要される２つのケースがあります。それはこのポストで説明します。
  </li>
  <li>
    これは、あなたが$GOROOTを設定していない場合です。all.bashの親ディレクトリが$GOROOTにマッチしないとき、ビルドシステムが検出しますが。
  </li>
  <ol>