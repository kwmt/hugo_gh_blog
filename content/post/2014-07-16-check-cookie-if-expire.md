---
title: 【Android】【WebView】Cookieの有効期限が切れた場合にどうなるか確認する
author: kwmt
layout: post
date: 2014-07-16
url: /index.php/2014/07/16/check-cookie-if-expire/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - WebView

---
<pre class="go">CookieSyncManager.createInstance(context);

// 'Set-Cookie' HTTP response headerのフォーマットに合わせる
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("EEE, d-MMM-yyyy HH:mm:ss z", Locale.UK);

TimeZone gmt = TimeZone.getTimeZone("GMT");
simpleDateFormat.setTimeZone(gmt);
Calendar calendar = Calendar.getInstance();
calendar.setTimeZone(gmt);

// すぐ確認できる時間を設定(ここでは5秒)
int afterFiveSeconds = calendar.get(Calendar.SECOND) + 5;
calendar.set(Calendar.SECOND, afterFiveSeconds);

// 有効期限の書式を作成して、
String expire = "expires=" + simpleDateFormat.format(calendar.getTime()) + ";";

// 上で作った有効期限を含めたクッキーを保存して、
CookieManager.getInstance().setCookie(&lt;URL&gt;, &lt;'Set-Cookie' HTTP response headerのフォーマット&gt;);

//6秒ぐらい待って、
Thread.sleep(6000); 

// クッキーが削除されているか確認
Log.d(TAG,CookieManager.getInstace().getCookie(&lt;クッキーを保存したURL&gt;));

</pre>