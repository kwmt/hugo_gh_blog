
+++
title= "LINE Simple Beaconを触ってみて、自分なりの疑問点をまとめた"
date= 2020-03-26T00:06:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["linebot"]
tags = ["linebot", "beacon", "Line", "Go"]
keywords = [""]
+++
基本的に[こちらの記事](https://engineering.linecorp.com/ja/blog/detail/117)を参考にすれば、すぐ分かる人は分かるかと思いますが、僕自身が少しハマったことや疑問点があったので、それを忘れないうちにまとめておこうかと思います。


## HWID(端末ID)はLINE@ MANAGERページの方にある

[ブログ記事](https://engineering.linecorp.com/ja/blog/detail/117)の通り進めていくと、LINE@ MANAGERとLINE Developerのページの２つのページで確認したり設定することになります。

HWIDは[こちら](https://admin-official.line.me/beacon/register#/botlist)から発行できるのですが、[LINE@ Manager](https://admin-official.line.me)で使用しているbotアカウントを選択して、左メニューの`LINE Beacon -> アカウント連携` にある`端末ID`も`HWID`を示していますので、そちらでも確認できます。

## HWIDとは？

スマートフォンがRaspberry Piの受信圏内に出入りしたときに、Beacon EventのWebhookを投げれる状態にするもの。
[ブログ記事](https://engineering.linecorp.com/ja/blog/detail/117)にあるとおりにラズパイ側で設定するだけでいい。ただ、これだけでは投げる先がわかならないので、投げる先の登録は LINE Developersページで登録する必要があります。

## LINE Developersページに行くには？
[LINE@ Manager](https://admin-official.line.me)から設定したいBotアカウントを選択して、左メニューの`アカウント設定->Bot設定`とたどります。
すると、下図のようなリンクがあるので、そこをクリックするといけます。

![Go to LINE Developers page.png](https://qiita-image-store.s3.amazonaws.com/0/22161/39145a34-9bb3-4940-cfe7-30d205be171b.png "Go to LINE Developers page.png")

(LINE Dvelopersページに行くのに、毎回迷子になってました（汗）)


## WebhookのURLは何を指定したらいいの？

サーバーを準備する必要があり、そのURLを指定します。
私はHeroku＋Goを使用しました。[Getting Started on Heroku with Go](https://devcenter.heroku.com/articles/getting-started-with-go#introduction)などを参考にWebhookでBeaconEventを受け取れるようにサーバーを準備します。

ただ単に受けるだけなら、下記のような感じでいいです。

```
http.HandleFunc("/callback", func(w http.ResponseWriter, req *http.Request) {
    log.Println(req)
})
http.ListenAndServe("", nil)
```

ただ、これだけだとEventが来たときスルーさせるだけなので、Eventが来たことを知る必要があるのと、Eventが来たらどうするのか、たとえばLINEのメッセージに送るとかをしたいと思いますので、それに必要なのがこちらの[line-bot-sdk](https://github.com/line/line-bot-sdk-go)です。

Channel SecretとChannel Access Tokenを指定する必要がありますが、これはLINE Developersページに記載されていますので、確認してみてください。

設定したりパースしたりする部分は省略しますが、Beacon Eventが来たことを判定にするには、下記のように書きます。

```
for _, event := range events {
    if event.Type == linebot.EventTypeBeacon {
        // Beacon Eventが来た。ここでLINEメッセージを送ったりする。
    }
}
```

LINEメッセージを送るには、`event`の中にある`replyToken`を使用して、下記のように書くと、ここでは`はろー`というメッセージが届くようになります。

```
bot.ReplyMessage(event.ReplyToken, linebot.NewTextMessage("はろー")).Do()
```



<img width="300" src="https://qiita-image-store.s3.amazonaws.com/0/22161/2f2e92ee-d1c1-c624-a1f3-6ba63fe88ca0.png"  />




## Beacon Eventが来ないんだけど・・・

[Webhook送信を利用する](https://engineering.linecorp.com/ja/blog/detail/115#5_1)にチェックを入れるとか、全部設定はした！設定の確認もした！なのにwebhook送信が来ない場合、[Beacon Event](https://devdocs.line.me/ja/?go#webhook-event-object)や[LINE Beacon](https://devdocs.line.me/ja/?go#line-beacon)の仕様をよく見ると、

> イベント送信元のユーザがLINE Beaconデバイスの受信圏内に出入りしたことなどを表すイベントです。

や、

> LINE Beacon は、「連携しているLINEユーザがビーコンデバイスの受信圏内に出入りしたことなどをwebhookで即座にbotが知ることができる」サービスです。

などと書かれていますので、端末を受信圏内に出入りしてみるとよいかもしれません。
bluetoothの通信距離はだいたい10mなので圏内に出入りすぐできそうですが、それより簡単なのは、端末のbluetooth設定をオン/オフしてみてください。

僕はこれに少しハマりました。。

## 一連の流れ

手書きで汚くて申し訳ないですが・・・こんなイメージです。

![Image uploaded from iOS (2).jpg](https://qiita-image-store.s3.amazonaws.com/0/22161/429c84e0-ed3c-093d-4e33-3df8e2e028b3.jpeg "Image uploaded from iOS (2).jpg")

## LINE BeaconはMessaging APIのReplyAPIを使うので、0円で運用できる

![plan.png](https://qiita-image-store.s3.amazonaws.com/0/22161/d5b39b11-d1c1-9cf0-0103-dc9cd85a934e.png "plan.png")


お金、心配ですよね。LINE BeaconはMessaging APIを使うということは書かれてる記事は多いのですが、Messging APIもReply API とPush APIの2種類あって、結局どっちなの？とおもって調べていたら、

[こちらのドキュメント](https://devdocs.line.me/ja/#reply-message)に、Reply APIは「Webhookで通知されたイベントの中で、返信可能なイベントにはreplyTokenが付与されています。」と書かれてあり、[Beacon Eventのドキュメント](https://devdocs.line.me/ja/#webhook-event-object)に`replyToken`が含まれいるので、Reply APIを使用することになります。


まぁ、よくよく[Reply APIの定義](https://business.line.me/ja/services/bot)を見ると


> Reply APIとは、ユーザーからのメッセージに対して応答メッセージを送信するAPIです。

と書かれてるので、ユーザーの動きに反応して応答するのはReply APIになりますね。



# 参考
* [ラズベリーパイでLINE Beaconが作成可能に！「LINE Simple Beacon」仕様を公開しました](https://engineering.linecorp.com/ja/blog/detail/117)
* [API Reference](https://devdocs.line.me/ja/)
* [Messaging API](https://business.line.me/ja/companies/1343534/services/bot)



