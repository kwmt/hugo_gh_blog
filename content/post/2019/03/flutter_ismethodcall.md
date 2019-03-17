+++
title= "Flutterプラグインのテストで正しくMethodCallが呼ばれてるかテストするには"
date= 2019-03-17T00:12:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["flutter"]
tags = ["Flutter","test",'flutter_inapp_purchase"]
keywords = ["matcher","test","flutter"]
description = ""
+++


下記のコードは、[flutter_inapp_purchase](https://pub.dartlang.org/packages/flutter_inapp_purchase)のdartコードの1つのメソッドで、実装はわかりやすいように1つのinvokedMethodに修正したものですが、Flutterプラグインでネイティブのメソッドを呼び出すにはMethodChannelのinvokedMethod(文字列,パラメータ)のような感じで呼び出します。

```
static final MethodChannel _channel = const MethodChannel('flutter_inapp');
static Future<List<IAPItem>> getProducts(List<String> skus) async {
    dynamic result = await _channel.invokeMethod(
        'getItemsByType',
        <String, dynamic>{
        'type': 'inapp',
        'skus': skus,
        },
    );
    return extractItems(result);
}
```

invokedMethodは文字列を渡すので、例えば `'getItemsByType'`が 極端ですが`'get'`とかになっていてもコンパイルできてしまいます。

そこで、`getProducts`が正しく`'getItemsByType'`が呼ばれている確認するには、下記のコードのように[isMethodCall](https://docs.flutter.io/flutter/flutter_test/isMethodCall.html)を使います。

```
test('invokes correct method', () async {
  await FlutterInappPurchase.getProducts(skus);
  expect(log, <Matcher>[
    isMethodCall(
      'getItemsByType',
      arguments: <String, dynamic>{
        'type': 'inapp',
        'skus': skus,
      },
    ),
  ]);
});
```


このようにすると、たとえば先程の例のように`'getItemsByType'`が `get`になっていた場合、テストを実行すると、期待する文字列は`'getItemsByType'`だけど実際は`get`になっているよ、と教えてくれます。

```
Expected: [
            <has method name: 'getItemsByType' with arguments: {'type': 'inapp', 'skus': ['testsku']}>
          ]
  Actual: [MethodCall:MethodCall(get, {type: inapp, skus: [testsku]})]
   Which: does not match has method name: 'getItemsByType' with arguments: {'type': 'inapp', 'skus': ['testsku']} at location [0]
```

