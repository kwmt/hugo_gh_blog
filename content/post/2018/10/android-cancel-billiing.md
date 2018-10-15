+++
date = "2018-10-15T09:45:00+09:00"
title = "Androidのアプリ内課金のテスト購入して取り消すには？"
draft = false
categories = ["Android"]
tags = ["Android","Google Play", "Billing", "アプリ内課金"]

+++

## 問題

Androidのアプリ内課金(定期購入ではない方)をテストしていて、一度購入して、再度購入テストしようとすると、
[ITEM_ALREADY_OWNED](https://developer.android.com/reference/com/android/billingclient/api/BillingClient.BillingResponse#item_already_owned)
とすでに購入済みというエラーが出て購入テストができません。

ちなみにここでのテスト購入時のプロダクトIDは、たとえば[`android.test.purchased`](https://developer.android.com/google/play/billing/billing_testing#billing-testing-static)
です。
これで購入しようとすると、購入したとみなしてレスポンスを返してくれます。

## 対策

で、本題ですが、購入を取り消すには次の手順で取り消すことができました。

1. アプリをアンインストール
2. `% adb shell pm clear com.android.vending` を実行


## 注意

アプリをアンインストールせずに、上記2番のコマンドを叩くだけだと、うまくいかないようです。

## 参考

https://stackoverflow.com/a/30178861