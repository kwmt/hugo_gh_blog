---
title: Google App EngineのGo言語(GAE/Go)のチュートリアルをやったメモ。
author: kwmt
layout: post
date: 2012-04-27
url: /index.php/2012/04/27/google-app-engineのgo言語gaegoのチュートリアルをやったメモ。/
pdrp_attributionLocation:
  - end
categories:
  - golang

---
## はじめに {.section}

2012/04/15に東海GTUG主催の
  
[「[愛知/名古屋]作ればわかる！ Google App Engineハンズオン #1（東海GTUG）」][1]
  
に参加しました。 

この本は一度写経したことがあって、せっかく著者さんがいらっしゃるので、もう一度やるのもありだったのですが、
  
GAE/Goチームができてたので、そちらに惹かれ
  
[GAE/Goのチュートリアル][2]
  
をすることにしました。 

さて、チュートリアルは
  
<a target="_blank" href="https://developers.google.com/appengine/docs/go/gettingstarted/introduction">イントロダクション</a>
  
があって、
  
次にGAE/Goの
  
<a target="_blank" href="https://developers.google.com/appengine/docs/go/gettingstarted/devenvironment">環境構築</a>です。
  
最終的にはデプロイまでのチュートリアルがあります。実際にチュートリアルを最後までやって、忘れないためにメモと気づいた事などを書いていきます。 

<!--more-->

<a target="_blank" href="https://developers.google.com/appengine/docs/go/gettingstarted/devenvironment">環境構築</a>
  
の最初の方に「Python 2.5をインストールしてください」とありますが、2.7でも動きました。Pythonのバージョンを確認するには、ターミナルで次のコマンドで確認できます。

<pre class="terminal">% python -V
Python 2.7.1
</pre></p> 

## Google App Engine SDK for Goをダウンロード {.section}

下記のURLからSDKをダウンロードします。
  
<https://developers.google.com/appengine/downloads> 


<img
src="http://androg.up.seesaa.net/image/GAE_SDK_for_Go-thumbnail2.png"
width="320" height="106"
border="0" align="" alt="GAE SDK for Go"
pbsrc="http://androg.up.seesaa.net/image/GAE_SDK_for_Go.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" /> 

僕はMacなので、「go\_appengine\_sdk\_darwin\_amd64-1-6-4.zip」をダウンロードしました。
  
解凍すると、`google_appengine`というディレクトリができるので、そのディレクトリを適当なディレクトリに移動します。
  
僕は、ディレクトリ名を変更して、次の場所に置きました。 

<pre class="go">/gae/appengine-go-sdk/
</pre>

チュートリアルにもTo simplify devlopment and deployと書かれていますが、
  
環境変数の`PATH`を以下のように追加すると、コマンドをタイプする時面倒ではなくなります。 

bash系だと、

<pre class="go">export PATH=/path/to/google_appengine:$PATH
</pre>

csh系だと、

<pre class="go">setenv PATH /path/to/google_appengine:$PATH
</pre>

## 短いメッセージを表示するだけのアプリケーションを作ります {.section}

`myapp`という名前のディレクトリを作成して、`myapp`の下に`hello`というディレクトリを作成します。
  
`hello`ディレクトリの下に`hello`パッケージGoソースファイルを作成していきます。
  
さっそく、`hello.go`を作成しましょう。 

<pre class="brush: golang; title: ; notranslate" title="">package hello
import (
    "fmt"
    "net/http"
)
func init(){
    http.HandleFunc("/", handler)
}
func handler(w http.ResponseWriter, r *http.Request){
    fmt.Fprint(w, "Hello, world!")
}
</pre>

次に設定ファイル`app.yaml`を`myapp`の下に作成します。 

<pre class="go">applicaiton:helloworld
version:1
rutime:go
api_version:go1
handlers:
- url:/.*
script:_go_app
</pre>

結果、次のような構成になっていればOK。 

<pre class="go">myapp/
    app.yaml
    hello/
        hello.go
</pre>

間違って、`myapp`ディレクトリの下に`hello.go`がある場合、 

<pre class="go">Compile error:
2012/04/15 13:47:48 go-app-builder: Failed parsing input: go files must be in a subdirectory of the app root
</pre>

のようなエラーが出ます。`hello`ディレクトリの配下に`hello.go`を移動させて解決。 

## アプリケーションの実行 {.section}

次のコマンドで、作成したアプリケーションの実行ができます。 

<pre class="terminal">$ ls
myapp
$ dev_appserver.py mypp/
~
INFO     2012-04-24 22:44:48,883 dev_appserver_multiprocess.py:647] Running application dev~helloworld on port 8080: http://localhost:8080
INFO     2012-04-24 22:44:48,883 dev_appserver_multiprocess.py:649] Admin console is available at: http://localhost:8080/_ah/admin
</pre>

Running application&#8230;とでたらサーバーが起動。`http://localhost:8080`をブラウザに入力すると、
  
`Hello, world!`と出ているはず。 

サーバーを止めるには、Control-C 

たとえば、サーバーを動かしている状態で`hello.go`を編集した場合、サーバーを一旦止める必要はありません。
  
`http://localhost:8080`のリロードでOKです。 

## データベースのデータを削除するには {.section}

[
  
データベースを使ったチュートリアル][3]があるのですが、データベースに登録の仕方
  
・使用の仕方が書いており、最後「Clearing the Development Server Datastore」でデータベースに登録したデータを消去するには、
  
次のコマンドで消すことが可能です。 

<pre class="terminal">$ dev_appserver.py --clear_datastore myapp/
</pre>

しかし、これで消えない時があります。それは、サーバー起動中です。データベースのデータを消去するには、
  
サーバーをControl-Cでシャットダウンしてください。 

## デプロイするには {.section}

Goアプリケーションをデプロイするには、`app.yaml`の`application`を、
  
登録したアプリケーションIDに変更して、次のコマンドを実行します。 

<pre class="terminal">$ appcfg.py update myapp/
</pre>

GAE/Jで作っていたアプリケーションIDを利用して、デプロイコマンドを打つと、 

<pre class="go">The 'go' runtime is only supported for apps using the High Replication Datastore.
</pre>

と出て、デプロイできません。`Storage Scheme`が`High Replication`ではなく、
  
`Master/Slave`であったことが原因でした。あたらしくアプリケーションIDを作り直し、このとき
  
Datastoreは`High Replication`であることを確認して、上記のデプロイコマンドを打って、無事デプロイできました。 


<img
src="http://androg.up.seesaa.net/image/Storage_Scheme-thumbnail2.png"
width="172" height="107"
border="0" align="" alt="Storage Scheme"
pbsrc="http://androg.up.seesaa.net/image/Storage_Scheme.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" /> 

## emacsのシンタックスハイライトを設定する {.section}

Goのソースの`misc`(GOROOT/misc)ディレクトリにemacsや、vimなどの
  
シンタックスハイライトを設定できる設定ファイルが入っています。
  
emacsのシンタックスハイライト設定をしたいたため、`misc/emacs`配下にある
  
`go-mode-load.el`と`go-mode.el`の2つを`~/.emacs.d/`にコピーし
  
`~/.emacs`に次のように記述します。 

<pre class="go">;Go言語シンタックスハイライトファイルの読み込み
(add-to-list 'load-path "~/.emacs.d/" t)
(require 'go-mode-load)
</pre>

あとは、`source ~/.emamcs`としてgoファイルを開けばシンタックスハイライトされます。 

<img
src="http://androg.up.seesaa.net/image/hellogo-thumbnail2.png"
width="292" height="320"
border="0" align="" alt="goのシンタックスハイライト"
pbsrc="http://androg.up.seesaa.net/image/hellogo.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />

 [1]: http://www.zusaar.com/event/234051
 [2]: https://developers.google.com/appengine/docs/go/gettingstarted/
 [3]: https://developers.google.com/appengine/docs/go/gettingstarted/usingdatastore