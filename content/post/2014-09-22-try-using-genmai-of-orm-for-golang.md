---
title: 'Genmaiを使ってみた。 #golang'
author: kwmt
layout: post
date: 2014-09-22
url: /index.php/2014/09/22/try-using-genmai-of-orm-for-golang/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - Genmai
  - golang
  - Go言語
  - MySQL

---
ORM for golangの<a href="http://bit.ly/1ucuNGX" target="_blank">Genmai</a>を使って、MySQLにアクセスしてみた。

使い方は以下の様な感じです。

テーブルを定義します。

<pre class="go">// ユーザーテーブル
type User struct {
	Id    int `db:"pk"`
	Name  string
	genmai.TimeStamp
}
</pre>

<!--more-->

データベースを作る時に、`&genmai.MySQLDialect{}`と、<a href="https://github.com/go-sql-driver/mysql/#dsn-data-source-name" target="_blank">DSN</a>を指定する。

<pre class="go">database, err := genmai.New(&genmai.MySQLDialect{}, dataSourceName)
// userテーブルがなければ、作成する
if err := database.CreateTableIfNotExists(&User{}); err != nil {
	panic(err)
}
</pre>

<pre class="go">// 挿入したいデータ
data := []User{
		{Name:"Aさん"},
		{Name:"Bさん"},
		{Name:"Cさん"},
	}
// dataを挿入
if _, err := database.Insert(data); err!= nil{
	panic(err)
}
</pre>

<pre class="go">// 取得したい空のデータを用意
var users []User
// データを取得
if err:= db.Select(&users); err != nil{
	panic(err)
}
</pre>

これを実行すると、
  
テーブル作成できた！
  
<img src="http://kwmt27.net/wp-content/uploads/2014/09/table_struct-600x73.png" alt="table_struct" width="600" height="73" class="aligncenter size-thumbnail wp-image-1321" srcset="http://kwmt27.net/wp-content/uploads/2014/09/table_struct-600x73.png 600w, http://kwmt27.net/wp-content/uploads/2014/09/table_struct-300x36.png 300w, http://kwmt27.net/wp-content/uploads/2014/09/table_struct-1024x125.png 1024w, http://kwmt27.net/wp-content/uploads/2014/09/table_struct-624x76.png 624w, http://kwmt27.net/wp-content/uploads/2014/09/table_struct-900x110.png 900w" sizes="(max-width: 600px) 100vw, 600px" />

データの挿入もOK！
  
<img src="http://kwmt27.net/wp-content/uploads/2014/09/table_content-600x152.png" alt="table_content" width="600" height="152" class="aligncenter size-thumbnail wp-image-1320" srcset="http://kwmt27.net/wp-content/uploads/2014/09/table_content-600x152.png 600w, http://kwmt27.net/wp-content/uploads/2014/09/table_content-300x76.png 300w, http://kwmt27.net/wp-content/uploads/2014/09/table_content-1024x259.png 1024w, http://kwmt27.net/wp-content/uploads/2014/09/table_content-624x158.png 624w, http://kwmt27.net/wp-content/uploads/2014/09/table_content-900x228.png 900w, http://kwmt27.net/wp-content/uploads/2014/09/table_content.png 1128w" sizes="(max-width: 600px) 100vw, 600px" />

あれ？データの取得が下記のエラーでコケるなぁ。

<pre class="go">unsupported driver -> Scan pair: []uint8 -> *time.Time
</pre>

`time.Time`を使ってる箇所は、`User`構造体の`genmai.TimeStamp`のところだけです。ちなみに`genmai.TimeStamp`がどうなっているかというと、

<pre class="go">// TimeStamp is fields for timestamps that commonly used.
type TimeStamp struct {
	// Time of creation. This field will be set automatically by BeforeInsert.
	CreatedAt time.Time `json:"created_at"`

	// Time of update. This field will be set by BeforeInsert or BeforeUpdate.
	UpdatedAt time.Time `json:"updated_at"`
}
</pre>

となっています。

また、MySQLのドライバに、<a href="https://github.com/go-sql-driver/mysql" target="_blank">github.com/go-sql-driver/mysql</a>を使っています。

うん、ココらへんが怪しいですね。とおもって調べていたら、<a href="https://github.com/go-sql-driver/mysql" target="_blank">github.com/go-sql-driver/mysql</a>は`time.Time`はサポートしているのですが、[]byteからtime.Timeへの変換がデフォルトではできません。これを可能にするには、DSNのパラメータに、`parseTime=true`をつける必要があります。というのが、下記に書いてあります。
  
<a href="https://github.com/go-sql-driver/mysql#timetime-support" target="_blank">https://github.com/go-sql-driver/mysql#timetime-support</a>

以上です。