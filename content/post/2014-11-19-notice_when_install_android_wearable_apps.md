---
title: android wear appのインストール時の注意点
author: kwmt
layout: post
date: 2014-11-19
url: /index.php/2014/11/19/notice_when_install_android_wearable_apps/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - Android
  - android wear

---
開発時（といいますか、デバッグキーを使ってビルドしている時）は、携帯端末とWearable端末に別々にインストールしないと、携帯端末だけにインストールしても自動的にWearable端末にインストールされません。

リリースキーを使って署名している場合は、自動的にインストールされます。

## 参考 {.section}

<a href="https://developer.android.com/training/wearables/apps/creating.html#Install" target="_blank">Install the Wearable App</a>