
+++
title= "State and Jetpack Compose"
date= 2021-03-06T12:30:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","State"]
keywords = ["Android", "Jetpack", "Compose","State"]
+++

# はじめに

基本的には、書きを参考に自分なりにまとめたものです。

- [State and Jetpack Compose](https://developer.android.com/jetpack/compose/state)
- [Jetpack Compose 入門](https://youtu.be/US1H0xhjnBI) 
composeバージョンは 1.0.0-beta01 です。





### State


Composeでは状態を`State`というもので扱います。

外から変更可能な `MutableState`と外からは変更できない`State`があります。


```kotlin
@Stable
interface MutableState<T> : State<T> {
    override var value: T
    operator fun component1(): T
    operator fun component2(): (T) -> Unit
}
```

```kotlin
@Stable
interface State<T> {
    val value: T
}
```

```kotiln
var expanded by remember { mutableStateOf(false) }
```
`mutableStateOf`で新しいBooleanのStateを作成しています。

Buttonのクリックイベントの中でその値を変更しています。
```kotiln
onClick = { expanded = true }
```

Stateを使っている箇所が再描画されます。

Composable関数は必要に応じて再実行されます。これを`recomposition(再構成)` とよびます。

#### stateを宣言する時、

 `by` はKotlinのデリゲートプロパティという機能。

 `by`を使わず `=`を使うこともできますが、`expanded.value = true`のように`.value`をつける必要があります。


 ```kotlin
 var expanded = remember { mutableStateOf(false) }
 ```

 ##### remember

`remember`もComposable関数です.

指定したラムダを実行した計算結果を覚えておくための関数です。

Composable関数が再実行される可能性があり、
rembemrを使わず mutableStateOfだけでStateを宣言した場合、
再実行されるときに最初期化され、状態が失われてしまうことになります。
その状態を保持するために`remember`を付ける必要があります。




 ### 例　Expanding Card

 ```kotlin
@Composable
fun ExpandingCard(title: String, body: String) {
    var expanded by remember { mutableStateOf(false) }

    Card {
        Column(
            Modifier
                .width(280.dp)
                .animateContentSize()
                .padding(top = 16.dp, start = 16.dp, end = 16.dp)
        ) {
            Text(text = title)
            if (expanded) {
                Text(text = body, Modifier.padding(top = 8.dp))
                IconButton(onClick = { expanded = false }, modifier = Modifier.fillMaxWidth()) {
                    Icon(Icons.Default.MoreVert, contentDescription = null)
                }
            } else {
                IconButton(onClick = { expanded = true }, modifier = Modifier.fillMaxWidth()) {
                    Icon(Icons.Default.Face, contentDescription = null)
                }
            }
        }
    }
}
```
これをPreviewすると

```kotlin
@Composable
@Preview
fun PreviewExpandingCard() {
    ExpandingCard("hello", "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa")
}
```

次のような動きになります。

<img src="/images/2021/03/jetpack-compose-state/expanding_card.gif" />

