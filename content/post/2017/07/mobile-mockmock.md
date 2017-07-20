+++
date = "2017-07-20T22:00:00+09:00"
title = "モバもく会を開催して、Github Trendingを軽く読んでみた"
draft = false
categories = ["Android"]

+++


## はじめに

<a  target="_blank" href="https://connpass.com/event/62168/" >モバもく会</a>という、モバイルに関連したもくもく会を開催しました。
一人はOpenSourceにプルリクを投げたり、一人は読書をしていました。

僕は、2017/07/17の時点のTrending in open sourceのAndroidに関係ある上位３つのサンプルをうごかしてみたのですが、そのときのまとめです。(※android-interview-questionsは除外しています)

<a target="_blank" href="https://github.com/trending/java?since=weekly">https://github.com/trending/java?since=weekly</a>


<img src="/images/2017/07/mobamock/trending.png">



## SmartRefreshLayout

<a target="_blank" href="https://github.com/scwang90/SmartRefreshLayout">https://github.com/scwang90/SmartRefreshLayout</a>

Pull to refreshのいい感じのライブラリです。
動きはReadmeに書かれてるので見てみてください。


基本的には`RecyclerView`を`SmartRefreshLayout`で包めばよいだけのようです。

```
<android.support.v7.widget.Toolbar 
style="@style/AppTheme.Toolbar"
        android:id="@+id/toolbar"
        app:title="@string/fragment_refresh_styles"/>

    <com.scwang.smartrefresh.layout.SmartRefreshLayout
        android:id="@+id/refreshLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:srlEnableLoadmore="false"
        app:srlEnableHeaderTranslationContent="true">
        <android.support.v7.widget.RecyclerView
            android:id="@+id/recyclerView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="@android:color/white"
            tools:listitem="@android:layout/simple_list_item_2"/>
    </com.scwang.smartrefresh.layout.SmartRefreshLayout>

```

サンプル動かしてみました。

<img src="/images/2017/07/mobamock/before.png" width="300">

「下拉可以刷新」などの中国語で書かれてるところを変更したいなぁと思ってソースを見てみました。

<a target="_blank" href="https://github.com/scwang90/SmartRefreshLayout/blob/343981410b8dd00d805859220e6e88c4be3178fe/refresh-layout/src/main/java/com/scwang/smartrefresh/layout/header/ClassicsHeader.java#L87"></a>


こちらを見る限り、おそらく`mHeaderText`にセットすればいいのかなぁと思ってみてたら、`mHeaderText`に外から自由にセットできるメソッドがないっぽい。。

```
public static String REFRESH_HEADER_PULLDOWN = "下拉可以刷新";
```

とpublic static Stringでfinalではなかったので、直接書き換えてみたところ、


```
index 2b056cd..fbdf4a7 100644
--- a/app/src/main/java/com/scwang/refreshlayout/fragment/RefreshStylesFragment.java
+++ b/app/src/main/java/com/scwang/refreshlayout/fragment/RefreshStylesFragment.java
@@ -85,6 +85,7 @@ public class RefreshStylesFragment extends Fragment implements AdapterView.OnIte
     @Override
     public void onViewCreated(View root, @Nullable Bundle savedInstanceState) {
         super.onViewCreated(root, savedInstanceState);
+        ClassicsHeader.REFRESH_HEADER_PULLDOWN = "Pull down to reresh";
 
         View view = root.findViewById(recyclerView);
         if (view instanceof RecyclerView) {
```

下記のように変更出来ました。

<img src="/images/2017/07/mobamock/after.png" width="300">


Pull to refreshとは直接関係ありません、サンプルソースを見ていてこれは使っていきたいなぁと思ったのが、

<a target="_blank" href="https://github.com/scwang90/SmartRefreshLayout/blob/343981410b8dd00d805859220e6e88c4be3178fe/app/src/main/java/com/scwang/refreshlayout/activity/IndexMainActivity.java#L21-L24">enum TabFragment</a>として列挙型としている点です。

たまたま「現場で役立つシステム設計の原則」という本の中で　~区分オブジェクト~ として紹介されてることでしたので、Androidなどではこのように使うといいのかと一つ勉強になりました。
タブで使われるフラグメントが一覧でわかりやすく、タブを追加したいときも容易に追加できそうなことがわかります。

あと、Android Studioの使い方ですが、下図にあるように、
`//<editor-fold desc="background">`や`//<editor-fold desc="API">`とコメントがあるのが気になりました。

<img src="/images/2017/07/mobamock/editor-fold.png" >


`editor-fold` とあるので、使い方はなんとなく想像つくと思うのですが、

<img src="/images/2017/07/mobamock/folded.png" >

折りたたむとこうなりました。
メソッド単位で折りたためるのは知っていたのですが、

```
//<editor-fold desc="background">
~~~
//</editor-fold>
```

と`<editor-fold></editor-fold>`で挟んだところが一気に折りたためるのは知らなかったです。しかもそれが何か説明(desc)を付けることができます。

## RichPath

<a target="_blank" href="https://github.com/tarek360/RichPath">https://github.com/tarek360/RichPath</a>

SVGをAndroidでまともに使ったことなかったのですが、Android 5.0からSVGが使えるんですね。
受託開発だとまだ4.4が切れないところも多いのですが、5.0以降で良い場合は、サイズごとの余計な画像が要らなくなるので積極的につかっていきたいと思いました。

こちらのツールでAnimatedVectorDrawableを作れるのですが、RichPathはprogramaticallyにアニメーションできるのが良いのでしょうか。

<a target="_blank" href="https://shapeshifter.design/">https://shapeshifter.design/></a>

SVG力がなくて、こちらに関してはもう少しSVG力を付けてからみてみようと思います。

## Spotlight

<a target="_blank" href="https://github.com/TakuSemba/Spotlight">https://github.com/TakuSemba/Spotlight</a>

サンプルを動かしてみました。

<img src="/images/2017/07/mobamock/spotlight.gif" width="300" >

かっこいいですね！


スポットライトしたいTargetを作って、

```
SimpleTarget firstTarget = new SimpleTarget.Builder(MainActivity.this).setPoint(findViewById(R.id.one))
                        .setRadius(100f)
                        .setTitle("first title")
                        .setDescription("first description")
                        .build();
                        
```

作ったTargetをsetTargetsでセットするだけ！

```
Spotlight.with(MainActivity.this)
        .setDuration(1000L)
        .setAnimation(new DecelerateInterpolator(2f))
        .setTargets(firstTarget, secondTarget, thirdTarget)
        .setOnSpotlightStartedListener(new OnSpotlightStartedListener() {
            @Override
            public void onStarted() {
                Toast.makeText(MainActivity.this, "spotlight is started", Toast.LENGTH_SHORT)
                        .show();
            }
        })
        .setOnSpotlightEndedListener(new OnSpotlightEndedListener() {
            @Override
            public void onEnded() {
                Toast.makeText(MainActivity.this, "spotlight is ended", Toast.LENGTH_SHORT).show();
            }
        })
        .start();
```

`SimpleTarget.Builder(MainActivity.this).setPoint`の引数は座標も指定できるので、WebViewでも使えそうかなと思いました。

## まとめ

ライブラリの中のソースコードまでガッツリ読まなくて、サンプルコード読むだけでも知らないことが多いことがわかりました。

サンプル動かしてみるだけならそんなに時間もかからないので、ちょっと空き時間に動かしてみると発見があるかもしれません。
