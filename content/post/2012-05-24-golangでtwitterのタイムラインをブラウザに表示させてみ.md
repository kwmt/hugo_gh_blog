---
title: golangでTwitterのタイムラインをブラウザに表示させてみた。
author: kwmt
layout: post
date: 2012-05-23
url: /index.php/2012/05/24/golangでtwitterのタイムラインをブラウザに表示させてみ/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - golang
  - Go言語

---
## まえおき

Andrew Gerrandさんのgithubはgolangの宝の宝庫。その中に
  
[https://github.com/nf/gotweet][1]{.broken_link}というTwitter APIを使うサンプルがあったので（2013/04/07追加 リポジトリが削除されてました）、
  
参考にさせてもらって、それに[GAEのチュートリアル][2]をあわせて、
  
Twitterのuser_timelineをブラウザに表示させるようにしてみました。 

## 準備

gotweetのプロジェクトをcloneします。（あとで名前をgotweet_changeプロジェクトに変更しています） 

<pre class="terminal">% cd workspace/
% git clone github.com/nf/gotweet
</pre>

READMEにも書かれてますが、mrjonesさんのoauthライブラリを使用するので、まずインストールしてください。
  
go install とありますが、うまく動かなかったので、clone しています。
  
（goファイルのimport文には`"github.com/mrjones/oauth"`となっているのを
  
`"oauth"`と変更しています。） 

<pre class="terminal">% git clone github.com/mrjones/oauth
</pre>

clone先のディレクトリは、僕が確認したのは、myappのGAEのプロジェクトだとすると、myappディレクトリの直下においてください。
  
下図のようなイメージです。 

<pre class="terminal">gotweet_change/
app.yaml
hello/
hello.go
oauth/
template/
tweet.html
</pre>

READMEには、ドキュメントを読んでください、と書いています。`oauth`ライブラリの使い方の説明などが書いています。
  
（まだ軽くしか読んでないので、いずれちゃんと読みます。。） 

<pre class="terminal">% godoc github.com/mrjones/oauth | less
</pre>

実際にTwitterAPIを使用する際は、
  
Consumer IDとConsumer Secret を[Twitter　Devloperサイト][3]{.broken_link}から取得しておいてください。 

## ソースごにょごにょ

全体のソースは[github][4]にあげました。
  
忘れそうなポイントだけ書いておくことにします。 

<!--more-->

JSONデータのたくさんのパラメータから、type structで取得したいパラメータ名のみを定義すると、
  
そのパラメータのみ取得できます。（取得したいデータを絞り込むことができます） 

<pre class="go">// 取得したいパラメータをstructで記述
// 参考 https://dev.twitter.com/docs/api/1/get/statuses/mentions
type TweetObject struct{
    Created_at string
    Id_str string
    Text string
    Source string
    In_reply_to_user_id_str string
    User UserObject // JSONオブジェクト内のオブジェクトをこのように定義する。
}
// JSONオブジェク内のオブジェクト
type UserObject struct{
    Id_str string
    Name string
    Screen_name string
}
</pre>

下記は、TwitterのtimelineをGETしてパースしてテンプレートに出力しているところです。
  
コメント参照。 

<pre class="go">resp, err := consumer.Get(url,nil, &atoken) //TimelineをGetしてrespに格納
w.Header().Add("Content-type","text/html charset=utf-8") // ヘッダー追加
body, err := ioutil.ReadAll(resp.Body) //respを読み込んでbodyに格納
var tweets []TweetObject // 実体を宣言
err2 := json.Unmarshal(body,&tweets) // UnmarshalはJSONエンコードデータをパースします
t,err:= template.ParseFiles("template/tweet.html")  // gotweet_change/template/tweet.html　に配置
t.Execute(w, tweets) // tweet.htmlにtweetsを埋め込んで表示する
</pre>

Go version 1 になってからかなりのパッケージが変更になっています。詳細は以下のページが分りやすいかもです。
  
<http://golang.org/doc/go1.html#packages> 

たとえば、`"json"`は`"json"`だけでよかったのが、`"encoding/json"`と変更になっています。 

7行目の[`json.Unmarshal`][5]について、
  
JSONエンコードされたデータ（ここでは`body`）を解析し、`tweets`に格納しています。 

[`template.ParseFiles()`][6]の引数で
  
テンプレートとなるファイルを指定することが出来ます。`template.ParseFiles("template/tweet.html")`と書くと、
  
gotweet_changeプロジェクトであれば、`gotweet_change/template/tweet.html`としてテンプレートファイルを作成します。 

また、`template.ParseFiles()`の引数には、複数ファイルを指定することができ、
  
たとえば、`header.html、main.html、footer.html`などに分けることができます。 

さて、`tweet.html`の中身は以下のようになっています。 

<pre class="go">&lt;!DOCTYPE html&gt;
&lt;html lang="ja"&gt;
&lt;head&gt;
&lt;meta charset="UTF-8"&gt;
&lt;style type="text/css"&gt;
    div.tl  {width:600px;}
    table,td {border: solid; border-collapse: collapse;}
&lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div class="tl"&gt;
&lt;table border cellspacing=0&gt;
{{range .}}
    &lt;tr&gt;
    &lt;td&gt;
    日時：{{.Created_at}}&lt;br/&gt;
    ツイート：{{.Text}} &lt;br/&gt;
    スクリーン名：{{.User.Screen_name}}
&lt;/td&gt;
&lt;/tr&gt;
{{end}}
&lt;/table&gt;
&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>

`{{range .}} T1 {{end}}`で`T1`を繰り返します。ドットは、この例では`tweets`です。
  
`{{.Created_at}}`や`{{.Text}}`は、`TweetObject`のメンバ変数に当たるもので、それらを表示します。 

`{{.User.Screen_name}}`は`UserObject`のメンバ変数`Screen_name`を表示するということです。
  
これらの書き方は、<a href="http://golang.org/pkg/text/template/#hdr-Actions" target="_blank">http://golang.org/pkg/text/template/#hdr-Actions</a>が参考になると思います。 

これを実行すると、下図のように、ユーザータイムラインを取得することができました。 

<img src="http://kwmt27.net/images/2012/05/gotweet-447x300.png" alt="gotweet" width="447" height="300"/>

## 参考

jsonのUnmarshalについて参考にしたgist
:   <https://gist.github.com/775526>

Templateについて参考にしたgolang_guestbook
:   [https://bitbucket.org/IanLewis/golang_guestbook/src/ece99a50ddfa/templates/base.html][7]

 [1]: https://github.com/nf/gotweet
 [2]: http://androg.seesaa.net/article/267361381.html
 [3]: http://dev.twitter.com/apps/new
 [4]: https://github.com/kwmt/gotweet_change
 [5]: http://golang.org/src/pkg/encoding/json/decode.go
 [6]: http://golang.org/src/pkg/html/template/template.go?h=ParseFiles#L283
 [7]: https://bitbucket.org/IanLewis/golang_guestbook/src/ece99a50ddfa/templates/base.html