+++
title= "SwiftUIでGoogleMapsを表示するには"
date= 2019-10-06T02:30:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["iOS"]
tags = ["SwiftUI","Swift","GoogleMap"]
keywords = [""]
+++

SwiftUIでGoogleMapを表示する方法を調べたのでメモします。

## GoogleMapView.swiftを作成

{{< gist 1df17577c59e33da1c4c2eb982f21bbf "GoogleMapView.swift" >}}



### 補足

[UIViewRepresentable](https://developer.apple.com/documentation/swiftui/uiviewrepresentable)はUIKitを表すViewです。

`makeUIView`と`updateUIView`は必ず実装する必要があります。

```swift
func makeUIView(context: Self.Context) -> Self.UIViewType
```

は、表示するUIKitビューを作成します。

```swift
func updateUIView(Self.UIViewType, context: Self.Context)
```

は、提示されたUIKitビュー（およびその座標）を最新に更新します。

これからもわかるように、`makeUIView`から`updateUIView`の順で呼ばれます。

## 作成したGoogleMapViewを表示したい場所に挿入


{{< gist 1df17577c59e33da1c4c2eb982f21bbf "ContentView.swift" >}}

これだけです。


## 完成

<img src="/images/2019/10/googlemap-on-swiftui/googlemap-swiftui.png" width="300">

## 補足

もちろん、Google MapsのAPIの有効化やAPIキーの設定は必要です。

- GoogleMapsの設定に関してはこちらを参照
    - [Get Started  |  Maps SDK for iOS  |  Google Developers](https://developers.google.com/maps/documentation/ios-sdk/start)
