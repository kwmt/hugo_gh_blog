---
title: 'Google I/O報告会 2015 参加メモ #devfest'
author: kwmt
layout: post
date: 2015-06-23
url: /index.php/2015/06/23/google-io-report-2015-memo-devfest/
categories:
  - Android
  - 日記

---
<a href="https://sites.google.com/site/devfestjapan/" target="_blank">Google I/O報告会 2015(会場:サイバーエージェント(渋谷マークシティ13F)</a>に行ってきました。

<a href="https://sites.google.com/site/devfestjapan/2015summer/agenda" target="_blank">アジェンダが公開されてた</a>

## 全体の流れメモ

### 全体

DevFestの歴史
  
2各会場の紹介
  
Keynote Androidはぬいて説明
  
会場MosconeWest
  
派手さな感じで楽しかった
  
すっごい寒かった
  
8回目SF
  
セッション数がヘリ、コミュニケーション重要視
  
Androidの話題が多い、デザイン多い
  
クラウド少ない→別のイベントになるんじゃないかと予想
  
Lobby公園のような感じ
  
コミュニケーションできるような。充電ステーションでみんあが集まて会話が生まれる感じ
  
exiii $300
  
おみやげはメインでなく、コミュニケーションメイン
  
キーノートはいま使える現実的な物。

DESIGN to DE$IGN デザインはビジネス

### Android

M developer previewの略？ｗ
  
指紋認証 Fingerprint
  
サンプルは github google
  
全データはデフォルトでバックアップされる
  
細かくも設定できる。
  
Doze
  
しばらくさわってない端末はdozeモード へ
  
App stanby
  
しばらくさわっていないアプリはidleモード
  
※しばらくはどれくらい？はわからない。

Icons in Notifications:Icon.createWidthBitmapで生成できる

Textの範囲指定

App Links

  * Webページとアプリとの関係性を明確にする。
  * 自分のアプリが優先的に。
  * Sylus

フラッシュだけを使うことがカメラを使わずにできるようになった。

AlphaOptimaization

  * いままでよりもいい感じで透明Viewを使える

MIDIのAPI追加

ストレージ

ART:runtime stats

Ambient Mode

  * できるだCPU稼働率をゼロにするモード
  * manifest
  * WakerLockとWearable supportlibraryの２つが前提でsetAmibentEnabeledの一行だけ

App Invite

  * 紹介するときsetDeppLinkでインテント作って
  * 紹介されたとき、リファラーがついてれればOK
  * マニフェストにintentfilterを追加

App Indexing

  * Google検索で、アプリがあればアプリを紹介してくれる。
  * マニフェストに関係があるドメインを禁輸しておく。
  * すべての検索でひっかかてもうっとおしので、robot.txtに相当する機能がある。

Android Wear

  * Maps API Watchで使える機能 
      * モバイルと一緒onMapReadyとかFragmentManagerからmapを取得

Materialデザイン

EmptyState

MaterialShowcase

各APIのFrameworkがCocoaPodsに対応
  
pod try

### Maps

  * Don&#8217;t ping too much
  * Don&#8217;t ping on the minute
  * ランダムな秒数をおいてサーバーに問い合わせなさい
  * 定期的な問い合わせだと、複数人からのアクセスで同時にサーバーアクセスが行くので重い
  * ランダムで他の人と合わないように問い合わせをするといいよ。
  * apikeyをアプリに入れないように。
  * サーバーを通してapiキーを取得してね。
  * iOSのGoogle Map SDK for iOS version1.10
  * Objc/Swift対応

### Materialデザイン

  * 全てに適用　VRもTVもAutoも。
  * でざいん 統一感、使いやすい

### ブランディング

  * 自社のブランドにするには？Googleっぽくなく。→ペルソナを明らかに。必要なところに。</p> 
  * Too hot→無理にハンバーガメニューを使わなくていい

  * ToolCold→ツールバーを大きめにうまく隠して
  * TooRight→モバイル、Webも前部に対応

## 東京会場でのセッション２つ

  * Polymerのライブコーディング
  * Google鈴木さんからのマテリアルデザインに関する補足

Polymerの話は別記事にする予定なので、以下は、マテリアルデザインについてのメモ。

  * FABは必ず必要ではない
  * カードViewを必ずしも使う必要がない。セカンダリアクションが期待されてしまうため、アクションが残念。
  * FABは右下に置かなくていい
  * アニメーションについて 
      * 早い段階でアニメーション設計を考えていたほうがいい。
  * portlaitとlandscape
  * 画面内で完結するようにするのもいい
  * Viewの階層は一番低いレイヤーから考えるとよい。FABが一番上。
  * 色紙を使うといい　ペーパープロトタイピング
  * material design award
  * ガイドラインは絶対じゃない。

**マテリアルデザインってグーグルっぽくなるけど、どうしたらいいん？**

  * 開発者が楽になったり、ユーザーが期待する動きに期待
  * Generalの選択肢とし、いいところどりでいいと思うby鈴木さん
  * 色を変えること　とガイドラインを解釈して自分たちでガイドラインを作るといいんじゃないby安藤幸央さん

## material design libraryの使い方

Google 公式のAndroid Blogよりも下のほうがわかりやすいらしい。
  
[Exploring the new Android Design Support Library — ribot labs — Medium][1]

ちなみにGoogleもMediumを使い始めているらしい
  
<a href="https://medium.com/google-developers" target="_blank">https://medium.com/google-developers</a>

## いちばん印象的だったこと

やはり、この質問がいちばん記憶にあるし、大事なことだとおもう。
  
Q「マテリアルデザインを使うとGoogleっぽくなるのでは？」
  
A.ガイドラインはガイドラインであって必ずそうしなければならないということはない。

Googleは<a href="http://android-developers.blogspot.jp/2015/05/announcing-material-design-showcase-and.html" target="_blank">Announcing the Material Design Showcase and Awards | Android Developers Blog</a>を実施したが、必ずガイドラインに沿わなければならないということはないということを示したかったため実施したとのこと。

特に<a href="https://play.google.com/store/apps/details?id=com.ideashower.readitlater.pro" target="_blank">Pocket Androidアプリ</a>, <a href="https://play.google.com/store/apps/details?id=com.tumblr" title="Tumbler Androidアプリ" target="_blank">Tumbler Androidアプリ</a>のアプリを見ればわかるとのこと。

Tumbler使用者からすると、FAB(Floating Action Button)が出てくるまでマテリアルデザインだと気づかなかったという。

  * [Material icons &#8211; Google Design][2] 
  * [How Google Finally Got Design | Co.Design | business + design][3]
  * [Slack Concept for Android – Mobile app by Nik][4]

## Twitterの#devfest

<a href="https://twitter.com/hashtag/devfest?src=hash&vertical=default&f=tweets" target="_blank">https://twitter.com/hashtag/devfest?src=hash&vertical=default&f=tweets</a>

 [1]: https://medium.com/ribot-labs/exploring-the-new-android-design-support-library-b7cda56d2c32
 [2]: https://www.google.com/design/icons/
 [3]: http://www.fastcodesign.com/3046512/how-google-finally-got-design
 [4]: https://www.materialup.com/