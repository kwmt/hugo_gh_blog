+++
title= "KotlinのDelegateについて"
date= 2019-08-18T18:50:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Kotlin"]
tags = ["Android","Kotlin","GoogleI/O 2019","Delegate"]
keywords = [""]
+++

## はじめに

Google I/O 2019のソースが[公開された](https://twitter.com/AndroidDev/status/1161704045482041344?s=20)のでさっそく読み始めたところ、下記のコードが気になった。
KotlinのDelegated propertyであることは知っていたが、あんまり詳しく知らなかったので、今回はDelegateについて調べてみたので、それについてのメモです。

```kotlin
override var onboardingCompleted by BooleanPreference(prefs, PREF_ONBOARDING, false)
```

ちなみに、試したコードはこちらです。

https://github.com/kwmt/Kotlin-Playground/tree/master/src/main/kotlin/delegation

## まずはDelegationの基本について

Javaでは処理をクラスに委譲するには次のようにやっていたと思います。(下記サンプルはJavaではないが、Javaで書くならこんな感じになる)

```kotlin
package delegation

interface Nameble {
    var name: String
}

class JackName : Nameble {
    override var name: String = "Jack"
}

class Person(name: Nameble) : Nameble {
    override var name: String = name.name
}

fun main() {
    val person = Person(JackName())
    println(person.name)
}
```

つまり、Personクラスの`name`をNamebleの`name`に委譲したい場合、

```kotlin
override var name: String = name.name
```

と書かないといけません。

しかし、kotlinのdelegationを使うと、次のように書けます。
 

```kotlin
package delegation

interface Nameble {
    var name: String
}

class JackName : Nameble {
    override var name: String = "Jack"
}

class Person(name: Nameble) : Nameble by name

fun main() {
    val person = Person(JackName())
    println(person.name)
}
```

この部分が変わりました。

```kotlin
class Person(name: Nameble) : Nameble by name
```

overrideを書く代わりに、`by name` となっていることがポイントです。これで

```kotlin
override var name: String = name.name
```

を省略できます。実際Javaにデコンパイルしてみると次のようになっていました。
Metadataとimport文とcheck文(Intrinsics.checkParameterIsNotNull)は省略しています。


```java
// Nameble.java
public interface Nameble {
   @NotNull
   String getName();

   void setName(@NotNull String var1);
}
// Person.java
public final class Person implements Nameble {
   @NotNull
   private String name;

   @NotNull
   public String getName() {
      return this.name;
   }

   public void setName(@NotNull String var1) {
      this.name = var1;
   }

   public Person(@NotNull Nameble name) {
      super();
      this.name = name.getName();
   }
}
// JackName.java
public final class JackName implements Nameble {
   @NotNull
   private String name = "Jack";

   @NotNull
   public String getName() {
      return this.name;
   }

   public void setName(@NotNull String var1) {
      this.name = var1;
   }
}
// ClassDelegationKt.java
public final class ClassDelegationKt {
   public static final void main() {
      Person person = new Person((Nameble)(new JackName()));
      String var1 = person.getName();
      boolean var2 = false;
      System.out.println(var1);
   }

   // $FF: synthetic method
   public static void main(String[] var0) {
      main();
   }
}
```


デコンパイル後のコードはjavaでDelegationを使わなかった方はkotlinですが、やっていることは一緒であることが分かると思います。


## プロパティDelegate

```kotlin
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name}' in $thisRef.")
    }
}
```

Delegateクラスをこのように定義して、


```kotlin
class Example {
    var someName by Delegate()
}
```
として、

```kotlin
fun main() {
    val example = Example()
    println(example.p)
    example.p = "NEW"
}
```

このようにすると、次にように出力され、`p`を参照時に、`Delegate#getValue`が呼ばれます。

```
delegation.Example@5c3bd550, thank you for delegating 'p' to me!
NEW has been assigned to 'p' in delegation.Example@5c3bd550.
```

## デリゲートの標準ライブラリ

### <a href="https://kotlinlang.org/docs/reference/delegated-properties.html#lazy" target="_blank">lazy</a>

lazy()はラムダを取り、`Lazy<T>`インスタンスを返す関数です。最初のget()の呼び出しはlazy()に渡されたラムダを実行し、結果を記憶します。その後のget()の呼び出しは記録したした結果を返すだけです。

```kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main() {
    println(lazyValue)
    println(lazyValue)
}
// computed!
// Hello
// Hello
```


デフォルトでは、lazyプロパティの評価は同期されます。値は1つのスレッドでのみ計算され、すべてのスレッドは同じ値を参照します。

### <a href="https://kotlinlang.org/docs/reference/delegated-properties.html#observable" target="_blank">observable</a>

Delegates.observable（）は、初期値と変更用のハンドラーの2つの引数を取ります。 ハンドラーは、プロパティに割り当てるたびに（割り当てが実行された後）呼び出されます。 割り当てられるプロパティ、古い値、新しい値の3つのパラメーターがあります。(Google翻訳そのまま・・・)


```kotlin
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") { property, oldValue, newValue ->
        println("$oldValue->$newValue")
    }
}

fun main() {
    val user = User()
    user.name = "first"
    user.name = "second"
}
// <no name>
// <no name>->first
// first
// first->second
// second
```

### <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/vetoable.html" target="_blank">vetoable</a>

もし、newValueに制限を掛けたい場合、`observable`の代わりに`vetoable`を使用すると良さそうとのことなので見てみます。

```kotlin
import kotlin.properties.Delegates

var max: Int by Delegates.vetoable(0) { property, oldValue, newValue ->
    newValue > oldValue
}

fun main() {
    println(max)
    max = 10
    println(max)
    max = 5
    println(max)
}
// 0
// 10
// 10
```

なるほど常に新しい値ではないと更新させないようにしたい場合は、こう書けるんですね。

次の例は、現在のあたりより古いをセットしようとしたとき、IllegalArgumentExceptionを投げる例。

```kotlin
fun main() {
    var max: Int by Delegates.vetoable(0) { property, oldValue, newValue ->
        if (newValue > oldValue) true else throw IllegalArgumentException("New value must be larger than old value.")
    }
    println(max)
    max = 10
    println(max)
    max = 5
}
// 0
// 10
// Exception in thread "main" java.lang.IllegalArgumentException: New value must be // larger than old value.
// 	at delegation.Vetoable2Kt$main$$inlined$vetoable$1.beforeChange(Delegates.kt:71)
// 	at kotlin.properties.ObservableProperty.setValue(ObservableProperty.kt:37)
// 	at delegation.Vetoable2Kt.main(vetoable2.kt:14)
// 	at delegation.Vetoable2Kt.main(vetoable2.kt)
```

ちなみに、`veto`とは「禁止する」「拒否する」の意味とのこと。初めて知った。

### <a href="https://kotlinlang.org/docs/reference/delegated-properties.html#storing-properties-in-a-map" target="_blank">Storing Properties in a Map</a>

一般的な使用例の1つは、マップにプロパティの値を保存することです。 これは、JSONの解析やその他の「動的な」処理などのアプリケーションで頻繁に発生します。 この場合、マップインスタンス自体を委任プロパティのデリゲートとして使用できます。


```kotlin
fun main() {
    class User(map: Map<String, Any?>) {
        val name: String by map
        val age: Int by map
    }

    val user = User(
        mapOf(
            "name" to "John Doe",
            "age" to 25
        )
    )

    println(user.name)
    println(user.age)

    println(user.name.javaClass)
    println(user.age.javaClass)
}
// John Doe
// 25
// class java.lang.String
// int
```

コード見て、どうなってるのか初見ではわからなかったので、デコンパイルしてみました。

```java
public final class StoreInMapKt {
   public static final void main() {
      final class User {
         // $FF: synthetic field
         static final KProperty[] $$delegatedProperties = new KProperty[]{(KProperty)Reflection.property1(new PropertyReference1Impl(Reflection.getOrCreateKotlinClass(User.class), "name", "getName()Ljava/lang/String;")), (KProperty)Reflection.property1(new PropertyReference1Impl(Reflection.getOrCreateKotlinClass(User.class), "age", "getAge()I"))};
         @NotNull
         private final Map name$delegate;
         @NotNull
         private final Map age$delegate;

         @NotNull
         public final String getName() {
            Map var1 = this.name$delegate;
            KProperty var3 = $$delegatedProperties[0];
            boolean var4 = false;
            return (String)MapsKt.getOrImplicitDefaultNullable(var1, var3.getName());
         }

         public final int getAge() {
            Map var1 = this.age$delegate;
            KProperty var3 = $$delegatedProperties[1];
            boolean var4 = false;
            return ((Number)MapsKt.getOrImplicitDefaultNullable(var1, var3.getName())).intValue();
         }

         public User(@NotNull Map map) {
            Intrinsics.checkParameterIsNotNull(map, "map");
            super();
            this.name$delegate = map;
            this.age$delegate = map;
         }
      }

      User user = new User(MapsKt.mapOf(new Pair[]{TuplesKt.to("name", "John Doe"), TuplesKt.to("age", 25)}));
      String var1 = user.getName();
      boolean var2 = false;
      System.out.println(var1);
      int var3 = user.getAge();
      var2 = false;
      System.out.println(var3);
      Class var4 = user.getName().getClass();
      var2 = false;
      System.out.println(var4);
      user.getAge();
      var4 = Integer.TYPE;
      var2 = false;
      System.out.println(var4);
   }

   // $FF: synthetic method
   public static void main(String[] var0) {
      main();
   }
}
```

metadataは削除しましたが、このようになります。
Userクラスは、フィールドにStringやIntではなく、それぞれMap型のフィールドを持たせていました。

```java
private final Map name$delegate;
private final Map age$delegate;
```

そして、getNameメソッドがそれらのフィールドから取得するようにしているようでした。

```java
public final String getName() {
   Map var1 = this.name$delegate;
   KProperty var3 = $$delegatedProperties[0];
   boolean var4 = false;
   return (String)MapsKt.getOrImplicitDefaultNullable(var1, var3.getName());
}
```

すごいなと思ったのはMap<String, Any?>とvalueをAny?型にしているのに、型を確認したらAny?型ではなく、それぞれセットした型で取得できているので、ビックリした(リフレクション使っているので当たり前かもしれないが・・・)。


### <a href="https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/not-null.html" target="_blank">notNull</a>


オブジェクトの構築時ではなく、後で初期化されるnull以外の値を持つ読み取り/書き込みプロパティのプロパティデリゲートを返します。 初期値が割り当てられる前にプロパティを読み取ろうとすると、例外が発生します。

```kotlin
fun main() {
    var max: Int by Delegates.notNull()
    println(max)
}
// Exception in thread "main" java.lang.IllegalStateException: Property max should be initialized before get.
// 	at kotlin.properties.NotNullVar.getValue(Delegates.kt:62)
// 	at delegation.NotnullKt.main(notnull.kt:10)
// 	at delegation.NotnullKt.main(notnull.kt)
```


```kotlin
fun main() {
    var max: Int by Delegates.notNull()
    max = 10
    println(max)
}
// 10
```



lateinitと何が違うのかと思って試してみたら、
- notnullは関数スコープで使えるがlateinitは使えない。
- notnullはプリミティブ型でも可能だが、lateinitはプリミティブ型では使えない。
ようだ。


### provideDelegate

バージョン1.1以降、関数本体内のDelegatedプロパティと、デリゲートを提供する前にプロパティをチェックできるprovideDelegate演算子を使用できます。


下記は、クラスのプロパティ名が4文字より大きいかどうかをチェックしています。

```kotlin
class StringProvider {
    operator fun provideDelegate(
        thisRef: Main,
        prop: KProperty<*>
    ): ReadOnlyProperty<Main, String> = if (checkProperty(thisRef, prop.name)) {
        object : ReadOnlyProperty<Main, String> {
            override fun getValue(thisRef: Main, property: KProperty<*>): String {
                return "true string"
            }
        }
    } else {
        object : ReadOnlyProperty<Main, String> {
            override fun getValue(thisRef: Main, property: KProperty<*>): String {
                return "false string"
            }
        }
    }

    private fun checkProperty(thisRef: Main, name: String) = name.length > 4
}

fun bindString() = StringProvider()

class Main {
    val fake by bindString()
    val valid by bindString()
}

fun main(args: Array<String>) {
    Main().apply {
        println(fake) //false string
        println(valid) //true string
    }
}
```

## 参考

- [Delegated Properties - Kotlin Programming Language](https://kotlinlang.org/docs/reference/delegated-properties.html)
- [Delegation in Kotlin](https://proandroiddev.com/delegation-in-kotlin-e1efb849641)
- [google/ioschedのBooleanPreferenceの使用箇所](https://github.com/google/iosched/blob/master/shared/src/main/java/com/google/samples/apps/iosched/shared/data/prefs/PreferenceStorage.kt#L76)

