+++
date = "2018-10-13T13:20:00+09:00"
title = "DartのMapを<String, dynamic>に変換するには？"
draft = false
categories = ["Dart"]
tags = ["Flutter","Dart", "Map"]

+++

FlutterでFirestoreに保存する際に、Map(たとえば`Map<String, bool>`)を`<String, dynamic>`に変換する必要があったので、その時のメモ。

```dart
// サンプルデータ
Map<String, bool> members = Map<String, bool>()
  ..putIfAbsent("userID1", () => true)
  ..putIfAbsent("userID2", () => true);
print(members);
// output:
// {userID1: true, userID2: true} 

var transMember = members.entries.map((member) {
  return <String, dynamic>{member.key: member.value};
});
print(transMember);
// output:
// ({userID1: true}, {userID2: true})
```
## Dartpad
動かして確認するには<a href="https://dartpad.dartlang.org/6d48e7bc264ae588d1b083cf6ca3c61a" target="_blank">こちら</a>


