---
title: '#golang でマッチングサイトを作った時の感想。'
author: kwmt
layout: post
date: 2013-12-18
url: /index.php/2013/12/18/golang-advent-calendar-2013/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - Advent Calendar
  - golang
  - Go言語
  - MySQL
  - net/http/fcgi
  - template

---
この記事は <a href="http://qiita.com/advent-calendar/2013/go" target="_blank">Go Advent Calendar 2013</a> の 18 日目の投稿です。

## はじめに {.section}

今年もいろいろと行われていますAdvent Calendarですが、初めて参加させて頂きます。プログラミング界隈のプロフェッショナルな方々の中で書くというのは、ただ1人で家で書いてるだけなのに、ちょっと緊張しちゃってます(^^ゞ　 

簡単にGo歴を。<a href="http://bit.ly/1dJqET7" target="_blank">2012年1月に勉強会</a>に参加してから、シンプルで守備範囲が広そうで強力で、なにより楽しいと感じ、それから自分で勉強したりして発表させて頂いたりしました(<a href="http://www.slideshare.net/yasi_life/presentations" target="_blank">slideshare</a>)。<a href="http://go-talks-kwmt.appspot.com/2013/gocon-spring.slide#1" target="_blank">神戸からGoCon Springに参加</a>したり、<a href="http://bit.ly/1dJqv20" target="_blank">神戸から名古屋の勉強会に参加</a>したりしました。 

そんな中、今年の10月に、マッチングサイトを作ってほしいという話があり、言語は何でもいいとのことだったので、担当者に&#8221;Goはキマスよ&#8221;と訴え続け、Goで作らせて頂きました。 

そこで、よく「Goで何ができるの？」って聞かれますので、「マッチングサイトが作れますよ」という事例を紹介したいところなのですが、そのマッチングサイトは、まだオープンしておらず、残念ながらリンクやスクリーンショットを貼ることができません。ので、そのマッチングサイトを作った時の&#8221;機能&#8221;と&#8221;構成&#8221;を簡単に説明した後、&#8221;感想&#8221;を書いていきたいと思います。

<!--more-->

## 機能 {.seciton}

このマッチングサイトのおおまかな機能としては、以下になります。 

  * 都道府県や店舗の特徴から、ある店舗を検索できる
  * 検索結果から、各店舗の詳細を見ることができる
  * お問い合わせフォームから各店舗に問い合わせできる(メール送信)
  * 各店舗を、新規に登録・更新・削除することができる
  * 登録時、画像をアップロードできる

マッチングサイトの必要最低限の機能のイメージです。

## 構成 {.section}

#### サーバーの構成

<a href="http://kwmt27.net/index.php/2013/10/03/nginx-golang-fcgi-mysql/" target="_blank">Nginx + Golang(FastCGI) + MySQL</a>で作成しました。 

#### パッケージ構成

`main`パッケージの他に`db`パッケージ、`data`パッケージ、後はテンプレートファイル(*.tmpl)です。 

MVCモデルのイメージで言うと、`data`,`db`パッケージがModelで、テンプレートはView、`main`パッケージがControllerというイメージで作成しました。あとは、`util`パッケージです。もちろん、db,data,utilという名前である必要はありません。 

#### 各パッケージのファイル数と総ステップ数

  * mainパッケージ・・・17ファイル
  * dataパッケージ・・・23ファイル
  * dbパッケージ・・・2ファイル
  * utilパッケージ・・・12ファイル
  * テンプレートファイル・・・37ファイル
  * 総ステップ数・・・8678ステップ

#### 使用した外部ライブラリ

データベース関連

  * MySQLのドライバ：<a href="https://github.com/go-sql-driver/mysql" target="_blank">&#8220;github.com/go-sql-driver/mysql&#8221;</a>
  * ORマッパー：<a href="https://github.com/coopernurse/gorp" target="_blank">&#8220;github.com/coopernurse/gorp&#8221;</a>

画像関連

  * 画像をリサイズする：<a href="https://github.com/nfnt/resize" target="_blank">&#8220;github.com/nfnt/resize&#8221;</a>

#### 実績工数

実装2人で約30人日(デザイン除く) 

## 感想

(これだけではイメージ沸かないですよね・・・すみません。。。感想に入らせて頂きます。) 

●現在、<a href="http://robfig.github.io/revel/" target="_blank">Revel</a>と先日の海外のAdvent Calenderで発表がありました<a href="http://blog.gopheracademy.com/day-05-beego" target="_blank">beego</a>というフレームワークがありますが、このくらいの規模ならフレームワークは不要かなと思いました。 

●ORMの<a href="https://github.com/coopernurse/gorp" target="_blank">gorp</a>を使わず、ゴリゴリSQLをかいても出来ますが、gorpにより非常に楽させてもらいました。個人的には使いやすかったと思います。 

●標準パッケージでテンプレート(<a href="http://golang.org/pkg/html/template/" target="_blank">html/template</a>)が使え、改めてフレームワークなどを入れる必要がないので、余計な使い方を覚える必要がないのが個人的には良いと思いました。 

●デザイン部分はデザイナーさんが担当したのですが、`if`文などのプログラミングがわかならなくても、&#8221;{{}}部分はプログラムしていますが、他のところはいじってもらって大丈夫です&#8221;ぐらいの説明で、テンプレートファイルを容易に理解して頂いたので、楽でした。 

●`{{define "header"}}ヘッダー{{end}}`のようにモジュール化でき、`{{template "header"}}`のようにして、モジュールを呼び出すことが出来ますが、あたりまえかもしれませんが、これもやみくもにモジュール化するのではなくて、分かりやすい構成にした方がよいと感じました。 

●テンプレートファイルの読み込みの仕方によっては、Goのバイナリを再コンパイルしなくても更新されるのは、非常に重宝しました。デザイナーさんがデザインを変更しても、ブラウザの更新だけでデザインの変更が確認できるからです。 

このようにする読み込みの仕方は、テンプレートファイルをParseするときに、関数の外で読み込まないことです。

<pre class="go">func RootHandler(w http.ResponseWriter, r *http.Request) {
	tmpl := template.New("main.tmpl")
	tmpl = template.Must(tmpl.ParseGlob("templates/*.tmpl"))
	data = ・・・
	tmpl.Execute(w,data)
	・・・
}
</pre>

つまり、次のようにしてしまうと、ブラウザの更新だけでは更新されません。 

<pre class="go">var  tmpl = template.Must((template.New("main.tmpl")).ParseGlob("templates/*.tmpl"))

func RootHandler(w http.ResponseWriter, r *http.Request) {
	data = ・・・
	tmpl.Execute(w,data)
	・・・
}
</pre>

●Go1.2からテンプレートに比較演算子が使えるようになりましたが(<a href="http://golang.org/doc/go1.2#text_template" target="_blank">本家変更点</a>, <a href="https://github.com/kwmt/golangwiki/blob/master/doc/go1.2.md#the-texttemplate-and-htmltemplate-packages" target="_blank">翻訳</a>)、個人的にはテンプレートをあまりプログラムちっくにすべきではないかもしれないと思いました。デザイナーさんにやさしいテンプレートを作成することが関係を良くするポイントかもしれません。 

●(感想というか覚え書きですが)テンプレートでは、ある構造体にレシーバとして定義したメソッドが使えます(<a href="http://golang.org/pkg/text/template/#hdr-Arguments" target="_blank">text/templateパッケージ</a>)。

例えば、 

<pre class="go">type Person struct{
	Address int
}

func (p *Person) Method() bool {
	・・・・
} 
</pre>

としたとき、テンプレートファイルを、

<pre class="go">{{if .Method}}
・・・
{{end}}
</pre>

とすれば、`Method`が使えます。<a href="http://golang.org/pkg/text/template/#FuncMap" target="_blank">template.FuncMap</a>(<a href="http://bit.ly/1acasGJ" target="_blank">使い方</a>)は、データに紐付けなくてもよい関数を作り、データに関連するものは上記の`Method`のように作るようにすれば分かりやすいかなと思います。

●(これも感想というか覚え書きですが)<a href="http://golang.org/pkg/net/http/fcgi/" target="_blank">fcgi.Serve</a>関数内で、要求があるたびに新しくゴルーチンを作っています(<a href="http://golang.org/src/pkg/net/http/fcgi/child.go?s=6606:6660#L262" target="_blank">ソース</a>)。 

## 終わりに {.section}

このマッチングサイトのリンクやスクリーンショットを示せず、とても分かりにくかったと思いますが、&#8221;Goってツール系のイメージがある&#8221;とか、&#8221;速さが必要なところでしか使わない&#8221;とか思ってる方に、普通にWebサイトが作れるんだ、ということを少しは知って頂けたかなと思い、終わりにしたいと思います。 

なにかGoに関してご相談ありましたら、[@kwmt27][1] までご連絡下さい。最後までお読み頂きありがとうございました。

 [1]: https://twitter.com/kwmt27