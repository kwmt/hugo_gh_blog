---
title: はじめてのPolymer1.0(Webコンポーネントの作り方)
author: kwmt
layout: post
date: 2015-06-23
url: /index.php/2015/06/23/how-to-make-web-component/
categories:
  - polymer
tags:
  - polymer

---
## はじめに

<a href="https://sites.google.com/site/devfestjapan/" target="_blank">Google I/O報告会 2015 東京会場</a>のブレイクアウトセッションの中でWebコンポーネントの作り方をライブコーディングで紹介して頂きまして、一緒に実際にやって、はじめての自分にとってはすっごくわかりやすくて面白かったので残しておこうとメモ。（公開してもいいことは確認済みです）

当日は、下記の前提がインストールされてなかったので動かすところはできませんでしたが、いまこの記事を書きながら実際に動かしてみています。

## 前提

  1. <a href="http://qiita.com/oreo3@github/items/eb790fc091aa28af8d33" target="_blank">npmをインストールする</a>
  2. <a href="http://qiita.com/oreo3@github/items/eb790fc091aa28af8d33" target="_blank">Bowerをインストールする</a>
  3. `$HOME/polymer/devfest`ディレクトリがあるとします。

## 前提の補足

**`$ nodebrew install latest`でソースからインストールしたが、`node -v`などと打つと`command not found: node`と言われた時の対処法**
  
→バージョンを指定するだけ。

### 指定するには？

1.インストールされてるバージョンを確認

<pre class="go">$ nodebrew ls
v0.12.5
</pre>

2.v0.12.5を指定する

<pre class="go">$ nodebrew use v0.12.5
</pre>

3.確認

<pre class="go">% node -v
v0.12.5
</pre>

## Webコンポーネントの作り方(ここからライブコーディングでした)

<!--more-->

  1. `$HOME/polymer/devfest` に移動して、bower init

<pre class="go">$ bower init
</pre>

<a href="http://qiita.com/oreo3@github/items/eb790fc091aa28af8d33#%E5%88%9D%E6%9C%9F%E5%8C%96%E6%99%82%E3%81%AE%E8%B3%AA%E5%95%8F" target="_blank">いろいろ質問されて</a>、bower.jsonが出来上がります。

  1. polymerをインストール

<pre class="go">$ bower install polymer
bower polymer#*             not-cached git://github.com/Polymer/polymer.git#*
bower polymer#*                resolve git://github.com/Polymer/polymer.git#*
...

webcomponentsjs#0.7.5 bower_components/webcomponentsjs
</pre>

bower_componentsディレクトリが作成されている。

<pre class="go">$ ls
bower.json        bower_components/
</pre>

3.index.htmlを作成する。

<pre class="go">&lt;!DOCTYPE HTML&gt;
&lt;html lang="ja"&gt;
&lt;head&gt;
    &lt;script src="bower_components/webcomponentsjs/webcomponents.js"&gt;&lt;script&gt;
    &lt;!-- load component --&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;!-- これから作る部品 --&gt;
  &lt;devfest-component&gt;&lt;/devfest-component&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>

4.devfest-componentを作成する
  
4.1. コンポーネントを入れるディレクトリを作成

<pre class="go">$ mkdir components
</pre>

4.2. devfestComponent.htmlを作成

<pre class="go">$ vi devfestComponent.html
&lt;link rel="import" href="../bower_components/polymer/polymer.html"&gt;

  &lt;dom-module id="devfest-component"&gt;
    // テンプレート
    &lt;template&gt;
      &lt;h1&gt;{{title}}&lt;/h1&gt;
      &lt;p&gt;Hey!&lt;/p&gt;
    &lt;/template&gt;
    // スタイル
    &lt;style&gt;
      h1 {
        color:red;
      }
    &lt;/style&gt;
    // 部品に対して動くスクリプト
    &lt;script&gt;
      Polymer ({
        // どのコンポーネントかのひもづけ
        is :"devfest-component",
        properties : {
            title : {
                type:String,
                value: "Yes, Polymer!"
            }
        }
      });
    &lt;/script&gt;
  &lt;/dom-module&gt;
</pre>

メモだけでは動かなかったので、ここらへんも参考にした

  * <a href="https://www.polymer-project.org/1.0/docs/devguide/feature-overview.html" target="_blank">Feature overview &#8211; Polymer</a> 
  * <a href="https://www.polymer-project.org/0.5/docs/polymer/polymer.html" target="_blank">API developer guide &#8211; Polymer</a> 

5.index.htmlに4で作ったコンポーネントをインポートする。

<pre class="go">&lt;!DOCTYPE HTML&gt;
&lt;html lang="ja"&gt;
&lt;head&gt;
    &lt;script src="bower_components/webcomponentsjs/webcomponents.js"&gt;&lt;/script&gt;
    &lt;!-- load component --&gt;
    &lt;link rel="import" href="components/devfestComponent.html"&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;!-- これから作る部品 --&gt;
  &lt;devfest-component&gt;&lt;/devfest-component&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>

6.動かすにはWebサーバーが必要だが、polyserveというのが用意されているので、インストールする。

<pre class="go">$ npm install polyserve -g
$ polyserve
Starting Polyserve on port 8080
Files in this directory are available at localhost:8080/components/devfest/...
</pre>

7.確認
  
<img src="http://kwmt27.net/images/2015/06/polymer_finish-600x251.png" alt="polymer_finish" width="600" height="251" class="aligncenter size-thumbnail wp-image-1502" srcset="http://kwmt27.net/images/2015/06/polymer_finish-600x251.png 600w, http://kwmt27.net/images/2015/06/polymer_finish-300x125.png 300w, http://kwmt27.net/images/2015/06/polymer_finish-624x261.png 624w, http://kwmt27.net/images/2015/06/polymer_finish.png 834w" sizes="(max-width: 600px) 100vw, 600px" />

以上

<!-- shadow domの話があったが、あとにする
$ vi index.html
 <!DOCTYPE html>
 <html>
  
<body>
  // これから作る部品
  <devfest-component></devfest-component>
</body>
</html>
 -->