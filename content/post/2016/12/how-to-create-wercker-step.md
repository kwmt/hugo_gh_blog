+++
categories = ["CI"]
date = "2016-12-35T11:43:00+09:00"
tags = ["wercker", "wercker-step"]
title = "wercker-step.ymlの使い方"

+++

# はじめに
wercker CIでApplicationを新規作成したときにGoのテンプレートに下記のような記述を見かけました。

```
steps:
    - setup-go-workspace
```

これは、主にGoパッケージを作ったとき向けのセットアップっぽく、オレオレフレームワークなどには使いにくかったので、ここを変更できたらいいなと思って調べていたら、どうも <a href="https://github.com/wercker/step-setup-go-workspace" target="_blank">このレポジトリ</a>にある`run.sh`を実行してるっぽく、`setup-go-workspace`は`wercker-step.yml`で定義されてるっぽい。

なんとなく、`wercker-step.yml`と`run.sh`が必要そうってことはわかったけど、どうやって使うんだろうと思ったのがきっかけです。
そしてすぐにはわからなかったのでメモしておこうと思います。

ちなみに、`wercker-step.yml`はマニフェストファイルと呼ばれています。

# stepの作成からそのstepの使用まで
## 自作stepの作成
ということで、まず`wercker-step.yml`と`run.sh`という名前のファイルを用意します。


```
% mkdir wercker-step-helloworld && cd wercker-step-helloworld
% echo 'name: helloworld\nversion: 1.0.0' > wercker-step.yml
% echo 'echo "hello world!"' > run.sh
```

<a href="http://devcenter.wercker.com/docs/steps/creating-steps" target="_blank">ドキュメント</a>にあるように`wercker-step.yml`には、`name`と`version`だけが必須です。

`run.sh`には、hello world!と表示するだけのスクリプトを書いておきます。


GitHubにレポジトリを作ってプッシュします。
ということで、プッシュしたものがこちら。
<a href="https://github.com/kwmt/wercker-step-helloworld" target="_blank">https://github.com/kwmt/wercker-step-helloworld</a>

## Wercker directoryにデプロイ

先ほど作成したものをwerckerに登録して公開する必要があります。

こちらから<a href="https://app.wercker.com/" target="_blank">https://app.wercker.com/</a>stepを作成します。
<img src="./images/2016/12/create-wercker-step.png" >

このとき先ほど`wercker-step.yml`とかをプッシュしたレポジトリを指定します。

これでビルドが走ると思います。

これだけではまだ使えなくて、Wercker directoryというところにDeployする必要があります。
Wercker directoryにDeployするには、`deploy target`を指定する必要がありますが、新しく追加しなくても、`publish-step`というDeploy targetがすでに作成されてるはずなので、それを使います。


先ほどのビルドが成功したら、ビルドの詳細画面で`Build passed`と出ている右側に`Deploy to`というドロップダウンがあるので、そこをクリックして、`publish-step`というDeploy targetを指定し、`Start deploy`をクリックするとデプロイされます。

<img src="./images/2016/12/specify-deploy-target.png" >

デプロイされると下記URLのような感じになります。
<a href="https://app.wercker.com/applications/585f2630d8cb9e0100c142c4/tab/details/" target="_blank">https://app.wercker.com/applications/585f2630d8cb9e0100c142c4/tab/details/</a>

## 自作したstepをwercker.ymlで使う

自作したstepを使うには、`wercker.yml`に次のように記述します。

```
steps:
    - kwmt/helloworld
```

これでwerckerを走らせると、

<img src="./images/2016/12/run-my-wercker-step.png" >

`hello world!`が出力されてることがわかると思います。


# おわりに
`wercker-box.yml`というのもあってwercker directoryにデプロイするなどは同じっぽいのでこちらはさくっとできそう。
今気づきましたが、werckerのgolangのboxって、今日（2016/12/25）時点で、go1.5なんですね。。
<a href="https://github.com/wercker/box-golang" target="_blank">https://github.com/wercker/box-golang</a>
