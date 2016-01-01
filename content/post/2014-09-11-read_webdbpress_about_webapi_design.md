---
title: WEB+DB PRESS Vol.82 の「Web APIデザインの鉄則」特集を読んだ。
author: kwmt
layout: post
date: 2014-09-11
url: /index.php/2014/09/11/read_webdbpress_about_webapi_design/
pdrp_attributionLocation:
  - end
categories:
  - 日記
tags:
  - JSON Schema
  - Plant UML
  - Web API

---
<img src="http://kwmt27.net/wp-content/uploads/2014/09/20140908_103353-e1410424730181-225x300.jpg" alt="WEB+DB PRESS Vol.82" width="225" height="300" class="alignleft size-thumbnail wp-image-1288" srcset="http://kwmt27.net/wp-content/uploads/2014/09/20140908_103353-e1410424730181-225x300.jpg 225w, http://kwmt27.net/wp-content/uploads/2014/09/20140908_103353-e1410424730181-768x1024.jpg 768w" sizes="(max-width: 225px) 100vw, 225px" />

<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4774166278/kwmt27-22/ref=nosim/" name="amazletlink" target="_blank">WEB+DB PRESS Vol.82</a>を買いました。Go言語が載ってるからという理由で買いましたが、「Web APIデザインの鉄則」という特集があって、おもしろそうなのでよんでみました。 

<!--more-->

## 第1章 Web APIの基礎知識 {.section}

この特集ではメッセージ形式をJSONとするRESTful APIの設計について書かれていますが、REST以外のスタイルにRPCなどもあるのに、なぜRESTful APIにするのかを、基礎から丁寧に書いてくれています。 

ちなみに、RESTfulなWeb APIをここでは、ROA(Resource Oriented Architecture)に沿って設計することと定義しています。 

## 第2章 リソース設計 {.section}

「Eメール送信を行うためのAPI」を題材としています。その題材を元に、リソースに含まれるフィールドをどのように定義するか、その定義をどうやって明文化するかが書かれています。おすすめは、<a href="http://json-schema.org/" target="_blank">JSON Schema</a>という手法を用いることだとそうです。JSON Schemaを使いながら、題材に必要なリソースを定義していっています。 

語彙を決めるのには先人の知恵（[schema.org][1]や <a href ="http://www.opensearch.org/Specifications/OpenSearch/1.1" target="_blank">OpenSeacrh</a>) などを参考にするとよいとのことです。 

## 第3章 インターフェース設計 {.section}

ここでは、エンドポイントURI・HTTPメソッド・クエリパラメータを定めていきます。第2章で作成したJSON Shemaによる定義に追加していきます。 

「新しいリソースを作成するエンドポイント」や「すでに存在するリソースを削除するエンドポイント」のそれぞれに対して、どのような要件で作成するかが解説されています。「HTTPメソッドの選び方」も詳しく書いてあります。 

## 第4章 エラー表現とドキュメント {.section}

「エラーがなぜ起こったのか」「どのようにすればそれを解決できるか」を機械がわかるような仕組みにするために、エラー表現をどのように決めていくかについて説明されています。 

また、文書だけでは説明できない部分は図を書いたりしますが、その図を作成する<a href="http://plantuml.sourceforge.net/" target="_blank">Plant UML</a>というツールを紹介しています。 

## 感想 {.section}

Web APIの設計について非常に勉強になりました。知らないツールなどもあったので、積極的につかっていきたいと思う。 

<div class="amazlet-box" style="margin-bottom:0px;">
  <div class="amazlet-image" style="float:left;margin:0px 12px 1px 0px;">
    <a href="http://www.amazon.co.jp/exec/obidos/ASIN/4774166278/kwmt27-22/ref=nosim/" name="amazletlink" target="_blank"><img src="http://ecx.images-amazon.com/images/I/61gSqezYgPL._SL160_.jpg" alt="WEB+DB PRESS Vol.82" style="border: none;" /></a>
  </div>
  
  <div class="amazlet-info" style="line-height:120%; margin-bottom: 10px">
    <div class="amazlet-name" style="margin-bottom:10px;line-height:120%">
      <a href="http://www.amazon.co.jp/exec/obidos/ASIN/4774166278/kwmt27-22/ref=nosim/" name="amazletlink" target="_blank">WEB+DB PRESS Vol.82</a> 
      
      <div class="amazlet-powered-date" style="font-size:80%;margin-top:5px;line-height:120%">
        posted with <a href="http://www.amazlet.com/" title="amazlet" target="_blank">amazlet</a> at 14.09.11
      </div>
    </div>
    
    <div class="amazlet-detail">
      山口 徹 Jxck 佐々木 大輔 横路 隆 加来 純一 山本 伶 大平 武志 米川 健一 坂本 登史文 若原 祥正 和久田 龍 平栗 遵宜 伊藤 直也 佐藤 太一 高橋 俊幸 海野 弘成 五嶋 壮晃 佐藤 歩 吉村 総一郎 橋本 翔 舘野 祐一 中島 聡 渡邊 恵太 はまちや2 竹原 河合 宜文 <br />技術評論社 <br />売り上げランキング: 663
    </div>
    
    <div class="amazlet-sub-info" style="float: left;">
      <div class="amazlet-link" style="margin-top: 5px">
        <a href="http://www.amazon.co.jp/exec/obidos/ASIN/4774166278/kwmt27-22/ref=nosim/" name="amazletlink" target="_blank">Amazon.co.jpで詳細を見る</a>
      </div>
    </div>
  </div>
  
  <div class="amazlet-footer" style="clear: left">
  </div>
</div>

 [1]: http://schema.org/Person