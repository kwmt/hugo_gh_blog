+++
date = "2017-08-13T20:00:00+09:00"
title = "モバもく会 #3 を開催して、Support Design LibraryのBottomNavigationViewを読んだ"
draft = false
categories = ["Android"]

+++


# はじめに

<a  target="_blank" href="https://connpass.com/event/63575/" >モバもく会 #3</a>という、モバイルに関連したもくもく会を開催しました。

今回は2人でした。

* 一人はVR（前回の続き）

これを書いてる19:40過ぎてもまだやってますｗ

僕は、[support design library](https://github.com/android/platform_frameworks_support/tree/master/design/src/android/support/design/widget)の特にBottomNavigationViewを読んでみました。

ガイドライン[原文](https://material.io/guidelines/components/bottom-navigation.html)[日本語訳](https://www.gstatic.com/material/spec/jp/components.pdf)
に書いてあるのがどのようにコードで書かれているのかという視点で見てみました。


ちなみに、BottomNavigationというのはこのようなものです。

<img src="/images/2017/08/mobamock3/bottom_navigation.gif" width="300">


## [ガイドライン]最上位の移動先を 3～5 個表示する

### 使ってみる

- 6個にした場合

IllegalArgumentExceptionが出ます。

<img src="/images/2017/08/mobamock3/bottom_over6.png" width="300">


- 2個にした場合

Exceptionは出ずに、2個表示されます。

<img src="/images/2017/08/mobamock3/bottom_only2.png" width="300">



### コード読む


`BottomNavigationView` が `BottomNavivgationMenu` (MenuBuilder) に対して、`app:menu`で指定した
`app:menu="@menu/navigation"` navigation.xmlをinflateする際にBottomNavivgationMenuにaddします。


`BottomNavigationView`のコンストラクタで[`app:menu="@menu/navigation"`をinflate](https://github.com/android/platform_frameworks_support/blob/master/design/src/android/support/design/widget/BottomNavigationView.java#L167)します。

```java
if (a.hasValue(R.styleable.BottomNavigationView_menu)) {
    inflateMenu(a.getResourceId(R.styleable.BottomNavigationView_menu, 0));
}
```

XMLをパースしながら[`BottomNavivgationMenu`インスタンスに対してadd](https://github.com/android/platform_frameworks_support/blob/master/v7/appcompat/src/android/support/v7/view/SupportMenuInflater.java#L483)しているところがあり、

```java
public void addItem() {
    itemAdded = true;
    setItem(menu.add(groupId, itemId, itemCategoryOrder, itemTitle));
}
※`menu`が`BottomNavivgationMenu`インスタンスとなる。
```

この時、実際にaddする前に[MenuのサイズがMAX_ITEM_COUNT(5)個より多かったら](https://github.com/android/platform_frameworks_support/blob/master/design/src/android/support/design/internal/BottomNavigationMenu.java#L46)、IllegalArgumentExceptionを出しているようです。

```java
if (size() + 1 > MAX_ITEM_COUNT) {
    throw new IllegalArgumentException(
            "Maximum number of items supported by BottomNavigationView is " + MAX_ITEM_COUNT
                    + ". Limit can be checked with BottomNavigationView#getMaxItemCount()");
}
stopDispatchingItemsChanged();
final MenuItem item = super.addInternal(group, id, categoryOrder, title);
```

3個未満については、特にチェックしていないので、表示されてしまいます。

## [ガイドライン]アプリのメインの色でアクティブなアイコンを色付けします。

### 使ってみる

`colorPrimary`を次のように設定している場合、
```xml
<color name="colorPrimary">#3F51B5</color>
```
<img src="/images/2017/08/mobamock3/itemview_blue.png">

と青く表示されて、`colorPrimary`を変更するだけで下図のようにアイコンの色(とテキストの色)を変更できます。

<img src="/images/2017/08/mobamock3/itemview_red.png">


### コード読む

BottomNavigationViewのコンストラクタで、[xmlで`itemIconTint`や`itemTextColor`をセットしてないければ、デフォルトカラーをセットする](https://github.com/android/platform_frameworks_support/blob/7dbbadd44f1d8b14990cf413bace64e1535a2564/design/src/android/support/design/widget/BottomNavigationView.java#L144-L157)ようになっていました。

```java
if (a.hasValue(R.styleable.BottomNavigationView_itemIconTint)) {
    mMenuView.setIconTintList(
            a.getColorStateList(R.styleable.BottomNavigationView_itemIconTint));
} else {
    mMenuView.setIconTintList(
            createDefaultColorStateList(android.R.attr.textColorSecondary));
}
if (a.hasValue(R.styleable.BottomNavigationView_itemTextColor)) {
    mMenuView.setItemTextColor(
            a.getColorStateList(R.styleable.BottomNavigationView_itemTextColor));
} else {
    mMenuView.setItemTextColor(
            createDefaultColorStateList(android.R.attr.textColorSecondary));
}
```

`createDefaultColorStateList`メソッドは次のようになっています。

```java
private ColorStateList createDefaultColorStateList(int baseColorThemeAttr) {
    final TypedValue value = new TypedValue();
    if (!getContext().getTheme().resolveAttribute(baseColorThemeAttr, value, true)) {
        return null;
    }
    ColorStateList baseColor = AppCompatResources.getColorStateList(
            getContext(), value.resourceId);
    if (!getContext().getTheme().resolveAttribute(
            android.support.v7.appcompat.R.attr.colorPrimary, value, true)) {
        return null;
    }
    int colorPrimary = value.data;
    int defaultColor = baseColor.getDefaultColor();
    return new ColorStateList(new int[][]{
            DISABLED_STATE_SET,
            CHECKED_STATE_SET,
            EMPTY_STATE_SET
    }, new int[]{
            baseColor.getColorForState(DISABLED_STATE_SET, defaultColor),
            colorPrimary,
            defaultColor
    });
}
``` 


下記の部分で、動的にテーマに設定されているcolorPrimaryを取得しています。

```java
    final TypedValue value = new TypedValue();
    if (!getContext().getTheme().resolveAttribute(
            android.support.v7.appcompat.R.attr.colorPrimary, value, true)) {
        return null;
    }
    int colorPrimary = value.data;
```


参考: [Android　コードからテーマの属性値を取得する](http://y-anz-m.blogspot.jp/2012/10/android.html)

あと、`ColorStateList`は、状態に対応したカラーを設定するリストになりますので、

```java
return new ColorStateList(new int[][]{
        DISABLED_STATE_SET,
        CHECKED_STATE_SET,
        EMPTY_STATE_SET
}, new int[]{
        baseColor.getColorForState(DISABLED_STATE_SET, defaultColor),
        colorPrimary,
        defaultColor
});
```

この場合だと、選択状態(`CHECKED_STATE_SET`)のとき、`colorPrimary`カラーを設定しています。


## [ガイドライン]高さ: 56 dp

<img src="/images/2017/08/mobamock3/BottomNavigationMenuView.png" width="300">

高さは、[`design_bottom_navigation_height`が56dp](https://github.com/android/platform_frameworks_support/blob/7dbbadd44f1d8b14990cf413bace64e1535a2564/design/res/values/dimens.xml#L61)と定義されており、

```xml
<dimen name="design_bottom_navigation_height">56dp</dimen>
```

それを[こちらで取得して](https://github.com/android/platform_frameworks_support/blob/0b16435cc00a7e86687d53168b773105906ad1c0/design/src/android/support/design/internal/BottomNavigationMenuView.java#L77)、onMesureでセットしています。

## [ガイドライン]アイコン: 24 x 24 dp

<img src="/images/2017/08/mobamock3/bottomNavigationItemView.png" width="300">

メニュー1個を表すClassは`BottomNavigationItemView` になりますが、レイアウトは[design_bottom_navigation_item.xml](https://github.com/android/platform_frameworks_support/blob/master/design/res/layout/design_bottom_navigation_item.xml)で決めれています。

xmlの中身を見るとImageViewがアイコンになる箇所で、`layout_width`,`layout_height`に24dpが直に記入されていました。

```xml
<ImageView
    android:id="@+id/icon"
    android:layout_width="24dp"
    android:layout_height="24dp"
    android:layout_gravity="center_horizontal"
    android:layout_marginTop="@dimen/design_bottom_navigation_margin"
    android:layout_marginBottom="@dimen/design_bottom_navigation_margin"
    android:duplicateParentState="true" />

```

# その他わかったこと

## ?でのリソース参照について

```xml
android:background="?android:attr/windowBackground"
```

テーマに設定されている値を参照する場合に `?` を使うことができます。

上記の場合はAndroid SDKが定義しているAtrributeを使う場合の例です。


テーマに設定されているということなので、`styles.xml` の `<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">` のparentをどんどんたどっていくと `<style name="Theme.Light">` まで行き、そこに <a href="https://github.com/android/platform_frameworks_base/blob/master/core/res/res/values/themes.xml#L474" target="_blank">`windowBackground`</a> が

```xml
<item name="windowBackground">@drawable/screen_background_selector_light</item>
```

と定義されていました。`<style name="Theme">`の方には、

```xml
<item name="windowBackground">@drawable/screen_background_selector_dark</item>
```
と定義されてますね。

自分で定義したAttributeを使う場合には、`?attr/<自分で定義したAttribute>` や `?/<自分で定義したAttribute>` のようにする

テーマと連動させたい場合に `?` を使って参照すると良いようです。

## Tintについて

> API level 21 から、Drawable リソースをアルファマスクとして定義し、その Drawable に色を付けて表示する、ということができるようになりました。 同じ形で異なる色のアイコンを複数個用意したいというような場面で便利ですね。

http://vividcode.hatenablog.com/entry/android-app/drawable-tinting

こちら知らなかった。。。

## まとめ

- いままであんまり深く知らなかったことを見ることが出来たので、とても勉強になりました。
- Support Design Librayのコードは、そんなに複雑なコードじゃなかったので、読みやすいかなと思いました。

