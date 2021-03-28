

+++
title= "Jetpack Compose PlayGround"
date= 2021-03-28T19:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","AndroidDevChallenge"]
keywords = ["Android", "Jetpack", "Compose","AndroidDevChallenge"]
+++


# はじめに
※こちらは更新予定です。

確認環境は下記です。

- compose_version: 1.0.0-beta02
- kotlin version: 1.4.31
- Android Studio version: Android Studio Arctic Fox | 2020.3.1 Canary 12

確認したコードはGitHubにおいてます。

https://github.com/kwmt/JetpackComposePlayGround


### 縦方向（Vertical）のリスト表示するには

縦方向（Vertical）のリストを表示するには、[`LazyColumn`](https://developer.android.com/jetpack/compose/lists#lazy) を使います。

<img src="/images/2021/03/jetpack-compose-playground/lists/LazyColumn.gif" width="300" />


```kotlin
import androidx.compose.foundation.lazy.LazyColumn
@Composable
fun SampleList() {
    LazyColumn {
        items(5) { index ->
            // 各要素で表示したいUIを書く。
            ListItem(index) 
        }
    }
}
```

多くのアイテムやサイズのわからないリストを表示する必要がある場合、`Column`のようなレイアウトを使用すると、全てのアイテムが表示されるかどうかに関わらずレイアウトされるので、パフォーマンスに影響があります。
`LazyColumn`や`LazyRow`は表示されるアイテムのみをレイアウトするコンポーネントになります。（RecyclerViewと同じ概念）

ついでに、リストの各要素はComposable`ListItem`は自作のComposable関数で、リストアイテムのUIを定義しています。

```kotlin
@Composable
fun ListItem(index: Int = 0) {
    Column {
        Box(
            modifier = Modifier
                .height(200.dp)
                .background(Color.Blue)
                .fillMaxWidth()
        ) {
            Text(
                modifier = Modifier.align(Alignment.Center),
                text = "index: $index",
                style = TextStyle(color = Color.White)
            )
        }
        Divider(modifier = Modifier.height(8.dp))
    }
}
```

`LazyColumn`の引数には、`content: LazyListScope.() -> Unit` をとるようになっていて、[`LazyListScope`](https://developer.android.com/jetpack/compose/lists#lazylistscope)は、単一のアイテムを追加する`item`メソッドと、複数のアイテムを追加する`items`メソッドと、これはまだExperimentalですが、スティッキーヘッダーを追加する`stickyHeader`メソッドがあります。

### 横方向（Horizontal）のリスト表示するには

`LazyRow`を使う。

```kotlin
@Composable
fun SampleHorizontalList() {
    LazyRow{
        items(5) { index ->
            ListHorizontalItem(index)
        }
    }
}
```

<img src="/images/2021/03/jetpack-compose-playground/lists/LazyRow.gif" width="300" />

```kotlin
@Composable
fun ListHorizontalItem(index: Int = 0) {
    Column {
        Box(
            modifier = Modifier
                .width(200.dp)
                .background(Color.Blue)
                .height(200.dp)
        ) {
            Text(
                modifier = Modifier.align(Alignment.Center),
                text = "index: $index",
                style = TextStyle(color = Color.White)
            )
        }
        Divider(modifier = Modifier.height(8.dp))
    }
}
```

### スティッキーヘッダー(StickyHeader)を使うには

[`stickyHeader`](https://developer.android.com/jetpack/compose/lists#sticky-headers)メソッドを使うようです。（2021/03/28現在Experimental 1.0.0-beta02時点）

```kotlin
@OptIn(ExperimentalFoundationApi::class)
@Composable
fun StickyListSample() {
    LazyColumn {
        grouped.forEach { (initial, contactsForInitial) ->
            stickyHeader {
                Text(
                    modifier = Modifier
                        .fillMaxWidth()
                        .background(Color.Gray)
                        .padding(horizontal = 8.dp),
                    text = initial.toString()
                )
            }

            items(contactsForInitial) { contact ->
                ListItem2(contact.firstName)
            }
        }
    }
}
```

下記はFakeデータです。
```kotlin
private data class Contact(val firstName: String)

private val contacts =
    listOf<Contact>(
        Contact("AAA"),
        Contact("ABB"),
        Contact("Bbb"),
        Contact("Bbb2"),
        Contact("Bbb3"),
        Contact("Bbb3"),
        Contact("Bbb4"),
        Contact("Ccc"),
        Contact("Ccc2"),
        Contact("Ccc3"),
        Contact("Ccc4"),
        Contact("Ccc5"),
    )
private val grouped = contacts.groupBy { it.firstName[0] }
```

適当なアイテムです。

```kotlin
@Composable
fun ListItem2(text: String = "") {
    Column {
        Box(
            modifier = Modifier
                .height(200.dp)
                .background(Color.Blue)
                .fillMaxWidth()
        ) {
            Text(
                modifier = Modifier.align(Alignment.Center),
                text = "$text",
                style = TextStyle(color = Color.White)
            )
        }
        Divider()
    }
}
```

<img src="/images/2021/03/jetpack-compose-playground/lists/StickyHeader.gif" width="300" />

### View(Composable)を重ねて表示するには

`Box`を使います。


<img src="/images/2021/03/jetpack-compose-playground/box/box.png" />

たとえば、上の画像のようにグレーの四角(縦横100dp)とブルーの四角(縦横50dp)を重ねて表示するには、次のようなコードになります。


```kotlin
@Composable
fun BoxSample() {
    Box {
        // グレーの四角(縦横100dp)
        Spacer(
            modifier = Modifier
                .background(Color.Gray)
                .size(100.dp)
        )

        // ブルーの四角(縦横50dp)
        Box(
            modifier = Modifier
                .size(50.dp)
                .offset(x = 10.dp, y = 10.dp)
                .background(Color.Blue),
            contentAlignment = Alignment.Center
        ) {
            Text(
                "test",
                modifier = Modifier
                    .fillMaxWidth()
                    .align(Alignment.Center)
                    .background(Color.Red),
                textAlign = TextAlign.Center,
                style = TextStyle(color = Color.White),
            )
        }
    }
}
```

※グレーの四角とブルーの四角の順番を入れ替えると、ブルーの四角が見えなくなってしまいます。これまでのXMLと同様ですね。

### 戻るボタン（Upボタン）を表示するには

<img src="/images/2021/03/jetpack-compose-playground/icon/upbutton.gif" />

```kotlin
@Composable
fun Up(
    upPress: () -> Unit,
) {
    IconButton(
        onClick = upPress,
        modifier = Modifier
            .padding(horizontal = 16.dp, vertical = 16.dp)
            .size(36.dp)
            .background(
                color = Color.White.copy(alpha = 0.32f),
                shape = CircleShape
            )
    ) {
        Icon(
            imageVector = Icons.Rounded.ArrowBack,
            contentDescription = "back"
        )
    }
}

val Shadow4 = Color(0xff7057f5)

@Preview
@Composable
fun PreviewUp() {
    Box(modifier = Modifier.background(Shadow4)) {
        Up {}
    }
}
```


### 画像をWeb上からダウンロードして表示するには(URL)

Android Viewで使われていたGlideやCoilをCompose用にラップしている[accompanist](https://github.com/google/accompanist)というライブラリを使うのがデファクトスタンダードになりそうな気がしますので、それを使います。

```gradle
implementation "com.google.accompanist:accompanist-coil:0.7.0"
```

```kotlin
import com.google.accompanist.coil.CoilImage

@Composable
fun NetworkImageSample(
) {
    CoilImage(
        data = "https://picsum.photos/300/300",
        contentDescription = null,
    )
}
```

これを実行すると、次のようになります。

<img src="/images/2021/03/jetpack-compose-playground/image/networkimage.png" width="300"/>



インターネットにアクセスするので、パーミッションの追加を忘れずに。

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

こちらのサンプルのようなものを作っておくと便利だと感じました。
https://github.com/android/compose-samples/blob/main/Owl/app/src/main/java/com/example/owl/ui/utils/NetworkImage.kt#L44

### 画像を円形にするには

```kotlin
@Composable
fun CircleImageSample() {
    Surface(
        shape = CircleShape,
        modifier = Modifier
            .fillMaxWidth()
            .aspectRatio(1f)
    ) {
        CoilImage(
            data = "https://picsum.photos/300/300",
            contentDescription = null,
        )
    }
}
```

<img src="/images/2021/03/jetpack-compose-playground/image/circle_image.png" width="300"/>

### 画面遷移するには

Jetpack ComposeにもNavigation Componentをサポートしています。

```gradle
implementation "androidx.navigation:navigation-compose:1.0.0-alpha09"
```

`NavController`はNavigation Componentの中心的なAPIです。
このAPIはステートフルであり、アプリ内の画面を構成するコンポーザブルのバックスタックと各画面の状態を追跡します。

`NavController`を作成するには、`rememberNavController()`メソッドを使用します。

```kotlin
val navController = rememberNavController()
```

`NavController`は１つの`NavHost`コンポーザブルに関連付ける必要があります。


```kotlin
NavHost(navController, startDestination = "animals") {
    composable("animals") { Animals(...) }
    composable("animal/{animalId}") { AnimalDetail(...) }
}
```

これは動物一覧を最初の画面(startDestination)として、リストの要素をタップしたら、動物詳細画面("animal_detail")に遷移することを想定しています。

このようにNavHostを定義したら、コンポーザブル間を移動するには、`navigate()`メソッドを使用します。

```kotlin
fun Animals(navController: NavController) {
    //省略
    val animal: Animal  = // 省略
    Button(onClick = { navController.navigate("animal/{animal.id}")}) {
        // 省略
    }
}

data class Animal(val id: String)
```

これだと、各画面がNavControllerを知ることになってしまうので、画面はクリックしたら何を渡すのかわたさないのかに関心があって、どこに遷移するかはNavGraph側で管理したいので、それを解決するのが、[こちらのMainActions](https://github.com/android/compose-samples/blob/main/Owl/app/src/main/java/com/example/owl/ui/NavGraph.kt#L75)が参考になるかと思います。


####  前の画面にもどるには

`NavController#navigateUp`メソッドを使用します。

https://github.com/kwmt/android-dev-challenge-compose/blob/main/app/src/main/java/com/example/androiddevchallenge/NavGraph.kt#L44


<img src="/images/2021/03/jetpack-compose-playground/navigation/navigation.gif" width="300"/>



### ローカルの画像を表示するには
TODO
### 角丸にするには
TODO




