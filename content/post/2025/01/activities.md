+++
categories = ["業務外活動"]
date = "2025-01-05T18:50:00+09:00"
tags = ["業務外活動"]
title = "業務外活動"
draft = false
toc = true
slug = ""
author = "kwmt27"
+++


## OSS貢献
- Flutter
  - [Removed `then` by kwmt · Pull Request #22346 · flutter/flutter](https://github.com/flutter/flutter/pull/22346)
  - [Fixed typo `BarcodeValueType` by kwmt · Pull Request #936 · flutter/plugins](https://github.com/flutter/plugins/pull/936)
  - [Fixed fcm crash by kwmt · Pull Request #651 · flutter/plugins](https://github.com/flutter/plugins/pull/651)
  - [Fixed typo REVERSED_CLIENT_ID by kwmt · Pull Request #634 · flutter/plugins](https://github.com/flutter/plugins/pull/634)
  - [[google_maps_flutter] : Scroll down the long list containing the map to the bottom once and return to the map position, the screen flickers. · Issue #26512 · flutter/flutter](https://github.com/flutter/flutter/issues/26512)
  - [Unable to get provider com.google.android.gms.ads.MobileAdsInitProvider: java.lang.IllegalStateException: · Issue #23102 · flutter/flutter](https://github.com/flutter/flutter/issues/23102)
- flutter_inapp_purchase
  - [Created unit test](https://github.com/dooboolab-community/flutter_inapp_purchase/pull/69)
  - [Improved getPurchaseHistory's speed 44% faster](https://github.com/dooboolab-community/flutter_inapp_purchase/pull/68)
  - [Convert list-base to enum](https://github.com/dooboolab-community/flutter_inapp_purchase/pull/67)
- DroidKaigi
  - conference-app-2023
    - [moved StaffList to section package](https://github.com/DroidKaigi/conference-app-2023/pull/730)
  - conference-app-2022
    - [added twitter compose rules with detekt](https://github.com/DroidKaigi/conference-app-2022/pull/464)
    - [Add a yml file for workflow_run trigger](https://github.com/DroidKaigi/conference-app-2022/pull/534)
  - conference-app-2020
    - [I think no need to hold and publish fragment field. by kwmt · Pull Request #374 · DroidKaigi/conference-app-2020](https://github.com/DroidKaigi/conference-app-2020/pull/374)
    - [Fixed status bar color not updating when Darktheme off by kwmt · Pull Request #256 · DroidKaigi/conference-app-2020](https://github.com/DroidKaigi/conference-app-2020/pull/256)
  - conference-app-2019
  
    - [fix typo by kwmt · Pull Request #791 · DroidKaigi/conference-app-2019](https://github.com/DroidKaigi/conference-app-2019/pull/791)
    - [Bumped Hyperion-Android 0.9.25 ( Fix for SecurityException on API 28) by kwmt · Pull Request #491 · DroidKaigi/conference-app-2019](https://github.com/DroidKaigi/conference-app-2019/pull/491)
  - conference-app-2018  
    - [Changed SwipeRefesh color by kwmt · Pull Request #618 · DroidKaigi/conference-app-2018](https://github.com/DroidKaigi/conference-app-2018/pull/618)
    - [Replaced ci build url to CircleCI environment variable(CIRCLE_BUILD_URL) by kwmt · Pull Request #334 · DroidKaigi/conference-app-2018](https://github.com/DroidKaigi/conference-app-2018/pull/334)
    - [Fixed there is no need to display day header view by kwmt · Pull Request #174 · DroidKaigi/conference-app-2018](https://github.com/DroidKaigi/conference-app-2018/pull/174)
    
  - conference-app-2017
    - [Fixed because NPE occurred, when activity is recreated. by kwmt · Pull Request #427 · DroidKaigi/conference-app-2017](https://github.com/DroidKaigi/conference-app-2017/pull/427)
    - [Fixed speaker image icon on mysession by kwmt · Pull Request #411 · DroidKaigi/conference-app-2017](https://github.com/DroidKaigi/conference-app-2017/pull/411)
    - [Create My Session List Page by kwmt · Pull Request #343 · DroidKaigi/conference-app-2017](https://github.com/DroidKaigi/conference-app-2017/pull/343)
    - [fix typo by kwmt · Pull Request #335 · DroidKaigi/conference-app-2017](https://github.com/DroidKaigi/conference-app-2017/pull/335)
    - [add reload when comming back from session detail by kwmt · Pull Request #276 · DroidKaigi/conference-app-2017](https://github.com/DroidKaigi/conference-app-2017/pull/276)

## 社内・個人作品
- Androidアプリ [QRCodeReader](https://play.google.com/store/apps/details?id=net.kwmt27.qrcode_reader)
  - 2019/05リリースし現在も継続的にアップデート。
  - 初マルチモジュール、gradleのkts化、DarkTheme対応などにチャレンジ。現在はJetpack Composeに置き換え中。
- チャットアプリインコネ [Androidアプリ](https://play.google.com/store/apps/details?id=com.instantonnection.app), iOSアプリ
  - 2018/07〜2018/11(Android版リリース)、2019/03(iOS版リリース)
  - Flutter製のチャットアプリ。アプリ内課金にチャレンジした。[GitHub](https://github.com/kwmt/flutter-inconne)で公開中。
- slackbot ランチボット
  - 2017/07ごろリリース
  - 「今日のランチ」などとslackにポストすると、近辺のランチを1件教えてくれるslackボットを作成
  - 技術：Node.js,lambda
- LINE bot LINEボットどこいこ
  - 「本町　焼肉」などとLINEに送ると、5件カルーセル形式で教えてくれるボットを作成
  - 技術：Go,Heroku
- slackbot オフィスオープンボット
  - 2016/11ごろリリース
  - 紹介ブログ：https://www.tech-vein.com/blog/iot/office-open-bot
  - オフィスのセキュリティを解除すると、ランプのON/OFFを検知して、slackに通知するボットを作成
  - 技術：ラズパイ,Go
  - ラズパイからボットまで1人。slackに通知するのはSlack APIのラッパーを書いて使用している
- Androidアプリ [CodeSearch for GitHub](https://play.google.com/store/apps/details?id=net.kwmt27.codesearch)
  - 2016/10リリース
  - RxJavaの練習用アプリ。GitHubAPIを使いアプリ上で検索可能にする。[GitHub](https://github.com/kwmt/GitHubSearch)で公開中。
- Androidアプリ [BookmarkShortcut](https://play.google.com/store/apps/details?id=net.androg.bookmarkshortcut)
  - 2011/08ごろリリース。
  - ブックマークを開くのにいちいちブラウザを開かないとブックマークが開けなかったので、起動するだけで表示されるアプリ。
  - ブラウザアプリが使っているデータベース構造を解析して実装。
- Androidアプリ DispDist
  - 2010/08リリース。
  - 特定の場所を登録した状態でアプリを起動すると登録した場所と自分の場所の距離を知ることができる
  - ブログ：https://kwmt27.net/index.php/2010/08/11/dispdist%E6%A6%82%E8%A6%81/
  - JavaもAndroidも初めて触って初のAndroidアプリをリリースし、アンドロイダーというレビューサイトに掲載される。世界ターゲットだったため、距離計算は地球が楕円として厳密に実装したりなどした。

## 登壇・ブログなど
### スライド
- [Speaker Deck](https://speakerdeck.com/yasi)
- [slideshare](https://www.slideshare.net/yasi_life/presentations)
- [Goのライブラリを公開する方法](https://go-talks.appspot.com/github.com/kwmt/go-talks/2017/souda-kyoto-go.slide#1)
- [AndroidTVハンズオン勉強会](https://docs.google.com/forms/d/e/1FAIpQLSeYOONFpk4Vk5Gyi1A4-lf0AlpqQYpdelJzO1j8ggGkUBDbKQ/viewform?c=0&w=1)
- 2011/03 日本Androidの会 神戸支部でAndroid WebViewに関して発表（ここが社外初プレゼン）
- 2023/06/24 Potatotips#82 https://twitter.com/yasi_kawamoto/status/1672451093715116033?s=20
- 2023/07/14 Shibuya.apk#43 https://twitter.com/yasi_kawamoto/status/1679816067785572355?s=20

### ブログ
- [ブログ](https://kwmt27.net/)
- [zenn](https://zenn.dev/yasi)
- [note](https://note.com/kyasi)


## 勉強会
### 主催
- 2019/09〜2020/01 [Android研究&発表会](https://arap.connpass.com/)
- 2017/07〜2018/01 [モバもく会](https://mobamoku.connpass.com/) 
- 2016/12 [[大阪]Goモク会](https://connpass.com/event/13696/)
- 2015/05 [UIKit Frameworkを読む会](https://connpass.com/event/14386/)
- 2013/04 Go言語とProject Euler勉強会(4名)
### 参加
- 2010/09〜2023/08までの参加回数 250回

## 受賞歴
- 2019/03 [SDLコンテストにてグランプリを受賞](https://car.watch.impress.co.jp/docs/news/1173850.html)
  - チームInstarideでSDLとAndroidアプリ連携を担当
- 2013/04 [Startup Weekend Osaka vol2にて優勝](https://kwmt27.net/index.php/2013/04/30/startup-weekend-osaka/)


## 執筆歴
- [Software Design 2019年4月号](https://gihyo.jp/magazine/SD/archive/2019/201904)
  - 連載記事「Androidで広がるエンジニアの愉しみ」の「AndroidとiOSアプリを1つのコードで開発できるFlutter」というタイトルで4ページを担当
- 2011/04 [Android UIデザイン&データベースプログラミング](https://www.amazon.co.jp/Android-UI%E3%83%87%E3%82%B6%E3%82%A4%E3%83%B3-%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0-%E9%96%93%E9%A1%95%E6%AC%A1/dp/488337761X)を監修


