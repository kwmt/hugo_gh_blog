---
title: '#python でPILを使って、jpegファイルを扱った時に「decoder jpeg not available」というエラーが出た場合の対処法'
author: kwmt
layout: post
date: 2013-07-13
url: /index.php/2013/07/14/python-pil-error-decoder-jpeg-not-available/
pdrp_attributionLocation:
  - end
categories:
  - python
tags:
  - PIL
  - python

---
環境：Mac OS X 10.7.5

## 問題 {.section}

PILを使って、jpgファイルをオープンして、オープンしたファイルに対してアクセスしようとすると（今回の場合、getpixelの部分）、下記のような「decoder jpeg not available」というエラーがでる。

<pre class="go">% python hoge.py
Traceback (most recent call last):
File "hoge.py", line 9, in
p= img.getpixel((x,y))
File "/Library/Python/2.7/site-packages/PIL/Image.py", line 949, in getpixel
self.load()
File "/Library/Python/2.7/site-packages/PIL/ImageFile.py", line 189, in load
d = Image._getdecoder(self.mode, d, a, self.decoderconfig)
File "/Library/Python/2.7/site-packages/PIL/Image.py", line 385, in _getdecoder
raise IOError("decoder %s not available" % decoder_name)
IOError: decoder jpeg not available
</pre>

## 確認 {.section}

libjpegが入ってないから？ということをtwitterで教えて頂き、インストールされているか確認したところ入ってなさげ。

<pre class="go">portからインストールしてた場合
% port installed | grep libjpeg
%

ソースからインストールしていた場合
% ll /usr/local/lib/ | grep jpeg
%
</pre>

## 対策 {.section}

  1. libjpegをソースからインストールする
<pre class="go"><a href="http://www.ijg.org/files/jpegsrc.v8c.tar.gz">http://www.ijg.org/files/jpegsrc.v8c.tar.gz</a>をダウンロードして解凍
% ./configure
% make
% sudo make install
</pre>

  2. PILをインストールしなおす
<pre class="go">% sudo pip uninstall pil
% sudo pip install pil
</pre>

  3. 最初の問題が起こったpythonプログラムを再実行
エラーが出なくなった。

## 参考 {.section}

Python Imaging Library (PIL) Decoder jpeg not available — how to fix?
:   <a href="http://apple.stackexchange.com/questions/59718/python-imaging-library-pil-decoder-jpeg-not-available-how-to-fix/59719#59719" target="_blank">http://apple.stackexchange.com/questions/59718/python-imaging-library-pil-decoder-jpeg-not-available-how-to-fix/59719#59719</a>