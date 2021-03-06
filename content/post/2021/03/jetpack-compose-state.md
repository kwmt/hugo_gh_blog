
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

基本的には、こちらの記事をメモしたものです。
[State and Jetpack Compose](https://developer.android.com/jetpack/compose/state)

composeバージョンは 1.0.0-beta01 です。


アプリのステートとは、時間の経過とともに変化する可能性のあるすべての値のことです。


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


