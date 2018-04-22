+++
date = "2018-04-21T14:00:00+09:00"
title = "Android Oreoのプッシュ通知対応でハマった３つのこと"
draft = false

+++

<div align="center">
<img src="/images/2018/04/yahoo_mercari.png" />
</div>

# はじめに

> In the second half of 2018, Play will require that new apps and app updates target a recent Android API level.
> 
> 引用元: <a target="_blank" href="https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html">https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html</a>
>
> 日本語訳: <a target="_blank" href="https://developers-jp.googleblog.com/2017/12/improving-app-security-and-performance.html">https://developers-jp.googleblog.com/2017/12/improving-app-security-and-performance.html</a>

突然ですが、PlayStoreでアプリを公開するにあたり、ターゲットSDKバージョンを最新に指定することが義務化されることはご存知でしょうか。
新規アプリの公開で、2018年8月から、既存アプリのアップデートで2018年11月からAPIレベル26以降つまりAndroid 8.0オレオ以降の対応が必須になります。

今年の11月というと半年後ぐらいですので、オレオ対応はそろそろ開始し始めないと遅いかもしれません。



ということでオレオ対応をしていたのですが、そのときに僕がハマったことを３つのこと共有させていただきたいと思います。

話さないこととしては、

- プッシュ通知の仕組みや実装の仕方
- プッシュ通知の大切さ

これらについては話しません。
プッシュ通知の大切さにつきましては、
去年9月のiOSDCで、「頼むからプッシュ通知の使い方をおろそかにしないでくれ！」(<a target="_blank" href="https://www.youtube.com/watch?v=6CSQFNWlVwE">YouTube</a>,
<a target="_blank" href="https://speakerdeck.com/jollyjoester/lai-mukaraputusiyutong-zhi-falseshi-ifang-wo-orosokanisinaidekure">スライド</a>
)というタイトルで発表されていて、とてもよい発表でしたのでぜひそちらを見て頂きたいです。


# ハマった3つのこと
ハマったこととは次の３つになります。

- 通知が来ない！
- 通知アイコンが真っ白になった！
- 通知をオフにしても通知が来る！

ひとつひとつ詳しく見ていきましょう。

## そもそも通知が来ない！
MarshmallowやNougatでは通知が来ていたのですが、targetSdkVersionをたとえば26にすると通知が来なくなりました。

どういうことかと言いますと、targetSdkVersionを26に設定すると、そのアプリはちゃんとOreo対応していますよ、と宣言したことになります。
通知をOreo対応するために必ず必要なことがあります。それは

> all notifications must be assigned to a channel.
> 
> 引用元: <a target="_blank" href="https://developer.android.com/training/notify-user/channels.html">https://developer.android.com/training/notify-user/channels.html</a>

すべての通知はチャンネルに割り当てなければなりません。

チャンネルというのは、例えばGoogle Play Musicアプリを参考しますと、下図の赤枠で囲んだ部分になります。

<div align="center">
<img src="/images/2018/04/channel_sample.png" />
</div>

チャンネルごとに通知をON/OFFすることができたり、カラーやサウンドもチャンネルごとに設定できます。
同じアプリ内で、通知が来て欲しいチャンネルは通知をONにし、通知が不快だなと思うチャンネルだけを通知が来ないようにOFFできるという機能です。

これらのチャンネルは、targetSdkVersionを上げただけでは作成されません。
つまり、通知が来ない原因は、開発者がチャンネルを明示的に作成する必要があったのですが、ただバージョンを上げただけなので、チャンネルは作成されず通知が来なかったというわけです。

## 通知アイコンが真っ白になった！
次にAndroid Oreoで通知アイコンが真っ白になるという現象に遭遇しました。下図の矢印のところです。

<div align="center">
<img src="/images/2018/04/notification_icon_white.png" />
</div>

これをみたとき最初は、Lolipopから、アイコン画像の透過でない部分は白くレンダリングされるため、アイコン画像自体が対応していないのなかな？と思ったんですが、手元にあったMarshmallow端末で確認した所、ちゃんと表示されていたので、それは違いました。

いろいろ調べた所、Firebase SDK version11.8.0の不具合とのことで、12.0.0に上げると解決しました。リリースノートにもアイコンの不具合を修正したと記載があります。

<div align="center">
<img src="/images/2018/04/firebase_sdk_release_note.png" />
</div>

<a target="_blank" href="https://firebase.google.com/support/release-notes/android">https://firebase.google.com/support/release-notes/android</a>

## 通知をオフにしても通知が来る！
3つ目は、通知OFFにしても通知が来てしまう現象です！
チャンネルごとに通知オフにできるのは、先程のチャンネルの説明で話したとおりなのですが、チャンネルの設定をオフにしても来てしまってました。

チャンネル設定を見てみると、作成した覚えのない「その他」というチャンネルが出来ていて、そのチャンネルに割り当てられてしまったようです。

<div align="center">
<img src="/images/2018/04/unkown_channel.png" />
</div>


アプリがバックグラウンドにいるときにプッシュ通知が来た場合のチャンネルをAndroidManifestで設定できるのですが、原因はその設定が間違っていただけでした。

こちらとしては参考にしたFirebase 公式のサンプルをそのまま書いていたのですが、そのサンプルが間違っていたためでした。

こちらに関してはいまから実装される方に関しては、すでにドキュメントは2017年9月に修正されてますのでハマることはないかと思いますが、こうことがあったので共有させていただきました。


以上ハマったこと3つでした。

# まとめ

- Android Oreoではチャンネルを作成、通知をチャンネルに割り当てる必要があります。
- なにかおかしかったら、SDKバージョンや公式の資料でも参考にしたものを疑ってみる


# おわりに
この記事は、
<a target="_blank" href="https://yahoo-osaka.connpass.com/event/79888/">mixleap #11</a>でLTさせて頂いた内容です。発表原稿を作ったのですが、それをもとに加筆・修正したものです。
その時のスライドは下記です。
<a target="_blank" href="https://www.slideshare.net/yasi_life/androidoreo-94560866">https://www.slideshare.net/yasi_life/androidoreo-94560866</a>

反省点は、発表原稿を読むのに必死で前をみれなかったことです。発表練習が圧倒的に足りなかったです。。あとPCを置く台が低かったというのもあるかもですが、資料の字が小さくて読みにくかったので、次はフォントサイズを少し大きめにしようと思いました。覚えるくらい練習しろよというツッコミが来そうですが。。5分のところ6分を超えてしまってスミマセンでした。。

しかし、めちゃめちゃいい知見だったと言ってくださる方がいて、発表した甲斐がありました。ただ、懇親会中のザワザワした中でのLTだったので、あまり聞かれてなかったっぽいのですがw

最後になりましたが、Yahooさん、メルカリさん、とてもいい場を作って頂きありがとうございました！最高の金曜になりました！
