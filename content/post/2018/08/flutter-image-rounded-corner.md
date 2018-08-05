+++
date = "2018-08-05T21:28:00+09:00"
title = "Flutterで画像を角丸にするには"
draft = false
categories = ["Flutter"]
tags = ["Flutter","角丸"]

+++

<img src="/images/2018/08/flutter-image-rounded-corner/flutter-rounded-corner.png" width="320" />

こんな感じに画像を角丸にするには、`ClipRRect`を使うと良さそう。

```dart
    SizedBox(
        height: 180.0,
        child: Container(
            child: ClipRRect(
                borderRadius: BorderRadius.circular(8.0),
                child: Image.network(
                    message.downloadImageUrl.toString(),
                    fit: BoxFit.cover,
                )),
        ));
```

## 参考

- Rounded Corners Image in Flutter
    - https://stackoverflow.com/a/51514220

