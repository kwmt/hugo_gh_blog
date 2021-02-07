
+++
title= "Layout in Jetpack compose"
date= 2021-02-07T14:30:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","Layout"]
keywords = ["Android", "Jetpack", "Compose","Layout"]
+++

### 縦に並べるには

`Column` を使う

```kotlin
Column {
    Text("Alfred Sisley", fontWeight = FontWeight.Bold)
    Providers(AmbientContentAlpha provides ContentAlpha.medium) {
        Text("3 minutes ago", style = MaterialTheme.typography.body2)
    }
}
```

<img src="/images/2021/02/layout/layout1.png" />


### 横に並べるには

`Row`を使う

```kotlin
Row {
    Surface(
        modifier = Modifier.preferredSize(50.dp),
        shape = CircleShape,
        color = MaterialTheme.colors.onSurface.copy(alpha = 0.2f)
    ) {
        // image goes here
    }
    Column {
        Text("Alfred Sisley", fontWeight = FontWeight.Bold)
        Providers(AmbientContentAlpha provides ContentAlpha.medium) {
            Text("3 minutes ago", style = MaterialTheme.typography.body2)
        }
    }
}
```

ここでは、円形画像のプレースホルダーを作るために`Surface`を使って、先程のColumnと横に並べています。

<img src="/images/2021/02/layout/layout2.png" />


### プレースホルダーとテキストの間に、パディングをいれるには

```
-        Column {
+        Column(
+            modifier = Modifier.padding(start = 8.dp)
+        ) {
```


### 上下中央にするには


```
-    Row {
+    Row(
+        verticalAlignment = Alignment.CenterVertically
+    ) {
```


### Modifier

ほとんどのComposable関数は、`Modifier`を取ることができ、マイComposable関数を作るときは`Modifier`を渡せるようにすることを考えたほうがよい。


```kotlin
@Composable
fun PhotographerCard(modifier: Modifier = Modifier) {
    Row(modifier) { ... }
}
```

`Modifier`は空でなにもしません。


 慣習的に、Modifierは関数の最初のオプションパラメータとして指定されます。これにより、すべてのパラメータに名前を付けなくても、コンポーザブル上で修飾子を指定することができます。

#### Modifierは順番が重要

Modifierは次のように連結できます。

```kotlin
Modifier.background(Color.Blue).padding(start = 8.dp)
```

これと

```kotlin
Modifier.padding(start = 8.dp).background(Color.Blue)
```

これは次の通り結果が異なります。

<img src="/images/2021/02/layout/layout3.png" />

https://developer.android.com/codelabs/jetpack-compose-layouts#2
こちらでは`clickable`をつける順番で挙動が変わる例が載っています。


たとえば、パディング、背景色、角丸などをしたい場合は次のようにします。

```
     Row(
-        modifier = modifier,
+        modifier = modifier
+            .padding(8.dp)
+            .clip(RoundedCornerShape(4.dp))
+            .background(MaterialTheme.colors.surface)
+            .clickable(onClick = {})
+            .padding(16.dp),
         verticalAlignment = Alignment.CenterVertically
     ) {
```

<img src="/images/2021/02/layout/layout4.png" />


### 自作Compossable関数の慣習

新しいコンポーザブルを作成する際には、コンポーザブルをより再利用しやすくするために、デフォルトで Modifier パラメータを持つことは良い習慣です。


```kotlin
@Composable
fun BodyContent(modifier: Modifier = Modifier) {
    Column(modifier = modifier) {
        Text(text = "Hi there!")
        Text(text = "Thanks for going through the Layouts codelab")
    }
}
```

### カスタムレイアウト

`Column`Composable関数を自作してみます。ここでは`MyOwnColumn`と呼ぶことにします。

最初にすべきことは、一度しか測定できない子を測定することです。
レイアウトモディファイアがどのように動作するかと同様に、 
`measurables`のラムダパラメータで、 `measurable.masure(constraints)`を呼び出すことで、
測定可能なすべての子を取得できます。


```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    children: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        children = children
    ) { measurables, constraints ->

        // Don't constrain child views further, measure them with given constraints
        // List of measured children
        val placeables = measurables.map { measurable ->
            // Measure each children
            measurable.measure(constraints)
        }
    }
}
```

自分たちが画面上に配置する前に、Columnのサイズを計算する必要があります。
これから作る予定のColumnのサイズを指定するには、`layout(width,height)`を呼び出して子を配置するためにラムダを指定します。

```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    children: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        children = children
    ) { measurables, constraints ->
        // Measure children - code in the previous code snippet
        ...

        // Set the size of the layout as big as it can
        layout(constraints.maxWidth, constraints.maxHeight) {
            // Place children
        }
    }
}
```

最後に`placeable.placeRelative(x, y)`を呼んで、画面上に子を配置します。
子を垂直に配置するために、子を配置したy座標を記録しておきます。MyOwnColumnの最終コードは次のようになります。

```kotlin
@Composable
fun MyOwnColumn(
    modifier: Modifier = Modifier,
    children: @Composable () -> Unit
) {
    Layout(modifier = modifier, content = children) { measurables, constraints ->
        // 子供Viewをさらに制約しないでください。
        // 測定された子のリスト
        val placeables = measurables.map { measurable ->
            measurable.measure(constraints = constraints)
        }
        var yPosition = 0
        Log.d("MyOwnColumn", "maxWidth:${constraints.maxWidth}, maxHeight:${constraints.maxHeight}")
        layout(constraints.maxWidth, constraints.maxHeight) {
            // Place children in the parent layout
            Log.d("MyOwnColumn", "yPosition:$yPosition")
            placeables.forEach { placeable ->
                // Position item on the screen
                Log.d("MyOwnColumn", "placeable.height:" + placeable.height.toString())
                placeable.placeRelative(x = 0, y = yPosition)
                // Record the y co-ord placed up to
                yPosition += placeable.height
                Log.d("MyOwnColumn", "yPosition:$yPosition")
            }
        }
    }
}
```

この`MyOwnColumn`を次のように使うと

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyOwnColumn(modifier = Modifier.background(Color.Red)) {
                Text("MyOwnColumn")
                Text("places items")
                Text("vertically.")
                Text("We've done it by hand!")
            }
        }
    }
}
```

次のようにレイアウトされ、

<img src="/images/2021/02/layout/myowncolumn_complete.png" width="300"/>


上記で仕込んだログは次のようになります。

```
D/MyOwnColumn: maxWidth:1080, maxHeight:1977
D/MyOwnColumn: yPosition:0
D/MyOwnColumn: placeable.height:52
D/MyOwnColumn: yPosition:52
D/MyOwnColumn: placeable.height:52
D/MyOwnColumn: yPosition:104
D/MyOwnColumn: placeable.height:52
D/MyOwnColumn: yPosition:156
D/MyOwnColumn: placeable.height:52
D/MyOwnColumn: yPosition:208
```

ちなみに、標準の`Column`の場合は次のようになります。

<img src="/images/2021/02/layout/column.png" width="300"/>