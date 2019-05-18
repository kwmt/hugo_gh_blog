+++
title= "Pragmatic State Management in Flutter (Google I/O'19)のメモ"
date= 2019-05-18T11:50:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Flutter"]
tags = ["Flutter","Provider","GoogleI/O 2019"]
keywords = [""]
+++

## 問題点

ネットワークから、他のwidgetからシステムコールなどから、widgetの状態変更したい場合は多くあります。


たとえば３つのWidgetがあったとき、MySliderからMyChartの状態を変更する場合、下図のようにしたくなるかもしれませんが、これにはいくつか問題があります。

<img src="/images/2019/05/flutter_state_management/directry-refrence.png"/>


1.widgetが強参照している(strongly coupling widget)

つまりMySliderはMyChartのことを知っている→これは拡張性がなくなります。not very scalable

2.グローバルの状態を持っている

多くのチャートがある場合、同期されてしまいます。

3.外のwidgetからsetStateを呼んでいること
これは最悪
- どこから状態が変わったのか把握できない
- クラッシュする可能性がある



下図は、UI is a functional of stateです。UI=レイアウトは、現在のアプリケーションの状態がビルドメソッドを介して決まります。

<img src="/images/2019/05/flutter_state_management/ui_f_state.png"/>

つまりは、UIは他のUIから変更されません。
findViewByIdのようなものはありません。


## 対策

これを解決するためには、両方のwidgetにアプローチ、アクセスできる何かが必要です。
これを今回はMyScheduleと呼びましょう。

これはMyHomePageにアタッチされます。なぜなら両方のwidgetをしっているから。

MySliderが直接MyChartを触る代わりにMyScheduleを通してMyChartに伝えます。

MySlider -> MyScheduler ->  MyChartに通知notify
MyChart -> MySchedular -> MySliderに変更したよ結果を通知


## コードはどのようになるか？
そのためにGoogleの状態管理アプローチの歴史を見る

- Scoped model

widgetのトップにモデルがあり子孫があるとき、
モデルが変更されたら、それらの子孫は通知される、。

これは使われているが、いくつかの機能が不足している


- BLoC
複雑なアプリが複数あるAdwordsのためにつくられた。
Rxとstremaベース
これは複雑すぎるという意見を聞いた

 あるいは、Flutterをやるにはnewアプローチをしないと


- package:provider
コミュニティベース
これをGoogle内部で使っている
Scopedモデルに近い

## Providerを使うには

まずMyScheduleを定義しましょう。

```dart
import 'package:flutter/widgets.dart';

class MySchedule with ChangeNotifier {
  double _stateManagementTime = 0.5;

  double get stateManagementTime => _stateManagementTime;

  set stateManagementTime(double newValue) {
    _stateManagementTime = newValue;
    notifyListeners();
  }
}
```

このMyScheduleをMyHomePageにアタッチするには、
トップにchangenotifierproviderを次のようにラップします


```dart
ChangeNotifierProvider(
      builder: (context) => MySchedule(),
      child: Scaffold(
```

これでツリーにstateを持つことができた。
ではこのstateにどうやってアクセスするのか？


MySlidetとMyScheduelをつなげる必要がある

- Provider.of

MySliderからMyScheduleに伝える必要があるが、そうするには`Provider.of`を使います。


```dart
final schedule = Provider.of<MySchedule>(context);
return Slider(
    value: schedule.stateManagementTime,
    onchanged: (value) => schedule.stateMangementTime = value
)
```

Provider.ofでMyScheduleインスタンスを取得して、状態の変数をセットします。

onChangeでMySceduleのstateManagermetTimeにセットすることで、MySChedule(のおstateMangementTime)を監視しているオブジェクトに通知されます。


- consumer widget

Consumerで変化を受け取ります。

```dart
  @override
  Widget build(BuildContext context) {
    return Consumer<MySchedule>(
        builder: (context, scheduler, _) =>
            PieChart(dataMap: createData(scheduler.stateManagementTime)));
  }
```

Consumerのbuilderの引数に状態が変更されたあとのMyScheduleインスタンスが渡ってくるので、そのインスタンスから状態を取得しています。

これでMySliderのスライダーを動かすと、チャートが変更されることがわかるかと思います。

セッションのコードを動かしてみたのはこちらにあります。(セッション中のチャートを厳密に再現するのはめんどくさかったので、適当なチャートです。)

https://github.com/kwmt/flutter_state_management/tree/provider



Googleではprovide (scoped model version 2)を公開したが、providerのほうがmuch betterだったのでproviderを使っている

## more real word approach

### diposeable

providerはデフォルトでdisposeできる仕組みをもっている
dispose callback

```dart
Provider<Foo>(
    builder: (context) => Foo(),
    dispose: (context, foo) => foo.dispose(),
)
```

ツリーではdisposeしなくていい、適切にdisposeされる