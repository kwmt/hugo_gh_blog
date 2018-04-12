+++
date = "2018-03-12T15:00:00+09:00"
title = "Kotlin List"
draft = false

+++


## 2つのリストの要素を比較し、Mapのkeyをその要素にし、valueを一致していたらtrue,不一致ならfalseとするMapを作るには？

```
/**
 * ２つのリストA,Bがあって、BはAの一部のリストとする。
 * Aの要素とBの要素が一致する場合（今回の場合、idが一致）、
 * その要素をKeyとしてValueをtrueとするMapを作りたい。（今回の場合は、Map<Element, Boolean>)
 */
fun main(args: Array<String>) {
    // データ
	val listA = arrayListOf(Element(1, "a"), Element(2, "b") , Element(3, "c"), Element(4, "d"))   // マスタデータ
    val listB = arrayListOf(Element(2, "a"), Element(4, "d"))   // ユーザーがマスタデータのどれかを選択したというイメージ
    // Bのidのみのリスト(List<Int>)
    val ids = listB.map { it.id }
    println(ids) // [2, 4]
    
    // Aを変換(map)します。このときAのidがBのidに含まれていたら、true　、含まれていなければfalseとするMapに変換します。
    val result = listA.map { it to ids.contains(it.id) }.toMap()
    println(result)
    // output
    // {Element(id=1, name=a)=false, Element(id=2, name=b)=true, Element(id=3, name=c)=false, Element(id=4, name=d)=true}
}

data class Element(
    val id: Int,
    val name : String
)
```

## リストが別のリストに含まれているか調べるには？
```
fun main(args: Array<String>) {
    val caches = arrayListOf(16, 8)
    val target = arrayListOf(16, 3)
    val result = target.filterNot { it in caches }
    println(result)
// output:
// [3]
```
https://try.kotlinlang.org/#/UserProjects/7et0bso059r7fs4t0jv2nmh1fo/m3suq2u3uo48jclbhe65o6n0av


`filterNot`は、falseを返した要素だけ抽出するのがポイント