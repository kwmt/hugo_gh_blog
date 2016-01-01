---
title: xml＋javaでUIを表示
author: kwmt
layout: post
date: 2010-08-29
url: /index.php/2010/08/29/xml＋javaでuiを表示/
categories:
  - Android
tags:
  - Android

---
setting.xml

<pre class="brush: xml; title: ; notranslate" title="">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent"
&gt;
&lt;CheckBox
android:id="@+id/checkbox_maponoff_id"
android:text="@string/checkbox_maponoff"
android:layout_width="wrap_content"
android:layout_height="wrap_content" /&gt;
&lt;/LinearLayout&gt;
</pre>

SettingActivity.java

<pre class="brush: java; title: ; notranslate" title="">public class SettingActivity extends Activity implements View.OnClickListener {
private  CheckBox chkbox = null; //チェックボックス
//初期化
@Override
public void onCreate(Bundle icicle) {
super.onCreate(icicle);
//setting.xmlで記述したレイアウトを表示
setContentView(R.layout.setting);
//setting.xmlで記述した部品を使用したい場合
chkbox = (CheckBox)findViewById( R.id.checkbox_id); //setting.xmlで指定したidを取得
chkbox.setChecked(true);　//チェックボックスをチェックした状態で初期化する
}
}
</pre>

<pre class="brush: java; title: ; notranslate" title="">setContentView(R.layout.setting);
</pre>

の&#8221;setting&#8221;はxmlファイルのファイル名

結果はこれ。
  
<img
src="http://androg.up.seesaa.net/image/xml-java-checkbox-thumbnail2.png" width="320" height="112" border="0" align="" alt="xml-java-checkbox.png"
pbsrc="http://androg.up.seesaa.net/image/xml-java-checkbox.png"
class="PopBoxImageSmall"
onclick="Pop(this,100,'PopBoxImageLarge');" />

背景が白いのは、
  
AndroidManifest.xml
  

  
で、

<pre class="brush: xml; title: ; notranslate" title="">&lt;!-- アプリケーションの定義 --&gt;
&lt;application  android:theme="@android:style/Theme.Light" &gt;
</pre>

というように、android:themeにTheme.Lightを指定しているため。
  

  
ちなみに、Theme.Light以外にもたくさんある。
  
<http://developer.android.com/intl/ja/reference/android/R.style.html>