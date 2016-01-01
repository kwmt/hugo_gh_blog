---
title: DDMSのEmulation Control:Speedの意味
author: kwmt
layout: post
date: 2010-09-06
url: /index.php/2010/09/06/ddmsのemulation-controlspeedの意味/
categories:
  - Android
tags:
  - Android

---
DDMSのEmulation Controlでネットワークスピードを変更しながらデバッグできるが、それぞれがどういう関係で、どういう意味かわからなかったので、簡単にまとめてみた。
  
基本的には本家サイト[Android Emulator][1]に書かれてある。
  
GSM　＜　HSCSD　＜　GPRS　＜　EDGE　＜　UMTS　＜　HSDPA
  
FULL は制限なし

<ins><b>GSM(Global System for Mobile Communications)</b></ins>
:   FDD-TDMA方式で実現されている第二世代携帯電話(2G)規格(Up: 14.4 , down: 14.4)

<ins><b>HSCSD(High Speed Circuit Switch Data)</b></ins>
:   GSMの高速回線交換データ通信(Up: 14.4, down: 43.2)

<ins><b>GPRS(General Packet Radio Service)</b></ins>
:   GSMのパケットデータ通信(Up: 40.0, down: 80.0)

<ins><b>EDGE(Enhanced Data Rates for GSM Evolution)</b></ins>
:   GRPSを拡張したパケット通信規格(Up: 118.4, down: 236.8)

<ins><b>UMTS(Universal Mobile Telecommunications System)</b></ins>
　
:   第3世代(3G)移動通信テクノロジーの1つで、W-CDMAを使う(Up: 128.0, down: 1920.0)

<ins><b>HSDPA(High Speed Downlink Packet Access)</b></ins>
:   W-CDMAを拡張した高速パケット通信規格(3.5G)(Up: 348.0, down: 14400.0)

※()は[Android Emulatorからの引用][1]で、数字の単位はkilobits/sec
  
※日本語の説明は、wikipediaからの引用

 [1]: http://developer.android.com/guide/developing/tools/emulator.html#netspeed