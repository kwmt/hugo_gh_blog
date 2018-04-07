+++
date = "2017-10-19T22:36:00+09:00"
title = "Kotlin Enum"
draft = false
categories = ["kotlin"]

+++

## はじめに

KotlinでEnumを定義して、IntからEnumに変換したいときどうしたらいいのかをメモ。

## Javaの場合

ItemTypeというのをこんな感じで定義して、staticなvalueOfメソッドで、ItemTypeのintが見つかればItemTypeを返していた。

```java
public enum ItemType {

    Normal(0),
    Progress(1),
    Ad(2);

    private int typeId;

    ItemType(int typeId) {
        this.typeId = typeId;
    }

    public int getTypeId() {
        return typeId;
    }

    public static ItemType valueOf(int id) {
        for (ItemType itemType : values()) {
            if (itemType.getTypeId() == id) {
                return itemType;
            }
        }
        throw new IllegalArgumentException("no such enum object for the id: " + id);
    }
}
```

## Kotlin

Kotlinでも同様にしてやればよい。

```kotlin
enum class ItemType(val typeId:Int) {
    Normal(0),
    Progress(1),
    Ad(2);

    companion object {
        fun valueOf(typeId:Int) :ItemType {
            val filtered = ItemType.values().filter {it.typeId == typeId}.firstOrNull()
            return filtered ?: throw IllegalArgumentException("no such enum object for the typeId: " + typeId)
        }
    }
}

fun main(args: Array<String>) {
    val inRangeId = 1
    val inRangeItem = ItemType.valueOf(inRangeId)
    println(inRangeItem)

    val outOfRangeId = 4
    val outOfRangeItem = ItemType.valueOf(outOfRangeId)
    println(outOfRangeItem)
}
```

実行結果はこんな感じ。
```
Progress

Exception in thread "main" java.lang.IllegalArgumentException: no such enum object for the typeId: 4
    at ItemType$Companion.valueOf(Simplest version.kt:29)
    at Simplest_versionKt.main(Simplest version.kt:12)
```

ちなみに、`companion object`はjavaでいうところ`static`のように扱うやつ。
