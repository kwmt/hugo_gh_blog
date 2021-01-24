
+++
title= "Jetpack Composeを既存のアプリと共存するには"
date= 2021-01-16T22:50:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose"]
keywords = ["Android", "Jetpack", "Compose"]
+++

# はじめに

Jetpack Composeでステータスバーのカラーを変更するにはどうすればいいか調べていたら、[`ambient`というキーワードが出てきて](https://github.com/android/compose-samples/blob/main/Jetsnack/app/src/main/java/com/example/jetsnack/ui/utils/SystemUi.kt#L147)、なにものかがわからなかったのでメモです。

## Ambientとは

[Ambinent](https://developer.android.com/reference/kotlin/androidx/compose/runtime/Ambient)とは、Flutterの[Provider](https://pub.dev/packages/provider)のようなものです。

サンプルコードを見たほうがわかりやすいと思いますので、見てみましょう。

```
// ① Ambientを作ります。
val ActiveUser = ambientOf<User> { error("No active user found!") }

@Composable
fun App(user: User) {
    // ② `Providers`コンポーネントを使用して、ambinentに値を渡します。
    Providers(ActiveUser provides user) {
        SomeScreen()
    }
}

// ③ツリーの間にあるコンポーネントはAmbientの値を知る必要はありません。
@Composable
fun SomeScreen() {
    UserPhoto()
}

@Composable
fun UserPhoto() {
    // ④ `current`プロパティを使うことでAmbinentの値を使うことができます。
    val user = ActiveUser.current
    ProfileIcon(src = user.profilePhotoUrl)
}
```

もしAmbientを使わなければ、コンポーネントに引数で渡していくことになり、渡された値が必要のないコンポーネントまで余計なものを知る必要が出てきてしまいます。


## ambientOfとstaticAmbinentOfの違いについて

[Jetpack Compose - What is the Difference Between ambientOf and staticAmbientOf](https://lcdsmao.dev/jetpack-compose-what-is-the-difference-between-ambient-and-static-ambient/) こちらが詳しいです。

[`ambientOf`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#ambientof)
[`staticAmbientOf`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#staticambientof)

テーマの変更、端末の言語設定など変更機会が少なく、読み込みが書き込みより多い場合に `staticAmbientOf`を使うそうです。

> staticAmbientOfでは、すべての子プロセスを再構成しますので、アンビエントを変更する際にコストがかかります（フレームが落ちる可能性もあります）。 
> ambientOfは、ターゲットを絞ったアップデートを行うため、再構成が速くなりますが、初期ツリーの構築や維持にコストがかかるため、アンビエントが変化しない場合でも、アプリ全体の動作が遅くなります。 
> staticAmbientOfのほうが一般的かもしれない。なぜなら、ambientの値となるものの多くは、多くのノードで消費されるはずで、多くのノードで消費されるものは、経験的にあまり変化しないと思うから（たとえば、端末の言語設定）

[slack](https://kotlinlang.slack.com/archives/CJLTWPH7S/p1604408816280700?thread_ts=1604395068.273700&cid=CJLTWPH7S)から翻訳して引用

## 参考
[Ambient  |  Android デベロッパー  |  Android Developers](https://developer.android.com/reference/kotlin/androidx/compose/runtime/Ambient)
[Jetpack Compose - What is the Difference Between ambientOf and staticAmbientOf](https://lcdsmao.dev/jetpack-compose-what-is-the-difference-between-ambient-and-static-ambient/)