---
title: Android Studio(0.5.7)のNew ProjectでTabbed Activityを選んだらエラーになる
author: kwmt
layout: post
date: 2014-05-03
url: /index.php/2014/05/03/android-studio-tabbed-activity-error/
pdrp_attributionLocation:
  - end
categories:
  - Android
tags:
  - Android Studio
  - Android

---
<img src="http://kwmt27.net/images/2014/05/tabbed-activity.png" alt="tabbed-activity" width="387" height="239" class="aligncenter size-full wp-image-1419" />
  
Android StudioのNew ProjectでTabbed Activityを選んだらいきなり下のエラーが出て困った。 

<pre class="go">Error:Execution failed for task ':app:processDebugResources'.
> com.android.ide.common.internal.LoggedErrorException: Failed to run command:
&lt;android SDK&gt;/build-tools/19.0.0/aapt package -f --no-crunch -I &lt;android SDK&gt;/platforms/android-19/android.jar -M &lt;my project&gt;/app/build/manifests/debug/AndroidManifest.xml -S &lt;my project&gt;/app/build/res/all/debug -A &lt;my project&gt;/app/build/assets/debug -m -J &lt;my project&gt;/app/build/source/r/debug -F &lt;my project&gt;/app/build/libs/app-debug.ap_ --debug-mode --custom-package &lt;my package>
  Error Code:
    138
</pre>

このエラーから検索していったらハマった。stack overflowとかではaaptのバグだよとか書かれてるし、しょうがないのかなぁとか思ってて、よくよく考えると、`MainActivity`は`ActionBarActivity`を継承しているのですが、ただ単に`ActionBarActivity`がみつからないって言われてただけでした。

つまり、`import android.support.v7.app.ActionBarActivity;`を追加する。ただし、最初のプロジェクトの自動生成では、support.v7ライブラリは追加されていないので、FileメニューからProject Structure(⌘;)から、Dependenciesタブを選択し、下部の+から`com.android.support:appcompat-v7`を追加する

<img src="http://kwmt27.net/images/2014/05/project-structure-600x164.png" alt="project-structure" width="600" height="164" class="aligncenter size-thumbnail wp-image-1418" />

これで`ActionBarActivity`のエラーは消えるのですが、Runすると、

<pre class="go">You need to use a Theme.AppCompat theme (or descendant) with this activity.
at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2295)
</pre>

のようなエラーが出るので、`AndroidManifest.xml`のthemeの部分を

<pre class="go">android:theme="@style/Theme.AppCompat"
</pre>

に変更する。これで無事起動した。