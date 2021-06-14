
+++
title= "Flutter for webのサンプルを動かしてみた"
date= 2019-05-11T18:10:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Flutter"]
tags = ["Flutter","flutter_web"]
keywords = ["flutter_web","flutter"]
+++

<!--more-->

## サンプルをgit clone

```shell
$ git clone git@github.com:kwmt/flutter_web.git
```

## サンプルプロジェクトディレクトリに移動

```
cd flutter_web/examples/hello_world/  
```

## flutter_webのビルドツールをインストール
flutter packages pub global activate webdev


```
Resolving dependencies...
+ args 1.5.1
+ async 2.2.0
+ browser_launcher 0.1.2
+ build_daemon 0.6.0
+ built_collection 4.2.1
+ built_value 6.5.0
+ charcode 1.1.2
+ codemirror 0.5.4+5.45.0
+ collection 1.14.11
+ convert 2.1.1
+ crypto 2.0.6
+ devtools 0.0.19 (0.1.0 available)
+ devtools_server 0.1.2
+ dwds 0.3.2
+ fixnum 0.10.9
+ http 0.12.0+2
+ http_multi_server 2.0.6
+ http_parser 3.1.3
+ intl 0.15.8
+ io 0.3.3
+ js 0.6.1+1
+ json_annotation 2.2.0
+ logging 0.11.3+2
+ matcher 0.12.5
+ meta 1.1.7
+ mime 0.9.6+2
+ octicons_css 0.0.1
+ package_config 1.0.5
+ package_resolver 1.0.10
+ path 1.6.2
+ pedantic 1.6.0
+ platform_detect 1.3.5
+ plotly_js 0.0.1
+ polymer_css 0.0.1
+ pool 1.4.0
+ primer_css 0.0.2
+ pub_semver 1.4.2
+ pubspec_parse 0.1.4
+ quiver 2.0.3
+ rxdart 0.21.0 (0.22.0 available)
+ shelf 0.7.5
+ shelf_proxy 0.1.0+6
+ shelf_static 0.2.8
+ shelf_web_socket 0.2.3
+ source_maps 0.10.8
+ source_span 1.5.5
+ split 0.0.2
+ sse 2.0.2
+ stack_trace 1.9.3
+ stream_channel 2.0.0
+ stream_transform 0.0.19
+ string_scanner 1.0.4
+ term_glyph 1.1.0
+ typed_data 1.1.6
+ uuid 2.0.1
+ vm_service_lib 3.15.1+1 (3.15.1+2 available)
+ watcher 0.9.7+10
+ web_socket_channel 1.0.12
+ webdev 2.0.4
+ webkit_inspection_protocol 0.4.0
+ yaml 2.1.15
Downloading webdev 2.0.4...
Downloading webkit_inspection_protocol 0.4.0...
Downloading async 2.2.0...
Downloading shelf_proxy 0.1.0+6...
Downloading pedantic 1.6.0...
Downloading build_daemon 0.6.0...
Downloading sse 2.0.2...
Downloading dwds 0.3.2...
Downloading vm_service_lib 3.15.1+1...
Downloading built_collection 4.2.1...
Downloading meta 1.1.7...
Downloading built_value 6.5.0...
Downloading json_annotation 2.2.0...
Downloading stream_transform 0.0.19...
Downloading http_multi_server 2.0.6...
Downloading devtools 0.0.19...
Downloading split 0.0.2...
Downloading rxdart 0.21.0...
Downloading primer_css 0.0.2...
Downloading polymer_css 0.0.1...
Downloading plotly_js 0.0.1...
Downloading platform_detect 1.3.5...
Downloading octicons_css 0.0.1...
Downloading devtools_server 0.1.2...
Downloading browser_launcher 0.1.2...
Downloading codemirror 0.5.4+5.45.0...
Downloading quiver 2.0.3...
Precompiling executables...
Precompiled webdev:webdev.
Installed executable webdev.
Warning: Pub installs executables into $HOME/.pub-cache/bin, which is not on your path.
You can fix that by adding this to your shell's config file (.bashrc, .bash_profile, etc.):

  export PATH="$PATH":"$HOME/.pub-cache/bin"

Activated webdev 2.0.4.
```

## パス追加
pub-cacheにパスが通ってなければ、パス追加する。

```
# flutter for web
export PATH="$HOME/.pub-cache/bin:$PATH/"
export PATH="/usr/local/flutter/bin/cache/dart-sdk/bin:$PATH"
```

## 依存ライブラリのダウンロード

```shell
$ flutter pub get
! flutter_web 0.0.0 from path ../../packages/flutter_web                
! flutter_web_ui 0.0.0 from path ../../packages/flutter_web_ui          
Running "flutter packages get" in hello_world...                    0.7s
```

## 実行

```shell
$ webdev serve                                                                         
[INFO] Serving `web` on http://localhost:8080
[INFO] Running build completed, took 305ms
[INFO] Caching finalized dependency graph completed, took 150ms
[INFO] Succeeded after 464ms with 0 outputs (0 actions)

```

## ブラウザでlocalhost:8080にアクセス

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">おー！flutter for webが動いた！感動！！ <a href="https://t.co/Nq6vqG3ABW">pic.twitter.com/Nq6vqG3ABW</a></p>&mdash; yasi kawamoto (@yasi_kawamoto) <a href="https://twitter.com/yasi_kawamoto/status/1126001578752655362?ref_src=twsrc%5Etfw">May 8, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
