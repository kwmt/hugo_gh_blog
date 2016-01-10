---
title: xcode で controlを押しながら接続出来ない場合の対処方法
author: kwmt
layout: post
date: 2014-10-01
url: /index.php/2014/10/01/cannot-create-connections-in-xcode6-storyboard/
pdrp_attributionLocation:
  - end
categories:
  - iOS
tags:
  - iOS
  - xcode

---
## 現象

TableViewなどのViewをcontrol押しながら、ヘッダーファイルにドラッグしても、うんともすんともないことがある。

## 原因

ViewControllerとクラスが対応付けがされてないため。

## 対処

ViewControllerを選択し、&#8221;Show the identity inspection&#8221;をクリックし、Custom Classで対応付けたいクラスを選択する。