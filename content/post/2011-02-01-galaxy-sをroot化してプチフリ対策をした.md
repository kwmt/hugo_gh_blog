---
title: Galaxy Sをroot化してプチフリ対策をした
author: kwmt
layout: post
date: 2011-02-01
url: /index.php/2011/02/01/galaxy-sをroot化してプチフリ対策をした/
categories:
  - Android

---
<a href="http://komugi.net/archives/2010/10/28225019" target="_blank">Galaxy S SC-02B root取得からリカバリまでhttp://komugi.net/archives/2010/10/28225019</a>
  
上記サイトを参考に、Galaxy Sをroot化してプチフリ対策をした。
  
この記事は完全に、自分もやったという記録です。（細かいやり方などは書いてません。独り言です。）
  
自分がやった順番で、上記サイトで少し分からなかったことを付け加えながら書いてみよう。
  
あ、root化するとDocomoからのサポートが受けられなくなるらしいので、覚悟を決めて。

* * *1.Galaxy Sのドライバインストール（kies)→これはすでに済み


  
2.「SuperOneClick」をPCにインストールし、PCで作業。
  
これで、suコマンドとSuperuserPermissionsをGalaxy Sに入れてくれる。
  
3．Galaxy Sを再起動
  
ここまででroot化は終了。</p> 

* * *ここからはプチフリ対策。以下はすべてGalaxy S側で操作。


  
4.「Quadrant Standard」アプリで、プチフリ対策前のベンチマークをした。
  
<img
src="http://androg.up.seesaa.net/image/pre_take_measures-thumbnail2.png" width="192" height="320" border="0" align="" alt="プチフリ対策前"
pbsrc="http://androg.up.seesaa.net/image/pre_take_measures.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
ふむふむ。まぁこんなものか。
  
5．「BusyBox」アプリをマーケットで検索しインストール。
  
このアプリを起動すると、Toastで「スーパーユーザー権限を取得しました」と出て、System dataを集めているのでしばらく待つ。
  
（Galaxsy Sを再起動せずにBusyBoxを起動したら夜ずっとPlease Wait&#8230;の状態だった。。）
  
BusyBox下部のInstallをタップすると、いろいろなツールがコピーされる。
  
※これは、あとでTitaniumBackupするときに必要になるもの。（もしかしたらOCLFでも必要かも。BusyBoxをインストールせずにTitanimuBackupを起動したら、BusyBoxが必要ですと怒られたので。）
  
6．「TitaniumBackup」をマーケットで検索、インストールして、プチフリ対策アプリを実施する前に、バックアップした。
  
全部バックアップするには、
  
アプリ起動→Menu→バッチ処理→リストの上から4つ目の「バックアップ：全ユーザーアプリ＋システムデータ」の実行をタップしてしばらく待つ。（205項目で約10分ちょいぐらいだった。まぁ入れてるアプリによると思いますが。）
  
7．「One Click Lag Fix」はメインのプチフリ対策アプリ。
  
マーケットで「One Click Lag Fix」と検索すると、「RyanZA&#8217;s OCLF 2.0」が出てくるがこれでOK。
  
OCLF=One Click Lag Fixの略みたい
  
僕はOCLFのバージョンは2.3.0を使用
  
バックアップ完了後、上記サイトのとおり、SDカードを抜いて、「Install EXT2 Tools」完了後、「OneClickLagFix V2.2+」を実行。
  
Ext Sizeは870MBぐらいにした。
  
プチフリ対策はここまで。
  
8.「Quadrant Standard」アプリで、プチフリ対策後のベンチマークをした。
  
<img
src="http://androg.up.seesaa.net/image/after_take_measures-thumbnail2.png" width="192" height="320" border="0" align="" alt="プチフリ対策後"
pbsrc="http://androg.up.seesaa.net/image/after_take_measures.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />
  
お～、感動！！
  
最後になりましたが、コムギドットネットさんありがとうございました。</p>