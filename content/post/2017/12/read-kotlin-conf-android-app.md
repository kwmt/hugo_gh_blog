+++
date = "2017-12-01T14:30:00+09:00"
title = "Kotlin Conf Android Codeを読んだときのメモ"
draft = false
categories = ["Android"]

+++

# はじめに

[Kotlin Confのソース](https://github.com/kwmt/kotlinconf-app)のAndroidを読みました。4時間ぐらいで読めた所までですが。


コードをおいやすくするために、Android Studio 3.0でプロジェクトを開きます。

## kotlinconf-app/android/build.gradleを見る


```
maven { url "http://dl.bintray.com/kotlin/kotlin-eap-1.2" }
```

まず、これが気になり調べたところ、Kotlinのアーリーアクセスプログラムのmavenリポジトリのようです。

[bintray](https://bintray.com/kotlin/kotlin-eap)には、

> Builds of Kotlin released under the Early Access Program

このように書いてありました。

## kotlinconf-app/android/app/build.gradleを見る

appモジュールのbuild.gradleのdependeciesを見ると、全体がだいたいどんな感じのアプリか分かるかもなので、みてみます。

```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:26.1.0'
    compile 'com.android.support:recyclerview-v7:26.1.0'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
    testCompile 'junit:junit:4.12'
    androidTestCompile('com.android.support.test.espresso:espresso-core:3.0.1', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })

    compile 'com.android.support:design:26.1.0'

    compile"org.jetbrains.kotlin:kotlin-stdlib-jre7:$kotlin_version"

    compile "android.arch.lifecycle:runtime:1.0.0"
    compile "android.arch.lifecycle:extensions:1.0.0-alpha9-1"
    kapt "android.arch.lifecycle:compiler:1.0.0-alpha9-1"

    compile "org.jetbrains.anko:anko-sdk25:$anko_version"
    compile "org.jetbrains.anko:anko-appcompat-v7:$anko_version"
    compile "org.jetbrains.anko:anko-recyclerview-v7:$anko_version"
    compile "org.jetbrains.anko:anko-commons:$anko_version"
    compile "org.jetbrains.anko:anko-design:$anko_version"
    compile "org.jetbrains.anko:anko-coroutines:$anko_version"

    compile 'com.squareup.retrofit2:retrofit:2.3.0'
    compile 'com.squareup.retrofit2:converter-gson:2.3.0'

    compile 'net.opacapp:multiline-collapsingtoolbar:1.5.0'
    compile 'com.github.bumptech.glide:glide:3.7.0'
    compile 'com.brandongogetap:stickyheaders:0.4.9'
    compile 'ru.gildor.coroutines:kotlin-coroutines-retrofit:0.8.2'

    compile project(":common-jvm")
}

kotlin {
    experimental {
        coroutines "enable"
    }
}
```

recycelrviewを使ってリストがあり、

```
compile 'com.android.support:design:26.1.0'
```

があるので、マテリアルデザインっぽくなってるのか。ふむふむ。

全体的な設計は、Android Architecture Componentsを使っていて、
レイアウトはankoを使って組んでいるっぽい。あとcoroutinesも使ってるのですね。

APIリクエストにはretrofitを使って、JSONのパースにはGSONを使っていました。

glide使ってるので画像表示があり、

```
com.brandongogetap:stickyheaders
```

というライブラリを使って、iOSのような感じでリストをスクロールすると、リストのセクションヘッダーが上部に張り付いてくれるUI(iOSのUITableViewのデフォルトのような動き)なのかな、と想像してみます。

testは残念ながら書いてなさそうな印象です。


## AndroidManifestを見る

起動時に表示されるのは`MainActivity`で、カスタムApplicationクラス(`KotlinConfApplication`)を使っていることがわかります。

パーミッションはインターネットアクセス権限だけです。

## KotlinConfApplicationを見てみる

onCreateでまずやっているのは、

```
val userId = getUserId()
```

UserIdを取得しています。

どうやって取得しているか、を見てみます。

```kotlin
private fun getUserId(): String {
    defaultSharedPreferences.getString(USER_ID_KEY, null)?.let {
        return it
    }
    val userId = "android-" + UUID.randomUUID().toString()
    defaultSharedPreferences
            .edit()
            .putString(USER_ID_KEY, userId)
            .apply()
    return userId
}
```

SharedPreferenceに保存されていれば取り出し、保存されていなければ、UUIDを作成して、

```
"android-" + UUID
```

という形式でSharedPreferenceに保存した上で、それを返しています。

(個人的な感想としては、getUserIdといいながら、create useridもしているので、メソッド名を変えるか、Create useridしている部分を別メソッドに分けたいかなと思いました)

### ところで、`defaultSharedPreferences`って急に出てきたけど、どこから生まれたのか？

```
compile "org.jetbrains.anko:anko-commons:$anko_version"
```

`anko-commons`というライブラリが提供している拡張関数で、

```
inline val Context.defaultSharedPreferences: SharedPreferences
    get() = PreferenceManager.getDefaultSharedPreferences(this)
```

`PreferenceManager.getDefaultSharedPreferences(this)`から`SharedPreferences`インスタンスを取得していました。

https://github.com/Kotlin/anko/blob/master/anko/library/static/commons/src/ContextUtils.kt#L44

SharedPreferencesを使うなら必ず書く処理なので、いいですね！

他にも、

```kotlin
KotlinConfDataRepository.Error.FAILED_TO_DELETE_RATING ->
    toast(R.string.msg_failed_to_delete_vote)
```

の部分の、`toast`も`anko-commons`で定義されている拡張関数のようです。

`toast`は下記のように定義され、`show`までやってくれています。

```kotlin
fun Context.toast(message: Int) = Toast.makeText(this, message, Toast.LENGTH_SHORT).show()
```


よく書くようなやつは、`anko-commons`にあるかもしれないので、探してみようと思いました。


### launch

onCreateの最後の部分に、下記の処理があります。

```kotlin
launch(UI) {
    val dataLoaded = repository.loadLocalData()
    if (!dataLoaded) {
        repository.update()
    }
    // Get new data from server if new user was created (server db was cleaned)
    if (postUserId(userId) && dataLoaded) {
        repository.update()
    }
}
```

`launch`ってなんでしょう？

これは[kotlinx.coroutines](https://github.com/kotlin/kotlinx.coroutines)ライブラリで定義されており、
[launch](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/launch.html)に記載されているドキュメントを見ると、


> Launches new coroutine without blocking current thread and returns a reference to the coroutine as a Job. The coroutine is cancelled when the resulting job is cancelled.

とのことです。詳しくは分かってないですが、とりあえず非同期処理をしてくれるものらしいです。


`launch`では、データをローカルからロードしたり、APIサーバーにpostしたり、APIサーバーからデータを取得したりしています。

### `awaitResult()`

`postUserId(userId)`の実装を見ていると、`awaitResult()`という見慣れないメソッドがありました。

これは、[kotlin-coroutines-retrofit](https://github.com/gildor/kotlin-coroutines-retrofit)ライブラリが提供しているRetrofit Callに対する拡張関数のようです。`Result`を返してくれます。

続けて、`ifSucceeded`や`ifError`、`ifException`は、KotlinConfアプリが作っている関数でした。[ソースはこのあたりです](https://github.com/kwmt/kotlinconf-app/blob/master/android/app/src/main/java/com/jetbrains/kotlinconf/Utils.kt#L68-L80)

### minus operator

`KotlinConfDataRepository#update`内で`syncLocalFavorites`を実行していて、その中で

```
val missingOnServer = favorites - sessionIds
```

と、マイナスを使っているところが気になったのですが、調べてみるとListの差分を`-`だけで表現できるのですね。

試してみた所、[a, b] - [a] = [b]となってました。

```
fun main(args: Array<String>) {
    val favorites = setOf("a", "b")
    println(favorites)
    val sessionId = arrayOf("a")
    println(sessionId.get(0))
    val missingOnServer = favorites - sessionId
    println(missingOnServer)
}

// 結果
[a, b]
a
[b]
```
情報
ドキュメントは[このあたり](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html)でしょうか。

### 処理をざっとまとめると

初回起動でユーザーを作ってUUIDをサーバーに送り、2回目以降はお気に入り、レーティングをローカルと同期しています。（ちょっと荒いですが）


# 参考
* [Kotlin+Androidでasync/await](https://qiita.com/k-kagurazaka@github/items/702c92bc3381af36db12)


