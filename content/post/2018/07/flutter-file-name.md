+++
date = "2018-07-25T01:00:00+09:00"
title = "FlutterでFileのファイル名を取得するには"
draft = false
categories = ["Flutter"]
tags = ["Flutter"]

+++

pathパッケージをimportして、

```dart
import 'package:path/path.dart';
```

basenameを使う。

```dart
File file = File("dir/name.png");
print(basename(file.path));
/// output:
/// name.png
```



## 参考

- https://stackoverflow.com/a/50439988
- https://pub.dartlang.org/documentation/path/1.3.6//path/path-library.html