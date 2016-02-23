---
title: Intellij IDEAでPlant UMLを使えるようにする。
author: kwmt
layout: post
date: 2014-09-11
url: /index.php/2014/09/12/how_to_setup_plant_uml_in_intellij_idea/
pdrp_attributionLocation:
  - end
categories:
  - Plant UML
tags:
  - Intellij IDEA
  - Plant UML

---
## はじめに

[WEB+DB PRESS Vol.82 の「Web APIデザインの鉄則」特集を読んで][1]、<a href="http://plantuml.sourceforge.net/index.html" target="_blank">Plant UML</a>というツールを知ったのですが、さっそくIntellij IDEAで使えたらいいなぁと思って、使えるようになったので、その設定方法を書いておこうかと思います。といっても、プラグインをインストール＋αぐらいですが、、、 

<!--more-->

## 設定方法

いつものIntellij IDEAにPluginをインストール画面(設定画面(⌘ + ,)->Pluginを選択->Browse repositoriesをクリック)から、&#8221;plant&#8221;で検索すると下記のような検索結果がでますが、「PlantUML integration」をインストールする。 

<img src="http://kwmt27.net/images/2014/09/スクリーンショット-2014-09-11-18.49.41-360x300.png" alt="PlantUML integration Plugin選択画面" width="360" height="300" class="aligncenter size-thumbnail wp-image-1294" srcset="http://kwmt27.net/images/2014/09/スクリーンショット-2014-09-11-18.49.41-360x300.png 360w, http://kwmt27.net/images/2014/09/スクリーンショット-2014-09-11-18.49.41-1024x852.png 1024w, http://kwmt27.net/images/2014/09/スクリーンショット-2014-09-11-18.49.41-624x519.png 624w" sizes="(max-width: 360px) 100vw, 360px" />

これだけで、とりあえず、test.pumlファイルを作ってみました（New ->UML State）。するとサンプルコードが書かれたファイルが作成されます。 

コードがあるからには図も見たいなぁと思って、View -> Tool Windows -> PlantUMLを選択し、表示させようとしたところ、&#8221;Dot Executable〜&#8221;などとエラーが出てしまいました。 

このエラーにも書かれていますが、<a href="http://www.graphviz.org/" target="_blank">Graphviz(dot)</a>というソフトが必要になりますので、それを<a href="http://www.graphviz.org/Download..php" target="_blank">インストール</a>します。 

Intellij IDEAが起動している場合は再起動すると、図が表示されることが確認できると思います。 

<img src="http://kwmt27.net/images/2014/09/PlantUML_Intellij-407x300.png" alt="PlantUML_Intellij" width="407" height="300" class="aligncenter size-thumbnail wp-image-1296" srcset="http://kwmt27.net/images/2014/09/PlantUML_Intellij-407x300.png 407w, http://kwmt27.net/images/2014/09/PlantUML_Intellij-1024x754.png 1024w, http://kwmt27.net/images/2014/09/PlantUML_Intellij-624x459.png 624w, http://kwmt27.net/images/2014/09/PlantUML_Intellij-900x662.png 900w" sizes="(max-width: 407px) 100vw, 407px" />

## 参考

・<a href="http://bit.ly/13K3Xwm" title="Plant UML公式サイト" target="_blank">Plant UML公式サイト</a>
  
・<a href="http://yohshiy.blog.fc2.com/blog-entry-152.html#install" target="_blank">PlantUML の使い方 | プログラマーズ雑記帳</a>

 [1]: http://kwmt27.net/index.php/2014/09/11/read_webdbpress_about_webapi_design/ "WEB+DB PRESS Vol.82 の「Web APIデザインの鉄則」特集を読んだ。"