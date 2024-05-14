+++
date = "2017-03-23T12:17:50+09:00"
title = "HerokuでGoアプリケーションを動かしたい"
categories = ["Go言語"]
tags = ["Heroku", "golang", "Go"]

+++


# はじめに

Goで作ったWebアプリケーションをHerokuにデプロイする手順を書いておこうと思います。最初はDockerを使わずにデプロイし、あとでDocker化してみようと思います。

# セットアップ

## Herokuのコマンドラインツールのインストール

Herokuのコマンドラインツールをインストールしましょう


[こちら](https://devcenter.heroku.com/articles/getting-started-with-go#set-up)から各環境のコマンドラインツールをインストールできます。

そしてツールをインストールしたら、

```
% heroku login
```

でログインしておきます。

## Heroku側にnew appを作成

HerokuにデプロイするためにHerokuに新規アプリを作成しましょう

```
% heroku apps:create heroku-with-go --buildpack heroku/go
```

`--buildpack`はHeroku上でビルドするのに必要ですのでGo用のbuildpackを設定しておきます。オープンソースですので、[こちら](https://github.com/heroku/heroku-buildpack-go)で見ることが出来ます。

新規アプリが作成されたかブラウザで確認してみます。

```
% heroku open --app heroku-with-go
```

{{< figure src="/images/2017/03/heroku-welcom.png" >}}



## Goアプリケーション作成してからHerokuデプロイまで

Goアプリケーションを準備するのですが、[Getting Started on Heroku with Go](https://devcenter.heroku.com/articles/getting-started-with-go#prepare-the-app)には、Githubからサンプルをクローンしてきて、デプロイしてみようみたな感じですが、ここではこのサンプルは使わず、1から自分で作ってみようと思います。

### プロジェクト（ディレクトリ）作成

`$GOPATH` にWebアプリケーションプロジェクトを作成しましょう。
今回は`heroku-with-go`ディレクトリを作成しました。

```
$ mkdir $GOPATH/src/github.com/kwmt/heroku-with-go
```

`heroku-with-go` にWebアプリを書いて行きましょう。

### main.go作成

とりあえず、簡単のためにURLのPathを表示するだけのアプリを書いてみます。(環境変数`PORT`を取得できるようにする必要があります)

```main.go
package main

import (
	"fmt"
	"log"
	"net/http"
	"os"
)

func main() {
	port := os.Getenv("PORT")
	if port == "" {
		log.Fatal("$PORT must be set")
	}

	http.HandleFunc("/", handler)
	http.ListenAndServe(":"+port, nil)
}

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello, %q", r.URL.Path[1:])
}
```

### Procfile作成

Herokuで動かすには、`Procfile`というファイルが必要ですので、それも作成しましょう。(`Procfile`については、[こちら](https://devcenter.heroku.com/articles/procfile)に詳細があります)

`Procfile`には、Heroku上で自分のアプリケーションを動かすためのコマンドを記述しますので、たとえば次のように記述します。

```Procfile
web: heroku-with-go
```

`heroku-with-go`というのは、最初に作成したディレクトリです。つまり、ここでは`$GOPATH/src/github.com/kwmt/heroku-with-go`の`heroku-with-go`の部分です。
下記でBuildpackというのを設定するのですが、そのなかで自動的にGoプログラムを`go build`して `go install` で`$GOPATH/bin`にインストールされる仕組みになっていますので、herokuプロジェクト名を書いて置くといいでしょう。ソースは[このあたり](https://github.com/heroku/heroku-buildpack-go/blob/ebcff600544c39e41d5066fc289eb20bd25e44e1/bin/compile#L388)を参照すると良いかもしれません。


### vendor.json作成

`vendor/vendor.json`ファイルを作成しましょう。これは依存ライブラリを使っていなくても必須のようです。簡単でいいので作成しておきます。

```vendor/vendor.json
{
	"rootPath": "github.com/kwmt/heroku-with-go"
}
```


簡単といっても`rootPath`だけは必要のようです。`{}`だけだと

```
-----> Go app detected
-----> Checking vendor/vendor.json file.
 !!    The 'rootPath' field is not specified in 'vendor/vendor.json'.
 !!    'rootPath' must be set to the root package name used by your repository.
 !!    Recent versions of govendor add this field automatically, please upgrade
 !!    and re-run 'govendor in
```

と怒られてしまいます。



また、`vendor/vendor.json`がないときにherokuにpushすると、次のようにdeployに失敗してしまいます。

```
% git push heroku master
Counting objects: 15, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (15/15), 1.64 KiB | 0 bytes/s, done.
Total 15 (delta 2), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote: 
remote: -----> Failed to detect app matching https://github.com/heroku/heroku-buildpack-go.git buildpack
remote:        More info: https://devcenter.heroku.com/articles/buildpacks#detection-failure
remote: 
remote:  !     Push failed
remote: Verifying deploy...
remote: 
remote: !	Push rejected to heroku-with-go.
remote: 
To https://git.heroku.com/heroku-with-go.git
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'https://git.heroku.com/heroku-with-go.git'
```

### Herokuにデプロイ

ここまでで最低限の準備ができましたので、Herokuにデプロイしてみます。下記のようにherokuのgitレポジトリにpushするだけです。

```
% git push heroku master
```

`heroku` は新規アプリ作ったとき(heroku createしたとき)に自動的に作成されています。`git remote`コマンドで確認できます。


```
% git remote -v
heroku	https://git.heroku.com/heroku-with-go.git (fetch)
heroku	https://git.heroku.com/heroku-with-go.git (push)
```

ちゃんとデプロイされたかブラウザで確認してみます。

```
% heroku open world
```

とすると

{{< figure src="/images/2017/03/heroku-with-go.png" >}}

と期待どうりになっていることがわかるかと思います。

## Dockerを使いたい

### 前提として

上記ディレクトリをコピーして名前`heroku-with-docker`と少し変更して作業を進めています

```
cp -r $GOPATH/src/github.com/kwmt/heroku-with-go $GOPATH/src/github.com/kwmt/heroku-with-docker
```

`heroku-container-tools`プラグインをインストールしておきます。（このプラグインはdeprecatedなので本当は使いたくないのですが。。）

```
% heroku plugins:install heroku-container-tools 
```

### app.json

下記のようなapp.jsonファイルを作成します。

```app.json
{
  "name": "Heroku with Docker",
  "description": "Sample",
  "image": "heroku/go:1.6",
  "mount_dir": "src/github.com/kwmt/heroku-with-docker",
  "website": "http://github.com/kwmt/heroku-with-docker",
  "repository": "http://github.com/kwmt/heroku-with-docker"
}
```


これは`heroku-container-tools`プラグインの

```
% heroku container:init
```

というコマンドを使って、次のような`Dockerfile`と`docker-compose.yml`ファイルを自動生成するためのものです。


```Dockerfile
FROM heroku/go:1.6
```

```docker-compose.yml
web:
  build: .
  command: 'bash -c ''heroku-with-docker'''
  working_dir: /app/user/src/github.com/kwmt/heroku-with-docker
  environment:
    PORT: 8080
  ports:
    - '8080:8080'
shell:
  build: .
  command: bash
  working_dir: /app/user/src/github.com/kwmt/heroku-with-docker
  environment:
    PORT: 8080
  ports:
    - '8080:8080'
  volumes:
    - '.:/app/user/src/github.com/kwmt/heroku-with-docker'
```


ぶっちゃけこれらのファイルの書き方が分かってしまえば、`app.json`は不要です。といいますか、`heroku-container-tools`はdeprecatedになっているので、今後`heroku container:init`する手段がなくなります。。。

そもそも`heroku container:init`は`heroku-container-tools`プラグインのコマンドなんですが、このプラグインはdeprecatedになってたり。
addonは[ここ](https://github.com/heroku/heroku-container-tools/blob/master/lib/addons.js)にあるものしかかけないし・・・


### デプロイ

```
% heroku container:release
```

以上でデプロイできて、動いた。わーい！

とはいかなくて、`Godeps/Godeps.json`ファイルを作らないと動きません。

```
% cat Godeps/Godeps.json
{
    "ImportPath": "github.com/kwmt/heroku-with-docker",
    "GoVersion": "go1.6",
    "Deps": []
}
```

Dockerイメージ[`heroku/go`がGodepsに依存](https://github.com/heroku/docker-go/blob/master/1.6/compile#L39)してしまってて。。

とはいえ、Godeps.jsonファイルを作れば下図のようにデプロイ出来ました。



{{< figure src="/images/2017/03/heroku-with-docker.png" >}}

{{< figure src="/images/2017/03/heroku-with-docker2.png" >}}




# まとめ（Heroku+Dockerが微妙なとこ）

* Dockerのベースイメージを自由にできない
* ベースイメージを自由にできないに関係するが、docker-composeのリンクは決まったものしか対応していない（redisとかpostgresは使えるがmysqlが使えないとか）
* vendoringツールはGodepsだけしか使えない（サンプルではgovendorに移行したという[コミットコメント](https://github.com/heroku/go-getting-started/commit/ef9db03770fe24d917c64fd1790e65587e04c095)があったのでできるのかと思ったけど、ベースイメージのソース見る限りできなさそう？やり方がありそうだが、[READMEにもGodepsが必要ってある](https://github.com/heroku/go-getting-started/blob/master/README.md#running-locally)し、頑張るところでもない気がするので諦める）
* おそらく上記を解決するのが`heroku-container-registry`プラグインな気がするけど、まだ解決できてなさそう。


# サンプルソース

* [heroku-with-go](https://github.com/kwmt/heroku-with-go)
* [heroku-with-docker](https://github.com/kwmt/heroku-with-docker)



# 参考
* [HerokuにDockerで動くアプリケーション（Go）をデプロイする](http://otiai10.hatenablog.com/entry/2016/01/21/134905)
* [Getting Started on Heroku with Go](https://devcenter.heroku.com/articles/getting-started-with-go)


