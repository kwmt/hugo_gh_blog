
+++
title= "Jetpack Composeでインクリメンタルサーチ(SearchBar UI)を実装するには"
date= 2021-05-15T13:20:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","Room"]
keywords = ["Android", "Jetpack", "Compose","SearchBar","インクリメンタルサーチ","インクリメンタル検索","検索バー"]
+++

# はじめに

composeバージョンは 1.0.0-beta04 です。

検索入力欄に検索した文字列を入れるとリストが絞り込まれる、いわゆるインクリメンタルサーチを実装したいとおもいます。下の動画のようなイメージです。

<img src="/images/2021/05/jetpack-compose-searchbar/qr_searchbar.gif" />


## SearchBarのUIを作るには？

{{< gist 6fbecb46bd621a451e28e1095f24f541#file-searchbar-kt >}}

だいたいこんな感じで作れます。

## ROOMで部分検索するには

```
@Dao
interface QrResultDao {

    @Query("SELECT * from qr_results WHERE text LIKE '%' || :query || '%' " +
            "OR name LIKE '%' || :query || '%' ORDER BY updated_at DESC")
    suspend fun search(query: String): List<QrResultEntity>
}
```


こんな感じ。


## ViewModelの実装は

{{< gist 6fbecb46bd621a451e28e1095f24f541#file-qrhistoryviewmodel-kt >}}

SearchQrResultUseCaseを使ってますが、基本的にはDaoのsearchまでの橋渡しなので省略。型変換とかはしています。

## SearchBarとリストをくっつけると

{{< gist 6fbecb46bd621a451e28e1095f24f541#file-qrhistoryscreen-kt >}}


listを監視して、検索されたらUIに通知するみたいな感じです。

以上です。