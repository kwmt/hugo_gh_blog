
+++
title= "corutine callbackFlowについて"
date= 2021-04-29T13:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "coroutine", "Flow"]
keywords = ["Android", "coroutine", "Flow", "callbackFlow"]
+++


# はじめに

```
interface OnChangeListener {
    fun onChange()
}
```

のようなコールバックを、Flowに変換するにはどうすればいいかを調べました。

RxJavaでいうところ、`Observable.create` のようなことを実現するにはどうすればいいかという記事です。


# 結論

[`callbackFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/callback-flow.html)というAPIがcoroutineに用意されてるので、それを使うと良さそうです。

これは、cold flowを生成します。つまり、`collect`されるまで動きません。


# サンプル

サンプルを書いてみました。


{{< gist fdc27a0fb44278b1597a13331693b392 >}}

flowFlow関数で、`callbackFlow`を使ってコールバックからFlowに変換しています。
OnChangeListener#onChangeの実装で[`offer`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.channels/-send-channel/offer.html)を使用してチャンネルに追加しています。
容量がいっぱいの場合は例外がなげられるとのこと。

`awaitClose` でunregsiterやcancel処理を実装する必要があります。
これを実装しなければ、実行時に

> IllegalStateException: 'awaitClose { yourCallbackOrListener.cancel() }' should be used in the end of callbackFlow block.

というエラーがでます。（感想ですが、クローズ処理を忘れないので、良い仕組みだと思います！）

callbackからflowに変換できたので、あとは`collect`してあげるだけです。
このサンプルでは、1秒ごとにログに値が出力されます。

```
onchange: 1
onchange: 2
onchange: 3
onchange: 4
onchange: 5
onchange: 6
onchange: 7
...
```