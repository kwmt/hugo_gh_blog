---
title: Android StudiodでgreenDaoを使う
author: kwmt
layout: post
date: 2014-09-17
url: /index.php/2014/09/17/how-to-use-greendao-library/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - Android
  - Android Studio
  - greenDao

---
## はじめに

AndroidのORMライブラリについて調べてみてまして、ORMLite、Active Android、greenDaoが有名のようです。

ORMLite、Active Androidは、アノテーションを使っていて、greenDaoは使っていません。
  
アノテーションはリフレクションに依存しているため、特にAndroidデバイスにおいては、パフォーマンスに影響出ますので、greenDaoを使おうかと思います。([参考1][1]などをみますと、パフォーマンスにクリティカルに影響があるアプリでなければどれつかっても良さそうですが・・・）

## 使い方としては、

２ステップ必要で、１ステップ目は、モデル定義しgreendao-generatorでコードを自動生成します。(普通のjava)
  
２ステップ目として、自動生成したコードをAndroidに組み込みます。

それで、自動生成するために新規にjavaプロジェクトを作る必要があると書いてある参考サイトが多くて、確かにそれでもいいのですが、別プロジェクトを作るのが面倒くさいし、プロジェクトが別なので管理がバラバラになりそうなので、なんとかしてのプロジェクトで出来ないか調べていた所、ちょ～分かりやすく書いて下さっていたサイトがありましたので、リンク張らせて頂きます。
  
<!--more-->


  
<a href="http://bit.ly/1yf9A5G" target="_blank">Android Studioでgradle taskを使ってgreenDaoのDao生成を行う &#8211; きょこみのーと</a>

ほとんど上記で、Android Studioのバージョンが0.8時に、リポジトリがmavenCentral()からjcenter()に変更になったことと、自動生成の出力先(上記のサイトではdaoOutputDirPath)に注意することぐらいでしょうか。

あとは、ココらへんを見たりしてgreenDaoの使い方を確認すると良いと思います。
  
<a href="http://bit.ly/YRdS3A" target="_blank">Documentation | greenDAO – Android ORM for SQLite</a> 

## 参考

<li id="sanko">
  <a href="http://bit.ly/1wBwKBn" target="_blank">Androidで使えるOR Mapper: ORMLite « LINE Engineers&#8217; Blog</a>
</li>
  1. <a href="http://bit.ly/Xzc3r0" target="_blank">Non-technical FAQ | greenDAO – Android ORM for SQLite</a>
  2. <a href="http://bit.ly/XzcGki" target="_blank">ormlite &#8211; Green DAO vs ORM lite vs Active Android &#8211; Stack Overflow</a>
  3. <a href="http://bit.ly/YRe8zz" target="_blank">[Android Tips] ActiveAndroid を使って ActiveRecord ライクに SQLite を操作する ｜ Developers.IO</a> 
      * <a href="http://www.amazon.co.jp/exec/obidos/ASIN/4798040029/kwmt27-22/ref=nosim/" name="amazletlink" target="_blank">Androidオープンソースライブラリ徹底活用(書籍)</a></ol>

 [1]: #sanko