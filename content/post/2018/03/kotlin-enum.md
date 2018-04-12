+++
date = "2018-03-08T22:56:51+09:00"
title = "Kotlin Enum"
draft = false

+++

## 定義されたEnum名そのままの文字列がほしい

```kotlin
enum class Type {
    Hoge,
    fugA
}
```

このようなEnumがあったとして、Hogeなら`Hoge`、fugAなら`fugA`というそのままの文字列がほしいとき、`name`を使います。

```kotlin
println(Type.Hoge.name)
println(Type.fugA.name)
// output:
// Hoge
// fugA
```

## StringからEnumにしたい

```kotlin
enum class Type {
    Hoge
}
```

このようなEnumがあったとして、String `"Hoge"`から、Enum(Type)型の `Hoge` にしたい。

```kotlin
val type = Type.valueOf("Hoge")
println(type is Type.Hoge)
// output:
// true
```

ちなみに、下記のように、`"hoge"`などとType.Hogeとは違う文字列で`valueOf`をしようとすると、

```kotlin
val type = Type.valueOf("hoge")
```

`IllegalArgumentException`がスローされます。

```
Exception in thread "main" java.lang.IllegalArgumentException: No enum constant Type.hoge
    at java.lang.Enum.valueOf(Enum.java:238)
```

## 小文字からEnum型にしたい


```kotlin
enum class Type {
    Hoge
}
```

このようなEnumがあるとき、小文字の`"hoge"`からEnumに変換したいときがあります。
たとえば、スキームなどをEnumで定義するも、Enumは大文字始まりで定義すべきと警告がでるので、大文字で定義しますが、スキーマが小文字始まりだった場合などです。

この場合、次のように`typeOf`メソッドを用意すると良さそうです。

```kotlin
enum class Type() {
    Hoge;
    companion object {
        fun typeOf(lowerString: String): Type {
            return values().first { it.name.toLowerCase() == lowerString }
        }
    }
}
```

使い方は次のようにします。

```kotlin
val type = Type.typeOf("hoge")
println(type is Type.Hoge)
// output:
// true
```

## 複数のEnumで小文字からEnum型にしたい

複数のEnumで小文字からEnumに変換したい場合、すぐ思いつくのは、いかの用にTypeA,TypeBそれぞれのEnumにtypeOfメソッドを定義する方法です。

```
enum class TypeA {
    Hoge;
    
    companion object {
           fun typeOf(lowerString: String): TypeA {
            return values().first { it.name.toLowerCase() == lowerString }
        }
    }
}

enum class TypeB {
    Fuga;
    
    companion object {
           fun typeOf(lowerString: String): TypeB {
            return values().first { it.name.toLowerCase() == lowerString }
        }
    }
}
```

Enumは継承できないし、仕方ないです。と思って調べていたら、`sealed class`がもしかしたら使えるかも？と思い、やってみたところで来たのでメモ。



## Enum.values().first{condition}としたとき条件が一致しなかったらNoSuchElementExceptionでるときの対処

try catchしたくなるのをぐっとこらえて、`firstOrNull`で解決。存在しない場合は`null`としているならこうすると良さそう。そのかわり、nullableになってしまい、使うところでnullチェックが必要になりますが。

```
fun main(args: Array<String>) {
    val type = "bar".let { SampleType.typeOf(it.toLowerCase()) }
    print(type)
}
enum class SampleType {
    Hoge,
    Fuga;

    companion object {
        fun typeOf(lowerString: String): SampleType? {
            return values().firstOrNull { it.name.toLowerCase() == lowerString }
        }
    }
}

// output:
// null
```


## Any?の型情報を知るには？

```
fun main(args: Array<String>) {
    val any:Any? = Hoge("2")
    println(any?.javaClass?.kotlin.toString())
}
data class Hoge(val value:String) 

// output:
// class Hoge
```