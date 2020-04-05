
+++
title= "IntelliJ IdeaのCreate New ProjectでIntelliJ Platform Pluginを指定した時、右ペイン"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["IntelliJ"]
tags = ["IntelliJ Idea"]
keywords = [""]
+++

[IntelliJ IdeaのCreate New ProjectでIntelliJ Platform Pluginが無い場合の出し方](http://qiita.com/kwmt@github/items/f8faed913fdf2dc134f2)でIntelliJ Platform Pluginを表示することができました。

[IntelliJ IDEAのプラグインを作ろう！](http://qiita.com/Vexus2/items/e04a21f00e467b7ac8ad)を参考に、プラグインを作ろうと思って、[プロジェクトの作成](http://qiita.com/Vexus2/items/e04a21f00e467b7ac8ad#3-1)で、Create New Project - > IntelliJ Platform Pluginを指定した時、Project SDKが<None>で何も選択するものがない。

![IntelliJ Platform PluginのProject SDKが<None>](https://qiita-image-store.s3.amazonaws.com/0/22161/b40a2f8d-1dac-d278-a5b7-37992bffe3a2.png "IntelliJ Platform PluginのProject SDKが<None>")

これを選択できるようにするには、

1. 右側にある"New"をクリックします。
2. IntelliJ Ideaのインストールディレクトリを選択します。(Newしたときにデフォルトでインストールディレクトリにいるので、そのままChooseをクリックするだけです。)
![スクリーンショット 2014-11-18 17.27.11.png](https://qiita-image-store.s3.amazonaws.com/0/22161/57cca20c-f59a-5a59-f830-67ffc4032e0d.png "スクリーンショット 2014-11-18 17.27.11.png")
3. 使用したいJava SDKバージョンを指定しOKをクリックします。
4. これでNoneからIDEA IU-xxx.xxx.xのように選択できるようになります。
![スクリーンショット 2014-11-18 17.30.09.png](https://qiita-image-store.s3.amazonaws.com/0/22161/e5ade446-0ab4-ac3e-58d0-6ce40f131283.png "スクリーンショット 2014-11-18 17.30.09.png")



これは、[Getting Started with Plugin DevelopmentのConfiguring IntelliJ IDEA SDK](https://confluence.jetbrains.com/display/IDEADEV/Getting+Started+with+Plugin+Development#GettingStartedwithPluginDevelopment-anchor2)の

> Using the Select Path dialog box that opens, select the IntelliJ IDEA installation directory directory, and then click OK.

>Note, that by default, this dialog box automatically selects the home directory of the currently running IntelliJ IDEA installation.

この部分に記載されています。

### あわせてよむ
* [IntelliJ IdeaのCreate New ProjectでIntelliJ Platform Pluginが無い場合の出し方](http://qiita.com/kwmt@github/items/f8faed913fdf2dc134f2)

