+++
title= "Go Modulesで簡単バージョン管理"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Go"]
tags = ["Go"]
keywords = [""]
+++
# はじめに
go1.11から[Go Modules](https://github.com/golang/go/wiki/Modules)というのがサポートされていたので、簡単に使い方をご紹介します。

これは簡単に言うとバージョン管理ツールです。
Go 1.11時点ではexperimentalで、最終的にGo1.13で取り込まれるようです。


```go:hello.go
package main

import (
    "fmt"
    "rsc.io/quote"
)

func main() {
    fmt.Println(quote.Hello())
}
```

という"rsc.io/quote"に依存したプログラムがあるとき、go buildとすると自動的にgo getしてくれるというものです。

# 使い方

例として、GOPATHに次のプロジェクトを作成した前提で、使い方を見ていきます。

```sh
$ mkdir -p $GOPATH/github.com/kwmt/gomod-sample
$ cd $GOPATH/github.com/kwmt/gomod-sample
```

まず、go modコマンドというのがあるのですが、そのコマンドを使うためには、`GO111MODULE`という環境変数を`on`に設定する必要があります。

```sh
export GO111MODULE=on 
```

次に`go mod init`で初期化します。

```sh
% go mod init github.com/kwmt/gomod-sample
go: creating new go.mod: module github.com/kwmt/gomod-sample
```

すると、go.modというファイルが作成されます。

```sh
% ls
go.mod
```

ちなみに、go.modファイルの中身は下記のようになっています。

```go.mod
module github.com/kwmt/gomod-sample
```

[Quick Start](https://github.com/golang/go/wiki/Modules#quick-start)にあるように、下記のような["rsc.io/quote"](https://github.com/rsc/quote)というライブラリに依存したプログラムhello.goを作成します。

```sh
% cat <<EOF > hello.go
package main

import (
    "fmt"
    "rsc.io/quote"
)

func main() {
    fmt.Println(quote.Hello())
}
EOF
```

```sh
% ls
hello.go
```

そしてgo buildします。

```sh
% go build
go: finding rsc.io/quote v1.5.2
go: finding rsc.io/sampler v1.3.0
go: finding golang.org/x/text v0.0.0-20170915032832-14c0d48ead0c
```

すると、依存ライブラリをダウンロードしてくてるようなログがでてますね。

終わったらgoプログラムのバイナリと、go.sumというファイルができています。



```sh
% ls
go.mod        go.sum        gomod-sample* hello.go
```

実行してみましょう。


```sh
% ./gomod-sample
こんにちは世界。
```

おぉ、なんといういことでしょう、go getも何もしていないのにgo buildだけで依存解決してくれました。

go.modファイルをもう一度みてみると、`require rsc.io/quote v1.5.2`という一文が追加されています。

```go.mod
module github.com/kwmt/gomod-sample

require rsc.io/quote v1.5.2
```

バージョン管理は`require <ライブラリ> <バージョン>`とすることでできそうです。

先程はgoプログラムからgo.modに依存ライブラリが書かてた流れになりましたが、逆にgo.modにライブラリ([go-print](https://github.com/kwmt/go-print))を書いてgoプログラムで使えるか見てます。


go.modファイルを次のように編集します。

```go.mod
module github.com/kwmt/gomod-sample

require (
    rsc.io/quote v1.5.2
    github.com/kwmt/go-print master
)
```

go build してみると、追加した依存ライブラリをダウロードしてくれてるようです。

```sh
% go build
go: finding github.com/kwmt/go-print master
```


これを使ってみましょう。



```go:hello.go
index d2c61a0..00167b6 100755
Binary files a/gomod-sample and b/gomod-sample differ
diff --git a/hello.go b/hello.go
index 7475f42..4439d38 100644
--- a/hello.go
+++ b/hello.go
@@ -3,8 +3,12 @@ package main
 import (
     "fmt"
     "rsc.io/quote"
+
+    p "github.com/kwmt/go-print"
 )
 
 func main() {
     fmt.Println(quote.Hello())
+
+    fmt.Println(p.Print("そうだ京都へ行こう！"))
 }
```

このように変更してみました。

実行してみましょう。

```sh
% go run hello.go
こんにちは世界。
そうだ京都へ行こう！
```

使えてますね。


go.modファイルをもう一度見てみると、masterと書いていたところが、v0.0.0-20170429154625-3ca7e5ea0b96のように変わっています。形式は`v0.0.0-<更新日時>-<コミットハッシュ>`ですかね。


```go.mod
github.com/kwmt/go-print v0.0.0-20170429154625-3ca7e5ea0b96
```


バージョンを指定しない状態でgo buildすると`usage: require module/path v1.2.3`と怒られるので何かしら指定しないといけないのですが、tagがプッシュされてないリポジトリとかは`master`でも良さそうです。


# まとめ

- go modコマンドを使うには、現時点では環境変数`GO111MODULE`を`on`に設定する。
- go modを使い始めるときは、go mod initで初期化して、go.modファイルを作成する。
- goプログラム側に依存ライブラリを書くか、go.modファイルに依存ライブラリを書いて go buildするだけ。

この記事を書くにあたって試した全体のソースはこちらです。
https://github.com/kwmt/gomod-sample


# おわりに

もともと[Google App Engine(go111)](https://cloud.google.com/appengine/docs/standard/go111/)に[govendor](https://github.com/kardianos/govendor)を使って複数のライブラリに依存したプログラムをデプロイしようとして、デプロイできなくていろいろ調べていたら、たまたまこの[stackoverflow](https://stackoverflow.com/a/53508257)を見てGo Moduleを知りました。
GAEでvendorを使うのはgopathの設定をいじったりしてめんどくさい感じだったのですが、これだとgo buildするだけなのでGAEでも使いやすいかなと思います。
実際[Firebase Admin Go SDK](https://github.com/firebase/firebase-admin-go)を使ってとくにgopathの設定をいじらなくてもデプロイできました。

参考までにFirestoreを使うために次のimportが必要なとき、

```go
import(
	"cloud.google.com/go/firestore"
	firebase "firebase.google.com/go"
	"golang.org/x/net/context"
	"google.golang.org/api/option"
)
```

go build後に更新されたgo.modは次のようになりました。

```go.mod
module <プロジェクト>

require (
	cloud.google.com/go v0.26.0
	firebase.google.com/go v3.5.0+incompatible
	github.com/googleapis/gax-go v2.0.2+incompatible // indirect
	github.com/googleapis/gax-go/v2 v2.0.3 // indirect
	go.opencensus.io v0.18.0 // indirect
	golang.org/x/net v0.0.0-20181220203305-927f97764cc3
	golang.org/x/oauth2 v0.0.0-20181203162652-d668ce993890 // indirect
	google.golang.org/api v0.0.0-20181229000844-f26a60c56f14
	google.golang.org/genproto v0.0.0-20181221175505-bd9b4fb69e2f // indirect
	google.golang.org/grpc v1.17.0 // indirect
)
```

# 参考
- https://github.com/golang/go/wiki/Modules

