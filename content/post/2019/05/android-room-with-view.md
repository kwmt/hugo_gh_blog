
+++
title= "Android Room with a View - KotlinというCodelabをやってみた"
date= 2019-05-04T10:50:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Room"]
keywords = ["Room"]
+++

<!--more-->

[Android Room with a View - Kotlin](https://codelabs.developers.google.com/codelabs/android-room-with-a-view-kotlin)というコードラボをやったときのメモです。


## Room データベースとは

> Room uses the DAO to issue queries to its database.

Roomはデータベースへのクエリを発行するためにDAOを使います。

> By default, to avoid poor UI performance, Room doesn't allow you to issue queries on the main thread. When Room queries return LiveData, the queries are automatically runasynchronously on a background thread.

https://codelabs.developers.google.com/codelabs/android-room-with-a-view-kotlin/#6

デフォルトではRoomはメインスレッドでクエリできません。
RoomがLiveDataを返すとき、クエリは自動的にバックグラウンドで非同期で実行さｆれます。

> Room provides compile-time checks of SQLite statements.


RoomはSQLiteステートメントをコンパイル時にチェックします。

## Room データベースを実装するために

- `RoomDatabase`を継承
- 継承したクラスは`abstract`でなければならない
- 通常、Roomデータベースインスタンスはアプリ全体で1つだけ。

## Repository


>  Repository implements the logic for deciding whether to fetch data from a network or use results cached in a local database.

Repositoryはネットワーク上からデータを取得するか、ローカルデータベースにあるキャッシュされたデータを使うかを決定するためのロジックを実装します。


> Add a wrapper for the insert() method. You must call this on a non-UI thread or your app will crash. Room ensures that you don't do any long-running operations on the main thread, blocking the UI.

insertメソッドは、UIスレッド上で呼んではいけません。でないとアプリはクラッシュします。
RoomはUIをブロックするメインスレッド上で長い時間の操作を行わないことを保証しています。

> Add the @WorkerThread annotation, to mark that this method needs to be called from a non-UI thread. Add the suspend modifier to tell the compiler that this needs to be called from a coroutine or another suspending function.

`@WorkerThread`アノテーションはnon-UIスレッド上で呼ぶ必要があります。
`suspend`修飾子は、コルーチンまたは他のsuspend関数から呼ばれる必要があることをコンパイラに伝えます。


## リポジトリ

https://github.com/kwmt/RoomWordSample