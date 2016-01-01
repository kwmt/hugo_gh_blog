---
title: 【Android】久しぶりにAndroidアプリった。
author: kwmt
layout: post
date: 2013-03-11
url: /index.php/2013/03/11/【android】久しぶりにandroidアプリった。/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - Android

---
<span style="font-size: x-small;">201302-03でやったキーワード</span>

久しぶりにAndroidアプリった。こんなに真剣にやったのはたぶん1年ぶりぐらい。
  
ググったら日本語の情報がわんさか出てきたことに感動。
  
ということで、基本的にはググればわかるので、ここでは今回やったキーワードだけ残しておくメモです。

## トグルボタンを使うには？ {.section}

<pre class="go">android.widget.ToggleButton</pre>

OnCheckedChangeListener　をimplementsして、以下のonCheckedChangedをoverride

<pre class="brush: java; title: ; notranslate" title="">// OnCheckedChangeListenerをimplementした時に実装が必要なメソッド
// トグルボタン用
@Override
public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
if (isChecked){
//ON
} else{
//OFF
}
}
</pre>

## プリファレンスは？ {.section}

<pre class="go">android.content.SharedPreferences
android.preference.PreferenceActivity</pre>

## 音声認識させるには？ {.section}

### 標準ダイアログが出る

<pre class="go">android.speech.RecognizerIntent</pre>

### 標準ダイアログを出さない

<pre class="go">android.speech.SpeechRecognizer</pre>

※以下のパーミッションが必要

<pre class="go">&lt;uses-permission android:name="android.permission.RECORD_AUDIO"/&gt;
</pre>

※ダイアログは出ないが、ピコっていう音はなる。

## 一定間隔で処理するには？ {.section}

<pre class="go">java.util.Timer
java.util.TimerTask</pre>

## ウィジェットを実装するには？ {.section}

<pre class="go">android.appwidget.AppWidgetManager　を継承したクラス(ここでWidgetProvider)を作成
onUpdateでServiceを継承したクラス(ここではWidgetService)をstartService</pre>

このとき、マニフェストファイルにこんな感じ書いとく

<pre class="brush: xml; title: ; notranslate" title="">&lt;receiver&gt;
android:name=&quot;.WidgetProvider&quot;
android:label=&quot;@string/app_name&quot; &gt;
&lt;intent-filter&gt;
&lt;action android:name=&quot;android.appwidget.action.APPWIDGET_UPDATE&quot; /&gt;
&lt;/intent-filter&gt;
&lt;meta-data
android:name=&quot;android.appwidget.provider&quot;
android:resource=&quot;@xml/widget_provider&quot; /&gt;
&lt;/receiver&gt;
&lt;service android:name=&quot;.WidgetService&quot; &gt;
&lt;intent-filter&gt;
&lt;action android:name=&quot;com.example.myproject.HOGEHOGE&quot;/&gt;
&lt;/intent-filter&gt;
&lt;/service&gt;
</pre>

サービスタグの は、ウィジェットに対してクリックイベントを取りたいときに必要。
  
作成中の自分のプロジェクト（myproject）の中で自分でHOGEHOGEを定義しておき、「HOGEHOGEになったら、ある処理をする」という具合に実装する。

## 画像を繰り返すには？ {.section}

<pre class="go">android:tileMode="repeat"</pre>

## 最後に {.section}

上記をキーワードにググれば分かるはず。と自分に言い残してみる。
  
参考にしたURLをメモってれば良かったけど、まぁいっか。