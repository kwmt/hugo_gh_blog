
+++
date = "2018-11-27T21:50:00+09:00"
title = "DartのfirstWhere"
draft = false
categories = ["Dart"]
tags = ["Flutter","Dart"]

+++

下記のような感じで書いていて、条件に一致しない場合は、

```dart
members.firstWhere((member) => _lastMessage.fromUserId == member.userId);
```

> Bad state: No element

と出ます。



仕様にも書いてました。

```dart
  /**
   * Returns the first element that satisfies the given predicate [test].
   *
   * Iterates through elements and returns the first to satisfy [test].
   *
   * If no element satisfies [test], the result of invoking the [orElse]
   * function is returned.
   * If [orElse] is omitted, it defaults to throwing a [StateError].
   */
  E firstWhere(bool test(E element), {E orElse()}) {
    for (E element in this) {
      if (test(element)) return element;
    }
    if (orElse != null) return orElse();
    throw IterableElementError.noElement();
  }
```