---
title: 'RuntimeError: Couldn&#8217;t determine Berks versionエラーが出たら'
author: kwmt
layout: post
date: 2014-08-06
url: /index.php/2014/08/06/runtimeerror-couldnt-determine-berks-version/
pdrp_attributionLocation:
  - end
fb_author_post_id:
  - 746468085416501
categories:
  - Vagrant
tags:
  - Vagrant

---
下記のパスを環境変数(PATH)に追加する。

<pre class="go">/opt/chefdk/bin
</pre>

以上。

## 参考

<a href="https://github.com/berkshelf/vagrant-berkshelf/issues/212" target="_blank">Could not determine Berks version. v3.0.0</a>