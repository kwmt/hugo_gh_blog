+++
categories = ["blog"]
date = "2016-02-07T13:19:40+09:00"
tags = ["Hugo", "Wordpress"]
title = "WordpressからHugoに移行した時のメモ"
draft = false
+++


WordpressからHugoに移行してみたので、そのときやったメモを残しておこうかと思います。

おおまかにやったこととしては以下です。

* WordpressからHugoに変換
* Github Pagesでホスティング
* GithubにプッシュでWerker CIを使って自動ビルド&デプロイ

## Wordpressの記事をHugoのマークダウンに変換


1.Wordpress置いてるサーバーログインして <a href="https://github.com/SchumacherFM/wordpress-to-hugo-exporter" target=_blank>wordpress to hugo</a>プラグインをダウンロードします。(wordpressのプラグイン検索ではみつからなかった)

```bash
$ git clone https://github.com/SchumacherFM/wordpress-to-hugo-exporter.git wp-content/plugins/wordpress-to-hugo-exporte
```

2.変換を実行します。

```bash
$ cd wp-content/plugins/wordpress-to-hugo-exporte
$ php hugo-export-cli.php

This is your file!
/tmp/wp-hugo.zip
```

3.変換されたwp-hugo.zipをローカルにダウンロードします。

4.画像パスなどがWPとHugoではことなるので、変換します。

* hugo-export/post を content/postに移動
* hugo-export/wp-content/uploads を content/uploadsに移動してuploadsをimagesにリネーム

のように一括置換すればよいでしょう。これでもパスが異なることがあるかも。



## Hugoで公開する準備


Githubで、2つrepositoryを作成します。

* hugo_gh_blog : 名前はなんでもOKです。これはblogのバージョン管理用です。ブログ記事を書いたらコチラにプッシュします。
* &lt;username&gt;.github.io : こちらが公開用になります。(デプロイ先です)

cloneしてきた`hugo_gh_blog`で `hugo new site .`してHugoサイトを作成します。
その辺りは <a href="https://gohugo.io/overview/quickstart/" target=_blank>Hugoの公式サイトQuick start</a>を見て下さい。


#### テーマ

テーマは <a href="http://themes.gohugo.io/" target=_blank>こちら</a>にまとまってるので、こちらからダウンロードします。

ぼくはcrispというテーマをカスタマイズしています。


```bash
% git clone https://github.com/kwmt/hugo-theme-crisp thmemes/hugo-theme-crisp
```

として、`themes`の下にテーマをcloneします。


```bash
% hugo server --theme=hugo-theme-crisp
```

で確認できます。


#### git管理

テーマはhugo_gh_blogレポジトリにaddしてしまいました。git submoduleの使い方がよくわからなくて。
ので、`thmemes/hugo-theme-crisp/.git` は削除しています。


`hugo server`とすると`public`ディレクトリができますが、こちらはgit管理にしないでください。gitignoreにかいておくとよいと思います。


## Wercker CIと連携

Hugoの記事作成からブログ公開までの流れとして、

1. 記事を書きます。
2. hugoコマンドでビルドします(publicディレクトリが作成されます)。
3. publicディレクトリをサーバーにアップロードします。

という流れになります。それを記事を書いてgithubにプッシュしたら上記の2,3は自動でやってもらうようにする設定をします。

WerckerCIのアカウント作ったり、ブログ管理用のレポジトリと連携させたりする基本設定はググるとして、
<a href="https://github.com/kwmt/hugo_gh_blog/blob/master/wercker.yml" target=_blank>wercker.yml</a>にビルドツールやデプロツールを使用するする記述をしてしまえば終わりです。

(slackに通知させるようにしていて、DeployとBuildは同じように書いてるのですが、Deploy時は通知来て、ビルド時に通知がこなくて困ってます。。)




これで、 <a href="https://github.com/kwmt/hugo_gh_blog">hugo_gh_blog</a>レポジトリにプッシュしたら、公開用の<a href="https://github.com/kwmt/kwmt.github.io">GithubPages</a>にデプロイされるようになりました。


## 記事作成手順

記事ファイル作成して、記事書いて、コミットして、プッシュ

```bash
% hugo new post/2016/02/foo.md
% git add content/post/2016/02/foo.md
% git commit -m 'add foo'
% git push
```

記事ファイルのディレクトリ構成は、まだ迷ってますが、わりと不満のない環境になりました。


## 参考
<a href="http://bit.ly/1lZ1Okf" target=_blank>自分のGitHub Pagesと独自ドメインの設定方法</a>

<a href="http://blog.negimic.com/post/wercker-success/" target=_blank>werckerでhugoビルド→githubにデプロイ · negimic blog </a>

<a href="http://blog.jigyakkuma.org/2015/02/11/hugo/" target=_blank>blogをoctopressからHugoに乗り換えたメモ</a>

<a href="http://qiita.com/syui/items/869538099551f24acbbf" target=_blank>HUGOを使ってサイトを立ち上げる方法</a>

<a href="http://nobu666.com/2015/06/21/1015.html" target=_blank>WordPressからGitHub Pages + hugoに移行した</a>




