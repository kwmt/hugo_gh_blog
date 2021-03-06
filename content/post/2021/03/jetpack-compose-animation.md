
+++
title= "Animation and Jetpack Compose"
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

[Jetpack Compose: Animation](https://youtu.be/7yY2OocGiQU)

## 単一アニメーション

```kotlin
@Composable
fun AnimateAsStateDemo() {
    var blue by remember { mutableStateOf(true) }
    val color = if (blue) Blue else Orange
    Column {
        Button(onClick = { blue = !blue }) {
            Text(text = "CHANGE COLOR")
        }
        Spacer(modifier = Modifier.height(16.dp))

        Box(
            modifier = Modifier
                .size(128.dp)
                .background(color)
        )
    }
}
```


この例では、BooleanのStateの値blueを持っていて、
ボタンをクリックするとそのStateは反転します。

BoxのカラーがそのBooleanの状態によって切り替わります。

<img src="/images/2021/03/jetpack-compose-animation/compose_no_animation.gif" />

カラーの変更部分を`animateColorAsSate`関数でラップすることで、簡単にアニメーションすることができます。（デフォルトはフェードアニメーションなのかな？）

```kotlin
val color by animateColorAsState(if (blue) Blue else Orange)
```

<img src="/images/2021/03/jetpack-compose-animation/compose_animateColorAsState.gif" />

（初回だけアニメーションしてないように見える）

## 複数のアニメーションを同時に実行したい

以下はBoxのサイズとカラーの値を同時に変更しています。まだアニメーションはしていません。

```kotlin
private enum class BoxState {
    Small,
    Large
}

@Composable
fun UpdateTransitionDemo() {
    var boxState by remember { mutableStateOf(BoxState.Small) }
    val color = when (boxState) {
        BoxState.Small -> Blue
        BoxState.Large -> Orange
    }
    val size = when (boxState) {
        BoxState.Small -> 64.dp
        BoxState.Large -> 128.dp
    }

    Column {
        Button(onClick = {
            boxState = when (boxState) {
                BoxState.Small -> BoxState.Large
                BoxState.Large -> BoxState.Small
            }
        }) {
            Text(text = "CHANGE COLOR AND SIZE")
        }
        Spacer(modifier = Modifier.height(16.dp))

        Box(
            modifier = Modifier
                .size(size)
                .background(color)
        )
    }
}
```

<img src="/images/2021/03/jetpack-compose-animation/compose_same_time_animation_yet.gif" />


このサイズとカラーの値を同時にアニメーションするには？

`Transition`を使います。
Transitionを作るには、`updateTransition`関数を使うことができます。

作成したtranstionをsizeとcolorにそれぞれanimateHogeでラップします。

```kotlin
val transition = updateTransition(targetState = boxState)
val color by transition.animateColor { state ->
    when (state) {
        BoxState.Small -> Blue
        BoxState.Large -> Orange
    }
}
val size by transition.animateDp { state ->
    when (state) {
        BoxState.Small -> 64.dp
        BoxState.Large -> 128.dp
    }
}
```

<img src="/images/2021/03/jetpack-compose-animation/compose_multiple_value.gif" />


アニメーションの振る舞いをカスタムするために、`transtionSpec`パラメータを指定することができます。
たとえば、 Small -> Largeはゆっくり変化させて、Large -> Smallは早く動かすようにするには、次のようにします。

```kotlin
val size by transition.animateDp(
    transitionSpec = {
        if(targetState == BoxState.Large) {
            tween(durationMillis = 500)
        } else {
            tween(durationMillis = 200)
        }
    }
)
```
<img src="/images/2021/03/jetpack-compose-animation/compose_transitionSpec.gif" />

## Visibiliy変更のアニメーションするには

```kotlin
@Composable
fun AnimatedVisibilityDemo() {
    var visible by remember { mutableStateOf(true) }
    Column {
        Button(
            onClick = { visible = !visible }
        ) {
            Text(text = if (visible) "HIDE" else "SHOW")
        }
        Spacer(modifier = Modifier.height(16.dp))

        if (visible) {
            Box(
                modifier = Modifier
                    .size(128.dp)
                    .background(Blue)
            )
        }
    }
}
```

このコードはアニメーションせず、ぱっと切り替わります。

<img src="/images/2021/03/jetpack-compose-animation/compose_visibility_no_animation.gif" />

これをアニメーションさせるには？

`if(visible)`の部分を、

```kotlin
AnimatedVisibility(visible)
```
にするだけです。（但し、現在は`@OptIn(ExperimentalAnimationApi::class)`を付ける必要があるようです）

<img src="/images/2021/03/jetpack-compose-animation/compose_visibility_animation.gif" />


### アニメーションするための簡単な方法

```kotlin
@Composable
fun AnimatedContentSizeDemo() {
    var expanded by remember { mutableStateOf(false) }
    Column {
        Button(
            onClick = { expanded = !expanded }
        ) {
            Text(text = if (expanded) "SHRINK" else "EXPAND")
        }
        Spacer(modifier = Modifier.height(16.dp))

        Box(
            modifier = Modifier.background(Color.LightGray)
        ) {
            Text(
                text = "Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.",
                fontSize = 16.sp,
                textAlign = TextAlign.Justify,
                overflow = TextOverflow.Ellipsis,
                modifier = Modifier.padding(16.dp),
                maxLines = if (expanded) Int.MAX_VALUE else 2
            )
        }
    }
}
```

これはぱっと切り替わりますが、Modifierに`animateContentSize()`をつけるだけで次のようにアニメーションします。

```kotlin
Box(
    modifier = Modifier.background(Color.LightGray)
        .animateContentSize()
)    
```

<img src="/images/2021/03/jetpack-compose-animation/compose_expand_text_animation.gif" />

### クロスフェードアニメーション

```kotlin
private enum class DemoScene {
    Text,
    Icon
}

@Composable
fun CrossFadeDemo() {
    var scene by remember { mutableStateOf(DemoScene.Text) }

    Column {
        Button(
            onClick = {
                scene = when (scene) {
                    DemoScene.Text -> DemoScene.Icon
                    DemoScene.Icon -> DemoScene.Text
                }
            }
        ) {
            Text(text = "TOGGLE")
        }
        Spacer(modifier = Modifier.height(16.dp))

        when (scene) {
            DemoScene.Text -> Text(
                text = "Phone",
                fontSize = 32.sp
            )
            DemoScene.Icon -> Icon(
                imageVector = Icons.Default.Phone,
                contentDescription = null,
                modifier = Modifier.height(48.dp)

            )
        }
    }
}
```


<img src="/images/2021/03/jetpack-compose-animation/compose_crossfade_no.gif" />

これをクロスフェードのアニメーションをさせるには、`Crossfade`でラップするだけです。

```kotlin
Crossfade(targetState = scene) { scene ->
    when (scene) {
        DemoScene.Text -> Text(
            text = "Phone",
            fontSize = 32.sp
        )
        DemoScene.Icon -> Icon(
            imageVector = Icons.Default.Phone,
            contentDescription = null,
            modifier = Modifier.height(48.dp)
        )
    }
}
```

<img src="/images/2021/03/jetpack-compose-animation/compose_crossfade.gif" />
