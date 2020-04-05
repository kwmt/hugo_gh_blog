+++
title= "Support Design LibraryのBottomNavigationViewを読んだ"
date= 2020-03-26T02:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android"]
keywords = [""]
+++


# はじめに

[support design library](https://github.com/android/platform_frameworks_support/tree/master/design/src/android/support/design/widget)の特に[BottomNavigationView](https://github.com/android/platform_frameworks_support/blob/master/design/src/android/support/design/widget/BottomNavigationView.java)を読んでみました。

ガイドライン[原文](https://material.io/guidelines/components/bottom-navigation.html)・[日本語訳](https://www.gstatic.com/material/spec/jp/components.pdf)に書いてあるのが、どのようにコードで書かれているのかという視点で読みました。


ちなみに、BottomNavigationというのはこのようなものです。

<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/fa066bad-6be9-09be-bfa3-cceae7789771.gif" width="300">


# BottomNavigationViewを読む
## [ガイドライン]最上位の移動先を3～5個表示する



- 6個にした場合

IllegalArgumentExceptionが出ます。

<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/9411395c-ce05-9eef-2575-889a9a40ac24.png" width="300">



- 2個にした場合

Exceptionは出ずに、2個表示されます。

<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/60b1d188-c79f-07e1-e1d8-7e41c8aa5b57.png" width="300">







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



`colorPrimary`を次のように設定している場合、
```xml
<color name="colorPrimary">#3F51B5</color>
```
<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/f749b02d-f9d0-193a-8982-8bf9baf209df.png">


と青く表示されて、`colorPrimary`を変更するだけで下図のようにアイコンの色(とテキストの色)を変更できます。

<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/89b25b00-cdb1-d35e-372c-6aef54e65646.png">




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

<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/56511c3a-ee9d-ad90-6979-3c0cce1936c1.png" width="300">



高さは、[`design_bottom_navigation_height`が56dp](https://github.com/android/platform_frameworks_support/blob/7dbbadd44f1d8b14990cf413bace64e1535a2564/design/res/values/dimens.xml#L61)と定義されており、

```xml
<dimen name="design_bottom_navigation_height">56dp</dimen>
```

それを[こちらで取得して](https://github.com/android/platform_frameworks_support/blob/0b16435cc00a7e86687d53168b773105906ad1c0/design/src/android/support/design/internal/BottomNavigationMenuView.java#L77)、onMesureでセットしています。

## [ガイドライン]アイコン: 24 x 24 dp、テキストの下に 10 dp、テキストサイズRoboto Regular 14sp (active view)、Roboto Regular 12sp (inactive view)

### アイコン: 24 x 24 dp
<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/737f5870-2f08-8e40-c448-a9df566546bd.png" width="300">


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
<android.support.design.internal.BaselineLayout
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|center_horizontal"
    android:clipToPadding="false"
    android:paddingBottom="10dp"
    android:duplicateParentState="true">
    <TextView
        android:id="@+id/smallLabel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="@dimen/design_bottom_navigation_text_size"
        android:singleLine="true"
        android:duplicateParentState="true" />
    <TextView
        android:id="@+id/largeLabel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:visibility="invisible"
        android:textSize="@dimen/design_bottom_navigation_active_text_size"
        android:singleLine="true"
        android:duplicateParentState="true" />
</android.support.design.internal.BaselineLayout>
```
### テキストの下に 10 dp

こちらは上記XMLをみたらすぐわかります。

```xml
android:paddingBottom="10dp"
```

となっていました。

### テキストサイズRoboto Regular 14sp (active view)、Roboto Regular 12sp 

こちらもXMLを見たらすぐわかります。smallLabelに`design_bottom_navigation_text_size`(12sp)が設定され、largeLabelに`design_bottom_navigation_active_text_size`(14sp)が設定されています。



## [ガイドライン]アクティブ時アイコン上部のパディングは6dp, 非アクティブ時アイコン上部のパディングは8dp
### アイコンのアニメーションはどのように実装されているのか？

について見ていく中でガイドラインを確認して行きたいと思います。

この記事の最初と同じものですが、

<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/fa066bad-6be9-09be-bfa3-cceae7789771.gif" width="300">

アイコンの動きをよく見ると、選択時にアイコンが少し上にアニメーションして動き、非選択時に下にアニメーションしているのがわかるかと思います。これをどのように実装しているかを見ていきます。



`BottomNavigationMenuView`が`OnClickListener`でクリックを検知して`MenuPresenter`をimplementsした`BottomNavigationPresenter#updateMenuView`が呼ばれ、[`BottomNavigationMenuView#updateMenuView`](https://github.com/android/platform_frameworks_support/blob/7dbbadd44f1d8b14990cf413bace64e1535a2564/design/src/android/support/design/internal/BottomNavigationMenuView.java#L285)が呼ばれます。

`BottomNavigationMenuView#updateMenuView`を抜き出すと下記になります。

```java
public void updateMenuView() {
    final int menuSize = mMenu.size();
    if (menuSize != mButtons.length) {
        // The size has changed. Rebuild menu view from scratch.
        buildMenuView();
        return;
    }
    int previousSelectedId = mSelectedItemId;
    for (int i = 0; i < menuSize; i++) {
        mPresenter.setUpdateSuspended(true);
        MenuItem item = mMenu.getItem(i);
        if (item.isChecked()) {
            mSelectedItemId = item.getItemId();
            mSelectedItemPosition = i;
        }
        mButtons[i].initialize((MenuItemImpl) item, 0);
        mPresenter.setUpdateSuspended(false);
    }
    if (previousSelectedId != mSelectedItemId) {
        mAnimationHelper.beginDelayedTransition(this);
    }
}
```

まず、`mButtons`は
```
private BottomNavigationItemView[] mButtons;
```
このように定義されていますが、`BottomNavigationItemView#initialize`メソッドの中で、`setChecked`メソッドを呼んでその中でチェック状態によって、アイコンの`topmargin`を変更することによって移動させています。

```java
@Override
public void setChecked(boolean checked) {
    // 省略
    if (checked) {
        LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams();
        iconParams.gravity = Gravity.CENTER_HORIZONTAL | Gravity.TOP;
        iconParams.topMargin = mDefaultMargin + mShiftAmount;
        mIcon.setLayoutParams(iconParams);
        //　省略
    } else {
        LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams();
        iconParams.gravity = Gravity.CENTER_HORIZONTAL | Gravity.TOP;
        iconParams.topMargin = mDefaultMargin;
        mIcon.setLayoutParams(iconParams);
        // 省略
    }
}
```

`mDefaultMargin`が8dpで`mShiftAmount`が-2dpになるので、アクティブ時（選択時）は6dpになります。

これだけだったら切り替わり時にパッと切り替わるだけなので、なんでアニメーションしているのかと思ったら、ポイントは`updateMenuView`メソッドの最後の下記の部分でアニメーションを実行させていました。

```java
mAnimationHelper.beginDelayedTransition(this);
```

こちらの実態はICS以降の場合は下記を呼びます。ICSより以前はサポートされてませんので、メソッドの中身は空の状態です。
```java
TransitionManager.beginDelayedTransition(view, mSet);
```

こちらのTransitionの詳細は[荒木さんのyoutube](https://www.youtube.com/watch?v=YMmuuuB-uxM)を見るのが分かりやすいかと思います。Transition Support Libraryについては、7:23あたりから聞くことが出来ます。


## [ガイドライン]アクションが4つまたは5つの場合は、アクティブではないビューにはアイコンしか表示しません。

<img src="https://qiita-image-store.s3.amazonaws.com/0/22161/99de8cac-8ab0-404e-4664-f003d8366c64.png" width="300">


`BottomNavigationMenuView`がメニューを作成する際に`buildMenuView`メソッドが呼ばれますが、そこで`mShiftingMode = mMenu.size() > 3;`
とメニュー数が4か5個だったら`mShiftingMode`をtrueにセットしています。この`mShiftingMode`を使っていろいろ判断しているようです。

先程の`BottomNavigationItemView#setChecked`メソッドで、`mLargeLabel`のみ使って、`mSmallLabel`をINVISIBLEにすることで実現していました。

```java
@Override
public void setChecked(boolean checked) {
    if (mShiftingMode) {
        if (checked) {
            LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams();
            iconParams.gravity = Gravity.CENTER_HORIZONTAL | Gravity.TOP;
            iconParams.topMargin = mDefaultMargin;
            mIcon.setLayoutParams(iconParams);
            mLargeLabel.setVisibility(VISIBLE);
            mLargeLabel.setScaleX(1f);
            mLargeLabel.setScaleY(1f);
        } else {
            LayoutParams iconParams = (LayoutParams) mIcon.getLayoutParams();
            iconParams.gravity = Gravity.CENTER;
            iconParams.topMargin = mDefaultMargin;
            mIcon.setLayoutParams(iconParams);
            mLargeLabel.setVisibility(INVISIBLE);
            mLargeLabel.setScaleX(0.5f);
            mLargeLabel.setScaleY(0.5f);
        }
        mSmallLabel.setVisibility(INVISIBLE);
    } else {
        //省略
    }
}
```

## [ガイドライン]Shifting bottom navigation barのWidthのサイズ

```
Active view
    Maximum: 168dp
    Minimum: 96dp
Inactive view
    Maximum: 96dp
    Minimum: 56dp
```

[`BotomNavigationMenuView#onMeasure`](https://github.com/android/platform_frameworks_support/blob/master/design/src/android/support/design/internal/BottomNavigationMenuView.java#L110-L116) で計算してました。

```java
final int inactiveCount = count - 1;
final int activeMaxAvailable = width - inactiveCount * mInactiveItemMinWidth;
final int activeWidth = Math.min(activeMaxAvailable, mActiveItemMaxWidth);
final int inactiveMaxAvailable = (width - activeWidth) / inactiveCount;
final int inactiveWidth = Math.min(inactiveMaxAvailable, mInactiveItemMaxWidth);
```

```
mInactiveItemMinWidth = 56dp
mInactiveItemMaxWidth = 96dp
mActiveItemMaxWidth = 168dp
```
なので、`activeWidth`は最大168dpになり、`inactiveWidth`は最大96dpになります。

## [ガイドライン] スナックバーの高度（6dp）は低いため、BottomNavigation（8dpの高度）の背面にスナックバーが表示されます。

関係するのは`BottomNavigationView`のコンストラクタで下記の処理があったぐらいでした。
BottomNavationの上側からSnackbarが表示されるように実装する必要があります。

```java
if (a.hasValue(R.styleable.BottomNavigationView_elevation)) {
    ViewCompat.setElevation(this, a.getDimensionPixelSize(
            R.styleable.BottomNavigationView_elevation, 0));
}
```

## その他わかったこと

### ?でのリソース参照について

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

### Tintについて

> API level 21 から、Drawable リソースをアルファマスクとして定義し、その Drawable に色を付けて表示する、ということができるようになりました。 同じ形で異なる色のアイコンを複数個用意したいというような場面で便利ですね。

http://vividcode.hatenablog.com/entry/android-app/drawable-tinting

こちら知らなかった。。。

### PointerIconについて

[`BottomNavigationItemView#setEnabled`](https://github.com/android/platform_frameworks_support/blob/master/design/src/android/support/design/internal/BottomNavigationItemView.java#L190)メソッドを眺めていたら、

```java
ViewCompat.setPointerIcon(this,
        PointerIconCompat.getSystemIcon(getContext(), PointerIconCompat.TYPE_HAND));
```

という処理があって気になったので、[PointerIconCompat#setPointerIcon](https://developer.android.com/reference/android/support/v4/view/ViewCompat.html#setPointerIcon)を調べてみたら、マウスオーバーした時にマウスポインタを変更するような処理なんですね。スマホでは使わないと思うんですが、TVとかで使うことあるんですかね。

# おわりに

- BottomNavigationViewのコードは、そんなに複雑なコードじゃなかったので、読みやすいかなと思いました。


