
+++
title= "IntelliJ IdeaのCreate New ProjectでIntelliJ Platform Pluginが無い場合の出し方"
date= 2020-03-26T00:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["IntelliJ"]
tags = ["IntelliJ Idea"]
keywords = [""]
+++

[IntelliJ IDEAのプラグインを作ろう！](http://qiita.com/Vexus2/items/e04a21f00e467b7ac8ad)を参考に、プラグインを作ろうと思って、[プロジェクトの作成](http://qiita.com/Vexus2/items/e04a21f00e467b7ac8ad#3-1)をしようとしたら、”IntelliJ Platform Plugin"がない！！！

![Create New Project](https://qiita-image-store.s3.amazonaws.com/0/22161/bc004d71-e4a3-ea8a-4b7b-3915f9cf1572.png "Create New Project(IntelliJ Platform Plugin無し)")


”IntelliJ Platform Plugin"でググったりしてたんですが、”IntelliJ Platform Plugin"の出し方を書いた記事が見つからない。

いろいろIntelliJ IdeaのPluginあたりを見ててやっと見つけました！

![スクリーンショット 2014-11-18 17.03.52.png](https://qiita-image-store.s3.amazonaws.com/0/22161/131e5eed-e619-9ba6-64d9-22521c132b8d.png "スクリーンショット 2014-11-18 17.03.52.png")

この"Plugin DevKit"が有効でなかったためです。
これを有効にしてOKして再起動すると
![スクリーンショット 2014-11-18 17.06.10.png](https://qiita-image-store.s3.amazonaws.com/0/22161/69c2aca7-38ec-2f5f-05c5-aa12760957f9.png "Create New Project(IntelliJ Platform Plugin有り)")

"IntelliJ Platform Plugin"が出ました！！

あとでよくよく[Getting Started with Plugin DevelopmentのPreliminary Steps](https://confluence.jetbrains.com/display/IDEADEV/Getting+Started+with+Plugin+Development#GettingStartedwithPluginDevelopment-anchor1)を見てみると、
> Plugin DevKit
> Please make sure that the "Plugin DevKit" plugin is enabled in Settings | Plugins.

「Plugin設定で、"Plugin DevKit"プラグインが有効になっていることを確認してください」って書いてました…（汗）

### あわせてよむ
* [IntelliJ IdeaのCreate New ProjectでIntelliJ Platform Pluginを指定した時、右ペインのProject SDKを指定する方法](http://qiita.com/kwmt@github/items/afee19bcca4d3b36f4af)



