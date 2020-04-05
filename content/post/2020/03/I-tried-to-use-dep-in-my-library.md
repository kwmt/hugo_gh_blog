+++
title= "dep(Go dependency tool)を自作ライブラリに使ってみた"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Go"]
tags = ["wercker", "Go"]
keywords = [""]
+++
# はじめに

1個だけの外部ライブラリに依存している（超かんたんな）自作ライブラリ[github.com/kwmt/combine](https://github.com/kwmt/combine)を例に、[`dep`](https://github.com/golang/dep)コマンドを使ってみた内容をメモしておこうと思います。

# 基本的な使い方
## インストールと初期化

`dep`のインストールは、プロジェクトルートに移動して、go getします。

```
$ cd $GOPATH/src/github.com/kwmt/combine
$ go get -u github.com/golang/dep/...
```

次のコマンドで初期化します。

```
$ dep init
```

でgithubログインを促されましたが、`manifest.json`と`lock.json`が作成されます。[^1]

このとき、すでに依存ライブラリを見つけてくれていて、下記のように依存ライブラリが記述されています。[^2]

```manifest.json
{
    "dependencies": {
        "github.com/kwmt/go-utils": {
            "branch": "master"
        }
    }
}
```

```lock.json
{
    "memo": "",
    "projects": [
        {
            "name": "github.com/kwmt/go-utils",
            "branch": "master",
            "revision": "7431874e8437574169c0fb50e811d790fc1ed2ab",
            "packages": [
                "."
            ]
        }
    ]
}
```


## 依存ライブラリのダウンロード

ここまでではまだ依存ライブラリはダウンロードされてないので、

```
$ dep ensure -update
```

とします。すると`vendor`ディレクトリが作成されて、そのなかに依存ライブラリがダウンロードされます

```
$ ls vendor/github.com/kwmt/         
go-utils
```

## 状態を確認

`dep status`コマンドは、プロジェクトの依存関係の状態を見ることが出来ます。

```
$ dep status
PROJECT                   CONSTRAINT     VERSION        REVISION  LATEST   PKGS USED
github.com/kwmt/go-utils  branch master  branch master  7431874   7431874  1  
```

# おまけ

## `-v`オプションで詳細なログを出力

```
$ dep init -v
dep: Finding dependencies for "github.com/kwmt/combine"...
dep: Found 1 dependencies.
dep: Building dependency graph...
dep: Package "github.com/kwmt/combine", analyzing...
dep: Package "github.com/kwmt/combine" has import "github.com/kwmt/go-utils", analyzing...
dep: Analyzing transitive imports...
dep: Analyzing "github.com/kwmt/go-utils"...
dep: Writing manifest and lock files.
```

```
$ dep ensure -update -v
Root project is "github.com/kwmt/combine"
 1 transitively valid internal packages
 1 external packages imported from 1 projects
✓ select (root)
| ? attempt github.com/kwmt/go-utils with 1 pkgs; 1 versions to try
| | try github.com/kwmt/go-utils@master
| ✓ select github.com/kwmt/go-utils@master w/1 pkgs
✓ found solution with 1 packages from 1 projects

Solver wall times by segment:
      b-list-pkgs: 136.094407ms
  b-source-exists:  84.013757ms
           b-gmal:   1.874825ms
      select-root:     90.984µs
         new-atom:     57.145µs
      select-atom:     36.776µs
            other:     22.698µs
          satisfy:     14.206µs
  b-list-versions:      5.215µs

  TOTAL: 222.210013ms
```


## wercker.yml

wercker.ymlでの依存ライブラリダウンロードはこんな感じでよさそう

```
build:
  steps:
    - setup-go-workspace

    # Gets the dependencies
    - script:
        name: go get
        code: |
          go get -u github.com/golang/dep/...
          dep ensure -update -v
```

## github.com/sdboyer/gps

[dep](https://github.com/golang/dep/blob/master/cmd/dep/init.go#L154)のソースを軽く見ていたら、依存解決のメインは[github.com/sdboyer/gps](https://github.com/sdboyer/gps)というライブラリでやってるみたいですね。



# 注意
[README](https://github.com/golang/dep/blob/master/README.md)にもありますが、`manifest.json`と`lock.json`ファイルのフォーマットはまだ確定ではなく、変更される可能性があるとのこと。



# 参考
* [golang/dep](https://github.com/golang/dep)
* [golang オフィシャル謹製のパッケージ依存解決ツール「dep」](http://mattn.kaoriya.net/software/lang/go/20170125023240.htm)

# 注釈
[^1]: こちらの[issue168](https://github.com/golang/dep/issues/168)によると、`manifest.json`と`lock.json`のファイル名は変更になりそうです。
[^2]: 2017/02/02現在、`dep init` 後、`manifest.json`ファイルの中身が空っぽになる[issue149](https://github.com/golang/dep/issues/149)があるみたいです。

