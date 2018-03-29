
+++
categories = ["Android"]
date = "2018-03-29T16:20:00+09:00"
tags = ["Android", "エミュレータ" ,"adb" ]
title = "コマンドからエミュレータ閉じるには？"
+++


## 問題
エミュレータを複数起動している途中でMacが落ちて再起動してしまった。
再起動後いろいろソフトが立上がるが、エミュレータは立ち上がらない（少なくとも見えない）現象になってしまった。

```
% adb devices
List of devices attached
emulator-5558	device
emulator-5556	device
emulator-5554	device
```

adb devicesとするとこのように見えるのだが、実際はエミュレータは見えない。


## 対策
コマンドからエミュレータを閉じるにはどうしたらいいか調べたところ、

```
adb -s emulator-5554 emu kill
```

とすると

```
% adb devices
List of devices attached
```

と閉じてくれた。


以上。

## 参考
* https://stackoverflow.com/a/20155436