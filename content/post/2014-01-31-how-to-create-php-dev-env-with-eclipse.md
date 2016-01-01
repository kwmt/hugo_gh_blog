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

---
## vagrantのインストール {.section}

参考サイト
  
<http://qiita.com/inouet@github/items/b36638adc2b5772db457>

  1. vagrantインストール
  2. VirtualBox を公式サイトからインストール
※VirtualBoxはインストール済みの為、ここでは作業してない

  3. Boxの追加
<pre class="go">vagrant box add centos64 http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-x86_64-v20130731.box
</pre>

  4. 仮想マシンを作る
<pre class="go">$ mkdir -p ~/Vagrant/CentOS64
$ cd ~/Vagrant/CentOS64
$ vagrant init centos64
</pre>

  5. 仮想マシンを起動する
<pre class="go">$ vagrant up
</pre>

  6. 仮想マシンへ接続
<pre class="go">$ vagrant ssh
</pre>

  7. ネットワークの設定
<pre class="go">$ vim Vagrantfile
# config.vm.network :private_network, ip: "192.168.33.10"
 ↑ ここのコメントアウトを削除
</pre>

  8. vagrant のリロード
<pre class="go">$ vagrant reload
</pre>

これで、192.168.33.10 というIPでアクセスできる

  9. cent os6からは下記をいれておくと何か早くなる設定
<pre class="go">sudo vi /etc/resolv.conf 
options single-request-reopen
</pre>

 10. ファイアーウォールを切る
ローカルの開発環境なので、必要がないため。

<pre class="go">$ sudo service iptables stop
</pre>

再起動したときに有効にならないようにする

<pre class="go">$ sudo chkconfig iptables off
</pre>

Webサーバーが入っているか確認

<pre class="go">$ yum list installed  |grep httpd
</pre>

入っていないので、インストール

<pre class="go">$ sudo yum install -y httpd
</pre>

Webサーバを立ち上げる

<pre class="go">$ sudo service httpd start
</pre>

OSを再起動した後も立ち上がるように設定する

<pre class="go">$ sudo chkconfig httpd on
</pre>

Webサーバーが立ち上がっているかブラウザで確認する
  
デフォルトのIPアドレスhttp://192.168.33.10/にアクセスしてApacheが見えたらOK

FTPファイル転送ツールを使いたい。
  
vagrantユーザーでアクセスできるようにしたい

<pre class="go">$ sudo chown -R vagrant:vagrant /var/www/html/
</pre>

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

<pre class="go">$ yum info php
</pre>

versionが最新ではないので、epelとremiをインストールする

<pre class="go">wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
sudo rpm -Uvh epel-release-6-8.noarch.rpm
sudo rpm -Uvh remi-release-6.rpm
</pre>

epelの設定

<pre class="go">sudo vi /etc/yum.repos.d/epel.repo
enable=1→0に変更
</pre>

これでphpの最新バージョンがインストールできるとこを確認

<pre class="go">yum info --enablerepo=remi php 
</pre>

## PHPインストール {.section}

ようやくphpのインストール(phpに関連するものも)

<pre class="go">sudo yum --enablerepo=remi install -y php php-devel php-mysql php-mbstring php-gd
</pre>

php.iniの設定

<pre class="go">sudo vi /etc/php.ini
</pre>

・error_logを追加

<pre class="go">error_log = /var/log/php.log 
</pre>

・mbstringのlanguageをJapanseで有効化

<pre class="go">;mbstring.language = Japanese
mbstring.language = Japanese
</pre>

・internalをUTF-8に変更と有効化

<pre class="go">;mbstring.internal_encoding = EUC-JP
mbstring.internal_encoding = UTF-8
</pre>

・http_inputはautoで有効化

<pre class="go">;mbstring.http_input = auto
mbstring.http_input = auto
</pre>

・detect_orderはautoで有効化

<pre class="go">;mbstring.detect_order = auto
mbstring.detect_order = auto
</pre>

・expose_php = OnをOffに

<pre class="go">expose_php = On
expose_php = Off
</pre>

・timezoneをAsia/Tokyoにして有効化

<pre class="go">;date.timezone = 
date.timezone = Asia/Tokyo
</pre>

以上の設定を有効にするには、httpdを再起動する

<pre class="go">sudo service httpd restart
</pre>

## MySQLをインストール {.section}

<pre class="go">$ sudo yum install -y --enablerepo=remi mysql-server
</pre>

設定

<pre class="go">$ sudo vi /etc/my.cnf
</pre>

<pre class="go">[mysqld]
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
</pre>

MySQLを起動

<pre class="go">$ sudo service mysqld start
</pre>

mysqld起動時にfailedして、/var/log/mysqld.logに下記が出ていたら

<pre class="go">140706  7:06:08 [Note] Plugin 'FEDERATED' is disabled.
/usr/libexec/mysqld: Table 'mysql.plugin' doesn't exist
140706  7:06:08 [ERROR] Can't open the mysql.plugin table. Please run mysql_upgrade to create it.
</pre>

<pre class="go">$ sudo mysql_install_db
</pre>

を実行する

これで立ち上がるが、いろいろ設定する必要がある（書いてある）

<pre class="go">$ /usr/bin/mysql_secure_installation
</pre>

を実行する

<pre class="go">Enter current password for root (enter for none):enter
Set root password? [Y/n] Y
パスワードを適当に設定
そのほかの設定はデフォルトのEnterで。
</pre>

再起動時もスタートするように設定

<pre class="go">$ sudo chkconfig mysqld on
</pre>

確認

<pre class="go">$ mysql -u root -p
</pre>

さっき設定したパスワード
  
Server version: 5.5.36 MySQL Community Server (GPL) by Remi
  
と5.5がインストールされていることを確認

## cakephpからMySQLを使えるように設定する {.section}

データベースを作成する

<pre class="go">$ mysql -u root -p
mysql> create database dev_cakephp;
mysql> grant all on dev_cakephp.* to dbuser@localhost identified by 'xxxxxxxxxx';
</pre>

これらの設定でcakephpを設定する

<pre class="go">% cd app/Config/
% cp database.php.default database.php
</pre>

以下のlogin,password,databaseを変更する。

<pre class="go">class DATABASE_CONFIG {

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
</pre>

DocumentRootにアップロードしたら、ブラウザを更新し、
  
CakePHP is able to connect to the database.
  
となったらOK。

 [1]: http://dotinstall.com/lessons/basic_local_development_v2/24805