+++
date = "2018-08-04T22:00:11+09:00"
title = "Firebase Cloud Messaging(FCM)のトピックについて"
draft = false
categories = ["Firebase"]
tags = ["FCM", "topic"]

+++

Firebase Cloud Messaging(FCM)はTopicをSubscribeすることができて、サーバーはそのSubscribeしたTopicに対して送信することができます。

複数のTopicをSubscribeすることができるのですが、複数のTopicに対して送信するには`condition`を使うことができます。これは[ドキュメント](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)に記載してあり、引用すると、

> "condition": "'dogs' in topics || 'cats' in topics",

このように、dogsトピックを購読しているかcatsトピックを購読している端末に送信することができます。

では、たとえばanimalトピックを購読している端末には送信したいんだけど、dogsトピックを購読している端末には送信したくない場合はどのようにするか。

次のようにすることができます。

> "condition": "'animal' in topics && !('dogs' in topics)",

これは[ドキュメント](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)に記載してあります。


ちょっとハマったのが変数の使い方です。結論からいうとシングルクォートが必要だったのですが、

```typescript
const condition = `\'${animal}\' in topics`
```

animalが変数とした場合、このようにすればOKでした。

## どのTopicを購読しているかを知りたい

どのtopicをsubscribeしているかを確認するには、下記のエンドポイントを使います。

```
https://iid.googleapis.com/iid/info/IID_TOKEN
```
ドキュメントは[こちら](https://developers.google.com/instance-id/reference/server#get_information_about_app_instances
)です。


以下はサンプルのリクエストとレスポンスです。

- リクエスト

```shell
curl -H "Authorization: key=<Server Key>" https://iid.googleapis.com/iid/info/<registration_token>?details=true
```

- レスポンス

```json
{
  "applicationVersion": "1",
  "connectDate": "2018-08-03",
  "attestStatus": "NOT_ROOTED",
  "application": "<application id>",
  "scope": "*",
  "authorizedEntity": "xxxxxxxxxxxx",
  "rel": {
    "topics": {
      "topic1": {
        "addDate": "2018-08-02"
      },
      "topic2": {
        "addDate": "2018-08-01"
      },
      "topic13": {
        "addDate": "2018-08-01"
      }
    }
  },
  "connectionType": "WIFI",
  "appSigner": "d9xxxxxxx",
  "platform": "ANDROID"
}
```

