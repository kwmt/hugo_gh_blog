+++
date = "2017-10-30T11:20:00+09:00"
title = "KotlinでMockito2を使ってfinal classをモック化するには"
draft = false
categories = ["Android"]
tags = ["Kotlin", "Mockito2"]

+++

## 環境

Android Studio: 2.3.3
Kotlin: 1.1.51
Mokito: 2.11.0

build.gradleには`mockito-core`を指定

```build.gradle
testCompile "org.mockito:mockito-core:2.11.0"
```

## 問題

kotlinで普通にクラスを作成すると、final classです。


```
class EventsDataSourceManager @Inject constructor(private val eventsRemoteDataSource: EventsRemoteDataSource) {

    fun eventEntities(user:String, page:Int): Single<List<EventEntity>> {
        // リモートかキャッシュを使うかの判断をココでやるとよさそう
        return eventsRemoteDataSource.eventEntities(user, page)
    }
}

```
その状態でmockitoを使って、モック化しようとすると、「Mockitoはfinal classはモック化できない」とエラーにになります。


```
org.mockito.exceptions.base.MockitoException: 
Cannot mock/spy class net.kwmt27.codesearch.infrastructure.repository.datesource.EventsDataSourceManager
Mockito cannot mock/spy because :
 - final class
```

## 対策

これを可能にするには、

```
test/resources/mockito-extensions
```
フォルダを作成し、`mockito-extensions`フォルダに、`org.mockito.plugins.MockMaker`ファイルを作成します。
`org.mockito.plugins.MockMaker`ファイルには、下記のテキストを入力しておきます。

```
mock-maker-inline
```

この状態でもう一度テストを実行します。
すると、テストが実行されます。

## 参考

* [How to mock final classes on Kotlin using Mockito 2 (KAD 23)](https://antonioleiva.com/mockito-2-kotlin/)
* [Mock the unmockable: opt-in mocking of final classes/methods](https://github.com/mockito/mockito/wiki/What%27s-new-in-Mockito-2#unmockable)(公式wiki)
