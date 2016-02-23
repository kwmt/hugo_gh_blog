---
title: 【Android】久しぶりにAndroidアプリった。
author: kwmt
layout: post
date: 2013-03-11
url: /index.php/2013/03/11/android久しぶりにandroidアプリった/
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

## トグルボタンを使うには？

<pre class="go">android.widget.ToggleButton</pre>

OnCheckedChangeListener　をimplementsして、以下のonCheckedChangedをoverride

```
// OnCheckedChangeListenerをimplementした時に実装が必要なメソッド
// トグルボタン用
@Override
public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
	if (isChecked){
		//ON
	} else{
		//OFF
	}
}
```

## プリファレンスは？

<pre class="go">android.content.SharedPreferences
android.preference.PreferenceActivity</pre>

## 音声認識させるには？

### 標準ダイアログが出る

<pre class="go">android.speech.RecognizerIntent</pre>

### 標準ダイアログを出さない

<pre class="go">android.speech.SpeechRecognizer</pre>

※以下のパーミッションが必要

<pre class="go">&lt;uses-permission android:name="android.permission.RECORD_AUDIO"/&gt;
</pre>

※ダイアログは出ないが、ピコっていう音はなる。

## 一定間隔で処理するには？

<pre class="go">java.util.Timer
java.util.TimerTask</pre>

## ウィジェットを実装するには？

<pre class="go">android.appwidget.AppWidgetManager　を継承したクラス(ここでWidgetProvider)を作成
onUpdateでServiceを継承したクラス(ここではWidgetService)をstartService</pre>

このとき、マニフェストファイルにこんな感じ書いとく

```
<receiver>
	android:name=".WidgetProvider"
	android:label="@string/app_name" >
	<intent-filter>
		<action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
	</intent-filter>
	<meta-data
		android:name="android.appwidget.provider"
		android:resource="@xml/widget_provider" />
</receiver>
<service android:name=".WidgetService" >
	<intent-filter>
		<action android:name="com.example.myproject.HOGEHOGE"/>
	</intent-filter>
</service>
```
サービスタグの は、ウィジェットに対してクリックイベントを取りたいときに必要。
  
作成中の自分のプロジェクト（myproject）の中で自分でHOGEHOGEを定義しておき、「HOGEHOGEになったら、ある処理をする」という具合に実装する。

## 画像を繰り返すには？

<pre class="go">android:tileMode="repeat"</pre>

## 最後に

上記をキーワードにググれば分かるはず。と自分に言い残してみる。
  
参考にしたURLをメモってれば良かったけど、まぁいっか。