+++
title= "UITextFieldで自動校正しないようにするには"
date= 2020-03-26T00:01:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["iOS"]
tags = ["iOS"]
keywords = [""]
+++

メールアドレス等入力中に、自動校正させたくない場合があった。
（たとえば、勝手に最初の文字を大文字にしようとしてくれて候補を表示していたのを、候補を表示しないようにしたい）

### 方法
AutolayoutのUITextFieldのAttributeのCorrectionをNoに設定する。

### 参考
https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/#//apple_ref/c/tdef/UITextAutocorrectionType

