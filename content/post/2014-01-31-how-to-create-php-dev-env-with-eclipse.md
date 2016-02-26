---
title: VagrantでPHP開発環境構築方法
author: kwmt
layout: post
date: 2014-01-31
url: /index.php/2014/01/31/how-to-create-php-dev-env-with-eclipse/
pdrp_attributionLocation:
  - end
categories:
  - PHP
  - Vagrant
tags:
  - PHP
  - Vagrant

---
## vagrantのインストール

参考サイト
  
<http://qiita.com/inouet@github/items/b36638adc2b5772db457>

  1.vagrantインストール
  2.VirtualBox を公式サイトからインストール
※VirtualBoxはインストール済みの為、ここでは作業してない

  3.Boxの追加

```
vagrant box add centos64 http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-x86_64-v20130731.box
```

  4.仮想マシンを作る

```
$ mkdir -p ~/Vagrant/CentOS64
$ cd ~/Vagrant/CentOS64
$ vagrant init centos64
```

  5.仮想マシンを起動する

```
$ vagrant up
```

  6.仮想マシンへ接続

```
$ vagrant ssh
```

  7.ネットワークの設定

```
$ vim Vagrantfile
# config.vm.network :private_network, ip: "192.168.33.10"
 ↑ ここのコメントアウトを削除
```

  8.vagrant のリロード

```
$ vagrant reload
```

これで、192.168.33.10 というIPでアクセスできる

  9.cent os6からは下記をいれておくと何か早くなる設定

```
sudo vi /etc/resolv.conf 
options single-request-reopen
```

 10.ファイアーウォールを切る
ローカルの開発環境なので、必要がないため。

```
$ sudo service iptables stop
```

再起動したときに有効にならないようにする

```
$ sudo chkconfig iptables off
```

Webサーバーが入っているか確認

```
$ yum list installed  |grep httpd
```

入っていないので、インストール

```
$ sudo yum install -y httpd
```

Webサーバを立ち上げる

```
$ sudo service httpd start
```

OSを再起動した後も立ち上がるように設定する

```
$ sudo chkconfig httpd on
```

Webサーバーが立ち上がっているかブラウザで確認する
  
デフォルトのIPアドレスhttp://192.168.33.10/にアクセスしてApacheが見えたらOK

FTPファイル転送ツールを使いたい。
  
vagrantユーザーでアクセスできるようにしたい

```
$ sudo chown -R vagrant:vagrant /var/www/html/
```

くわしくはこちら
  
[http://dotinstall.com/lessons/basic\_local\_development_v2/24805][1]

Hostsファイルを編集することで、IPアドレスを文字で表せるようにする
  
Hosterというツールを使った
  
セット名:hoge
  
ホスト名：dev.hoge.com
  
IPアドレス:192.168.33.10
  
有効化する

PhpStormからサーバーに接続
  
Tools > Deployment > ConfigrationからSFTPの設定をして、
  
Tools > Deployment > Browse Remote Hostとすると、
  
右側にサーバー側のツリーが表示される。

PHPをインストール

```
$ yum info php
```

versionが最新ではないので、epelとremiをインストールする

```
wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
sudo rpm -Uvh epel-release-6-8.noarch.rpm
sudo rpm -Uvh remi-release-6.rpm
```

epelの設定


```
sudo vi /etc/yum.repos.d/epel.repo
enable=1→0に変更
```

これでphpの最新バージョンがインストールできるとこを確認

```
yum info --enablerepo=remi php 
```

## PHPインストール

ようやくphpのインストール(phpに関連するものも)

```
sudo yum --enablerepo=remi install -y php php-devel php-mysql php-mbstring php-gd
```

php.iniの設定

```
sudo vi /etc/php.ini
```

・error_logを追加

```
error_log = /var/log/php.log 
```

・mbstringのlanguageをJapanseで有効化

```
;mbstring.language = Japanese
mbstring.language = Japanese
```

・internalをUTF-8に変更と有効化

```
;mbstring.internal_encoding = EUC-JP
mbstring.internal_encoding = UTF-8
```

・http_inputはautoで有効化

```
;mbstring.http_input = auto
mbstring.http_input = auto
```

・detect_orderはautoで有効化

```
;mbstring.detect_order = auto
mbstring.detect_order = auto
```

・expose_php = OnをOffに

```
expose_php = On
expose_php = Off
```

・timezoneをAsia/Tokyoにして有効化

```
;date.timezone = 
date.timezone = Asia/Tokyo
```

以上の設定を有効にするには、httpdを再起動する

```
sudo service httpd restart
```

## MySQLをインストール

```
$ sudo yum install -y --enablerepo=remi mysql-server
```

設定

```
$ sudo vi /etc/my.cnf
```

```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

#下の部分を追加
character_set_server=utf8
default-storage-engine=InnoDB
innodb_file_per_table
[mysql]
default-character-set=utf8
[mysqldump]
default-character-set=utf8
```

MySQLを起動

```
$ sudo service mysqld start
```

mysqld起動時にfailedして、/var/log/mysqld.logに下記が出ていたら

```
140706  7:06:08 [Note] Plugin 'FEDERATED' is disabled.
/usr/libexec/mysqld: Table 'mysql.plugin' doesn't exist
140706  7:06:08 [ERROR] Can't open the mysql.plugin table. Please run mysql_upgrade to create it.
```

```
$ sudo mysql_install_db
```

を実行する

これで立ち上がるが、いろいろ設定する必要がある（書いてある）

```
$ /usr/bin/mysql_secure_installation
```

を実行する

```
Enter current password for root (enter for none):enter
Set root password? [Y/n] Y
パスワードを適当に設定
そのほかの設定はデフォルトのEnterで。
```

再起動時もスタートするように設定

```
$ sudo chkconfig mysqld on
```

確認

```
$ mysql -u root -p
```

さっき設定したパスワード
  
Server version: 5.5.36 MySQL Community Server (GPL) by Remi
  
と5.5がインストールされていることを確認

## cakephpからMySQLを使えるように設定する

データベースを作成する

```
$ mysql -u root -p
mysql> create database dev_cakephp;
mysql> grant all on dev_cakephp.* to dbuser@localhost identified by 'xxxxxxxxxx';
```

これらの設定でcakephpを設定する

```
% cd app/Config/
% cp database.php.default database.php
```

以下のlogin,password,databaseを変更する。

```
class DATABASE_CONFIG {

	public $default = array(
		'datasource' => 'Database/Mysql',
		'persistent' => false,
		'host' => 'localhost',
		'login' => 'dbuser',
		'password' => 'xxxxxxxxxx',
		'database' => 'dev_cakephp',
		'prefix' => '',
		//'encoding' => 'utf8',
	);
```

DocumentRootにアップロードしたら、ブラウザを更新し、
  
CakePHP is able to connect to the database.
  
となったらOK。

 [1]: http://dotinstall.com/lessons/basic_local_development_v2/24805