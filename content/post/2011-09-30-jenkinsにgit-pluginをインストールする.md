---
title: JenkinsにGit Pluginをインストールする
author: kwmt
layout: post
date: 2011-09-30
url: /index.php/2011/09/30/jenkinsにgit-pluginをインストールする/
categories:
  - Jenkins

---
## Git Pluginをイントールする {.section}

TopページのJenkinsの管理　→　プラグインの管理をクリックする。 

下図のようなタブがあり、「アップデート」タブが選択されている状態なので、「利用可能」タブをクリックする。 


<img
src="http://androg.up.seesaa.net/image/JenkinsPlugin_Tab-thumbnail2.png" width="320" height="104" border="0" align="" alt="利用可能タブ"
pbsrc="http://androg.up.seesaa.net/image/JenkinsPlugin_Tab.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" /> 

Gitで検索すると、ソース管理システムの表の中に「Git Plugin」の左側にチェックボックスがあるので、チェックする。 

最下部に「インストール」ボタンがあるので、それをクリックするすると、インストールが始まります。 

Jenkinsを再起動する。（「インストール完了後、ジョブがなければJenkinsを再起動する」にチェックを入れとけば、勝手に再起動してくれる） 

## 確認 {.section}

「新しいジョブを作る」で、「ソースコード管理システム」に初期では、CVS・Subversion・なしの3つだったのが、Gitが追加されていれば、インストール完了 

<img
src="http://androg.up.seesaa.net/image/Jenkins_Gitplugin-thumbnail2.png" width="235" height="110" border="0" align="" alt="Gitの確認"
pbsrc="http://androg.up.seesaa.net/image/Jenkins_Gitplugin.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />