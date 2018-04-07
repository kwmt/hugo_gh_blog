+++
date = "2018-04-07T11:00:00+09:00"
title = "Kotlin When"
draft = false
+++

## kotlin when


```kotlin
fun main(args: Array<String>) {
    
    val x = 11
    when (x) {
        in 1..10 -> print("x is in the range")
        !in 10..20 -> print("x is outside the range")
        else -> print("none of the above")
    }
}
// output
// x=1の時`x is in the range`
// x=11の時 `none of the above`
// x=21の時 `x is outside the range`
```
    	
