---
title: Dart FLIGHT SCHOOL Kyoto に行ってきた
author: kwmt
layout: post
date: 2014-04-29
url: /index.php/2014/04/29/dart-flight-school-kyoto/
pdrp_attributionLocation:
  - end
categories:
  - Dart
tags:
  - Dart

---
GoogleのDart開発チームからビデオで挨拶

Dartの基本的な文法を話してくれるスピーカーが雪の影響で遅れて、場つなぎの説明があって
  
会場の注意事項とかパフォーマンスの話しとか

型はあるけど、実行時に型情報を捨てている。

## Dartとは {.section}

Googleが開発したプログラミング言語
  
JavaScriptに置き換わるブラウザ組み込みのスクリプト言語を目指して開発された

## 開発環境 {.section}

DartEditor
  
EclipseベースのIDE

DartでHello World

## Dartの動作環境 {.section}

・DartVM搭載のブラウザで直接動作
    
Dart1.1はJSより25％高速
  
・&#8221;dart2js&#8221;でJSに変換して動かす

## Dartの歴史 {.section}

2011年10月Dart発表(early preview)
  
2013年11月SDK1.0正式リリース
  
2014年2月 Dart FLIGHT SCHOOL

Jsはネストが深くなる→Callback Hell→Dartなら深くならない
  
参考：What&#8217;s New in Dart :Youtube

ローカル変数
  
querySelector(xxxxx)

ServerSide Dart
  
サーバーもクライアントもDartで書ける

FlogCはdart2jsになる前のもの。

&#8212;&#8212;
  
前半：文法まわり
  
class Hug{ ←クラスが作れる
   
final num strength;
   
Hug(this.strength); ←コンストラクタ

Hug.bear(); strength =100;

Hug operator +(Hug(other){
     
return new Hug(strength + 

JavascriptはJqueryとかCoffeeスクリプトとかいろんなものを入れてるから、クリーンじゃない。それを解決するために、Dartができた

Dartはthisはthis。javaの感覚で使ってよい

後半：ライブラリなど
  
Pub(package manager for Dart) https://pub.dartlang.org
  
Bootjack is a porting of Twitter Bootstrap 3.0.x in Dart.

DartEditorに統合されており、簡単に利用することができる(yamlに1行かくだけ）

New Fileは意外と賢い。入力したファイルタイプでひながたを作ってくれる。たとえば、a.htmlとかするとhtmlのひな形入りのファイルを作ってくれる

## dart:html {.section}

DOM操作を行うためののライブラリ。
  
HTMLの要素を選択するquerySelectorやInputElementなどのHTMLの要素のクラスが含まれています。

UnkwownElementがなんなのか分からなかった。ドイツ語のページに行っちゃう(´・ω・\`)
  
単一の要素の選択
  
ElementList querySelectorAll
  
&#8220;リストはリスト&#8221;

イベントハンドラ
  
HTML要素のイベントハンドラはStreamで提供されます。
  
Streamとは、連続するデータを扱う枠組みです。dart:asyncのライブラリの紹介時に説明

## dart:io {.section}

ファイル、ソケット、HTTP、プロセスなどをサポートするサーバーアプリケーション向けのライブラリ。
  
ブラウザベースでは動かない

## dart:async {.section}

非同期処理を行うためのライブラリでFutureやStreamなどが含まれる。

Future:
  
将来返る値を受け取るためのオブジェクト。
  
多くのライブラリにおいてDartではCallbackではんく、Futureを利用する

FutureはCompleterがセット。

Stream:
  
連続するデータを受け取る箇所で利用される。
  
HTML要素のonClickはクリックイベントを連続する要素としてみてStreamとして提供されています。

Dartはコールバックを使うのではなくて、FutureとかStreamを使って非同期処理を行う。

Dartはシングルスレッドのモデル。
  
EventQueueの内容をEventLoopが順次実行していく。

## Isolate {.section}

デバッガやStackTraceで見かけるIsolateとはいったい何か？
  
→IsolateはDartの並行処理モデル

・スレッドに似た独立したワーカー
  
・Isolate間でメモリは一切共有しない
  
・ReceivePort,SentPortを利用して文字列のメッセージによる通信でIsolate間は連携を行う。
  
　常に値はコピーされて送信される。

メモリは共有しないため排他制御を行わなくてよい！

使い方
  
Isolate.spawn(entryPoint, receivePort.sendPort)

DartはDOM構築が終わった後にmain()が実行されることを保証している。
  
なので、jsみたいにDOM構築が終わったらという条件判断を入れる必要がない。

privateはライブラリ単位。
  
なので、おなじライブラリの中にあるクラスの中のprivateの値は、そのライブラリのクラスの外からアクセス可能

&#8212;&#8211;
  
Dartのコードラボ
  
http://goo.gl/h5QfGt

AngularDartのコードラボ
  
http://goo.gl/HKVKuv