---
title: golangからMySQLを使うには
author: kwmt
layout: post
date: 2012-09-06
url: /index.php/2012/09/06/golangからmysqlを使うには/
categories:
  - golang
tags:
  - golang
  - Go言語

---
## まえおき {.section}

今回はMacにMySQLをインストールして、golangからアクセスしてみます。 

MacにMySQLをインストールする方法はググればわかるので、MySQLがインストールされているものとします。 

golangからMySQLにかぎらずデータベースにアクセスするライブラリを、
  
すでに多くの人が作ってくれているので、そのライブラリのどれかを使わせてもらいます。
  
([go-wiki][1]にいろいろあります。) 

ここでは、[mymysql][2]というライブラリを使ってmysqlにアクセスしてみます。 

## 準備 {.section}

まずは、mymysqlをインストールしましょう。 

<pre class="go">$ go get github.com/ziutek/mymysql/thrsafe
$ go get github.com/ziutek/mymysql/autorc
$ go get github.com/ziutek/mymysql/godrv
</pre>

次に、mysqlを起動して 

<pre class="go">% mysql -u root -p
</pre>

データベース、ユーザーを作って、パスワードを設定します。 

<pre class="go">mysql> create database mymysqltest;
Query OK, 1 row affected (0.00 sec)
mysql> grant all privileges on mymysqltest.* to mymysqltestuser@localhost;
Query OK, 0 rows affected (0.00 sec)
mysql> set password for mymysqltestuser@localhost = password("TestPassw9");
Query OK, 0 rows affected (0.00 sec)
</pre>

## サンプルを編集して実行して確認する {.section}

次にmymysqlのサンプルを編集して実行してみます。サンプルの場所は 

<pre class="go">$GOPATH/src/github.com/ziutek/mymysql/examples/simple.go
</pre>

で、編集箇所は、サンプルからユーザーとデータベース名を変更しているので、 

<pre class="brush: golang; title: ; notranslate" title="">user := "mymysqltestuser"
dbname := "mymysqltest"
</pre>

のように変更します。あとは、実行すると、 

<pre class="go">% go run $GOPATH/src/github.com/ziutek/mymysql/examples/simple.go
Connect to tcp:127.0.0.1:3306... OK
Drop A table if exists... OK
Create A table... OK
Insert int A... OK
Select from A...
Row: 0
name:  ''         &lt;nil>}
number: 0          {&lt;nil>}
Row: 1
name:  '1*10= 10' []byte{0x31, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x31, 0x30}}
number: 100        {[]byte{0x31, 0x30, 0x30}}
Row: 2
name:  '2*10= 20' []byte{0x32, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x32, 0x30}}
number: 200        {[]byte{0x32, 0x30, 0x30}}
Row: 3
name:  '3*10= 30' []byte{0x33, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x33, 0x30}}
number: 300        {[]byte{0x33, 0x30, 0x30}}
Row: 4
name:  '4*10= 40' []byte{0x34, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x34, 0x30}}
number: 400        {[]byte{0x34, 0x30, 0x30}}
Row: 5
name:  ''         &lt;nil>}
number: 0          {&lt;nil>}
Row: 6
name:  '6*10= 60' []byte{0x36, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x36, 0x30}}
number: 600        {[]byte{0x36, 0x30, 0x30}}
Row: 7
name:  '7*10= 70' []byte{0x37, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x37, 0x30}}
number: 700        {[]byte{0x37, 0x30, 0x30}}
Row: 8
name:  '8*10= 80' []byte{0x38, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x38, 0x30}}
number: 800        {[]byte{0x38, 0x30, 0x30}}
Row: 9
name:  '9*10= 90' []byte{0x39, 0x2a, 0x31, 0x30, 0x3d, 0x20, 0x39, 0x30}}
number: 900        {[]byte{0x39, 0x30, 0x30}}
Remove A... OK
Close connection... OK
</pre>

という感じになります。`db.Query("drop table A")`を実行している箇所をコメントアウトして、
  
mysql側で`select * from A;`とした結果は、 

<pre class="go">mysql> select * from A;
+----------+--------+
| name     | number |
+----------+--------+
| NULL     |   NULL |
| 1*10= 10 |    100 |
| 2*10= 20 |    200 |
| 3*10= 30 |    300 |
| 4*10= 40 |    400 |
| NULL     |   NULL |
| 6*10= 60 |    600 |
| 7*10= 70 |    700 |
| 8*10= 80 |    800 |
| 9*10= 90 |    900 |
+----------+--------+
10 rows in set (0.00 sec)
</pre>

となっていたので、golangからmysqlへアクセスしていることが分かったと思います。

 [1]: http://code.google.com/p/go-wiki/source/browse/SQLDrivers.wiki?repo=wiki
 [2]: https://github.com/ziutek/mymysql