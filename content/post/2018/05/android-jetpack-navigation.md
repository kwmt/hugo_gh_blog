+++
date = "2018-06-02T18:00:00+09:00"
title = "Navigation Architecture Component"
draft = false

+++


# はじめに
これは、[Navigation Codelab](https://codelabs.developers.google.com/codelabs/android-navigation/index.html?index=..%2F..%2Findex#2)をやりながら、[Implement navigation with the Navigation Architecture Component](https://developer.android.com/topic/libraries/architecture/navigation/navigation-implementing)も読みつつ、わからないことだったり、気づいたことをメモしていってる内容です。


## Navigationとは？

Navigation Architecture Component はアプリのナビゲーションの実装をシンプルにします。
行き先はアプリ内の特定の画面です。デフォルトでは、Navigation Architecture Componentは遷移先としてSupport FragmentとActivityを含みますが、

## setup

```groovy
// Navigation
implementation 'android.arch.navigation:navigation-fragment:' + rootProject.navigationVersion
implementation 'android.arch.navigation:navigation-ui:' + rootProject.navigationVersion
```

## Destinations
Destinationは、ユーザーが遷移可能な場所です。
プログラム的に、そして、オススメの方法に従えば、Destinationは通常はfragmentです。Activityもdestinationにできます。Navigationはfragmentとactivityの両方をサポートしていますが、必要ならカスタムdestination typeを作ることもできます。

## Navigation graph
Navigation Graphは、Destinationから別のDestinationに遷移するのを視覚的に表現するものです。
Navigation graphは新しいリソースの種類で、ユーザーが取りうる全てのパスを定義します。
Android StudioはNavigation Editorを使ってビジュアル的に表示してくれます。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation_graph.png" />
</div>

Navigation Editorを使うには、`res`ディレクトリに`navigation`ディレクトリを作成し、その中に、適当なファイル名で、リソースタイプをNavigationにしたリソースファイルを作成します。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-design-tab.png" />
</div>

上図のようにDesignタブがあるので、Designタブに切り替えるとNavigation Editorが使えます。

ちなみに、GradleのSyncに失敗すると、表示されませんので、ご注意ください。

## Navigation Editorを詳しく見てみる

[Navigation Codelab](https://github.com/googlecodelabs/android-navigation)のコードだと、下図のようになっています。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-editor-sample.png" />
</div>
このNavigation Graphは利用可能なDestinationを表示しています。
destination間のラインは`action`と呼ばれます。

Destinationをクリックすると、そのプロパティを確認することができます。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigaion-editor-destination-property.png" />
</div>

action(矢印部分)をクリックすると、actionのプロパティを確認することができます。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-editor-action-property.png" />
</div>

## NavigationのXMLを詳しく見てみる

Navigation Editorで行った全ての変更は、もとになるXMLを変更します。
それは、レイアウトエディタを使ってレイアウトXMLを変更するのと同じです。


<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-editor-design-xml.gif" />
</div>

ちなみに、Destinationを移動すると、`.idea/navEditor.xmlが変更されます。

```
iff --git a/.idea/navEditor.xml b/.idea/navEditor.xml
index a96811f..e2dfca8 100644
--- a/.idea/navEditor.xml
+++ b/.idea/navEditor.xml
@@ -46,8 +46,8 @@
                       <LayoutPositions>
                         <option name="myPosition">
                           <Point>
-                            <option name="x" value="610" />
-                            <option name="y" value="-47" />
+                            <option name="x" value="640" />
+                            <option name="y" value="-55" />
                           </Point>
                         </option>
                         <option name="myPositions">
```

Navigation GraphのXMLは次のようになっています。

```xml
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            xmlns:tools="http://schemas.android.com/tools"
            app:startDestination="@id/launcher_home">

    <fragment
            android:id="@+id/launcher_home"
            android:name="com.example.android.codelabs.navigation.MainFragment"
            android:label="@string/home"
            tools:layout="@layout/main_fragment">

    <!-- ...tags for fragments and activities here -->

</navigation>
```

- `<navigation>`は各Navigation Graphのルートノードです。
- `<navigation>`は1つ以上の`<activity>` または `<fragment>`の要素で表されるDestinationが入ります。
- `app:startDestination`は、ユーザーがまずアプリを開いたときにデフォルトで起動されるDestinationが何なのかを制御するアトリビュートです。
    - 上記の例だと、`<navigation>`にidが`launcher_home`というFragmentがあって、そのFragmentをFragmentを起動時のDestinationとしています。つまり今回の場合はMainFragmentが起動時のFragmentです。

Fragment DestinationのXMLを見てみます。

```xml
    <fragment
        android:id="@+id/flow_step_one"
        android:name="com.example.android.codelabs.navigation.FlowStepOneFragment"
        tools:layout="@layout/flow_step_one_fragment">

        <argument
            .../>

        <action
            android:id="@+id/next_action"
            app:destination="@+id/flow_step_second"/>
    </fragment>
```

- `android:id`はIDをFragmentに割り当て、このXMLからやプログラムから参照できます。
- `android:name`は、遷移したときにインスタンス化するFragmentのパッケージも含めた名前を指定します。
- `tools:layout`はNavigation Editorで表示するレイアウトを指定します。

## Destinationを追加する

Navigation Editorから追加できます。
<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-editor-add-destination.gif" />
</div>

すでにFragmentやActivityクラスがあれば、選択候補に出てきますのでそれらを選択でき、
なければ、`Create blank destination`をクリックすると、Fragmentを作成するダイアログが表示され、Fragmentをその場で作成することができます。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-create-blank-destination.png" />
</div>


## Navigation Graphを使ってNavigateする

Navigation Componentは[Navigationの原則](http://d.android.com/topic/libraries/architecture/navigation/navigation-principles)に従っています。
この原則は、Bottom Navigationのようなグローバルナビゲーションがあるアプリに対してのエントリポイントとしてActivityを使うことを勧めていて、Fragmentは実際の遷移先になります。

このように動かすためには、Activityのレイアウトを、`NavHostFragment`と呼ばれる特別なウィジェットに変更する必要があります。
`NavHostFragment`は、Navigation Graphで遷移するときに、異なるFragmentの遷移先を入れ替えるためのウィジェットです。コンテナの役割をします。

activity layout
```xml 
<LinearLayout
    ...
    />
    <android.support.v7.widget.Toolbar
        ...
    />
    <fragment
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:id="@+id/my_nav_host_fragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        app:navGraph="@navigation/mobile_navigation"
        app:defaultNavHost="true"
        />
    <android.support.design.widget.BottomNavigationView
    ...
    />
</LinearLayout>
```

- これはBottom NavigationとToolbarのグローバルナビゲーションを持つActivityのレイアウトです。
- `android:name="androidx.navigation.fragment.NavHostFragment"`と`app:defaultNavHost="true"`はシステムのバックボタンと`NavHostFragment`を連携させます。
- `app:navGraph="@navigation/mobile_navigation"`はNavigation Graphと`NavHostFragment`を連携させます。

Activityは、アクティビティレイアウトに追加されているNavHostインターフェイスの実装を通して、アプリのナビゲーションをホストします。
NavHostは空のViewですが、ユーザーがアプリを遷移するときにDestinationが入れ替えられます。

Navigation Architecture ComponentのデフォルトのNavHostの実装は`NavHostFragment`です。

NavHostを含めた後、`navGraph`アトリビュートを使って`NavHostFragment`とnavigation graphを関連付けなければなりません。

`app:defaultNavHost="true"`アトリビュートは、NavHostFragmentがシステムのバックボタンをフックすること保証します。または、
`AppCompatActivity.onSupportNavigateUp()`をoverrideし、`NavController.navigateUp`を呼ぶことも出来ます。

```kotlin
override fun onSupportNavigateUp()
        = findNavController(R.id.nav_host_fragment).navigateUp()
```

## DestinationをUIと結びつける


ボタンをクリックしたとき、navigateコマンドを呼ぶ必要があります。`NavController`と呼ばれる特別なクラスが、`NavHostFragment`で入れ替えるすべてのFragmentを実際のトリガーとなります。

```kotlin
val navController = v.findNavController()
navController.navigate(R.id.settings)
```
上記は、navigateメソッドにIDを指定してますが、これはNavigation GraphのXMLで定義されているものです。


destinationに遷移するのは、`NavController`クラスを使って実現します。`NavController`は、次のstaticメソッドのどれかを使って取得することができます。

- `NavHostFragment.findNavController(Fragment)`
- `Navigation.findNavController(Activity, @IdRes int viewId)`
- `Navigation.findNavController(View)`

ちなみにどうやって探しているかは、
まず、
NavHostFragmentのonViewCreatedでViewGroup(FrameLayout)にkeyが nav_controller_view_tagのNavControllerのタグをセットしていて、

```java
view.setTag(R.id.nav_controller_view_tag, navController);
```

findNavControllerでnav_controller_view_tagのタグを取得しNavControllerでなければ親Viewに対して同じことをして探しています。見つからなければ、nullになります。

```java
Object tag = view.getTag(R.id.nav_controller_view_tag);
```


そうやって探した`NavController`を取得後、destinationに遷移するために、`navigate()`メソッドを使います。`navigate()`メソッドはリソースIDを取ります。そのIDは、navigation graphの特定のDestinationのIDか、actionのIDになります。
特定のDestinationのIDの代わりにactionのIDを使うと、ナビゲーションにトランジションを関連付けることができるような利点があります。

下記は`viewTransactionsAction`への遷移方法の例です。action IDを指定しています。

```kotlin
viewTransactionsButton.setOnClickListener { view ->
   view.findNavController().navigate(R.id.viewTransactionsAction)
}
```

Androidシステムは、最後に訪れたDestinationを含むバックスタックを維持しています。
ユーザーがアプリを開いたとき、アプリの最初のDestinationがスタックに積まれます。
`navigate()`メソッドを呼び出す度に、他のDestinationがスタックの一番上に積まれていきます。
逆に、 `NavController.navigateUp()`や`NavController.popBackStack()` メソッド呼んでUpやBackボタンを押すと、スタックの上のDestinationがポップされます。

Destinationに遷移するために、Navigationクラスの`createNavigateOnClickListener()`という便利なメソッドを使う事もできます。

OnClickListenerのonClickでNavControllerを探して、navigateするだけで、

```kotlin
viewTransactionsButton.setOnClickListener { view ->
   view.findNavController().navigate(R.id.viewTransactionsAction)
}
```
これとやってることは同じです。

## メニューとDestinationを紐づける

Navigation Drawerやメニューから遷移するには、Navigation GraphのXMLのDestinationのidとメニューのidを同じにすることで実現できます。
Navigation GraphとMenuの紐づけはこのようにします。

```kotlin
val navigationView = findViewById(R.id.nav_view)
NavigationUI.setupWithNavController(navigationView, navController)
```

navigation-ui-ktxを使っている場合は、次のようになります。

```kotlin
val navigationView = findViewById(R.id.nav_view)
navigationView.setupWithNavController(navController)
```

## Navigationトランジションの変更

Codelabの説明では、デフォルトでトランジションアニメーションはしないと書かれてありますが、実際には navigation-ui:1.0.0-alpha01のバージョンで、アニメーションします。Frgamentが表示されるときは、alphaが0->1にFragmentが非表示になるときは、alphaが1->0になるようなアニメーションです。


このアニメーションを`NavOptions`を使って変更することができます。`NavOptions`を生成するにはBuilderパターンで生成し、上書きしたいアニメーションをセットします。生成した`NavOptions`を`NavController`の`navigate`メソッドの引数に渡します。

```kotlin
val options = NavOptions.Builder()
    .setEnterAnim(R.anim.slide_in_right)
    .setExitAnim(R.anim.slide_out_left)
    .setPopEnterAnim(R.anim.slide_in_left)
    .setPopExitAnim(R.anim.slide_out_right)
    .build()

view.findViewById<Button>(R.id.navigate_dest_bt)?.setOnClickListener {
    findNavController(it).navigate(R.id.flow_step_one, null, options)
}
```

## 画面遷移にactionを使う

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-actions.png" />
</div>

矢印のところがActionです。

Actionを使用すると、
Navigation Graphで、アプリのすべての遷移を見ることができます。
また、特定の遷移先を指定する代わりに、Actionを指定することで、遷移できます。

たとえば、"next_action"と呼ぶ、別の画面に遷移するアクションを定義できます。

```xml
<fragment
    android:id="@+id/launcher_home"
    android:name="com.example.android.codelabs.navigation.MainFragment">
    <action
        android:id="@+id/next_action"
        app:destination="@+id/flow_step_one" />
</fragment>
<fragment
    android:id="@+id/flow_step_one"
    android:name="com.example.android.codelabs.navigation.FlowStepFragment">
    <action
        android:id="@+id/next_action"
        app:destination="@+id/flow_step_two"/>
</fragment>

<fragment
    android:id="@+id/flow_step_two"
    android:name="com.example.android.codelabs.navigation.FlowStepFragment">
    <action
        android:id="@+id/next_action"
        app:popUpTo="@id/launcher_home"/>
</fragment>
```

- "next_action"というアクションIDが3つのFragment遷移先に定義されています。
- アクションの遷移先はそれぞれ異なっています。

アクションは、それぞれに属している遷移先のスコープになるため、異なる遷移先でも1つのアクションで振る舞いを変更できます。


アクションを使って遷移するには、まずNavigation Editorで、遷移元から遷移先にドラッグ&ドロップでアクションを作成し、
`navigate`メソッドのいままで遷移先IDを指定していたところをactionのIDに変更するだけです。

```kotlin
view.findViewById<Button>(R.id.navigate_action_bt)?.setOnClickListener(
    Navigation.createNavigateOnClickListener(R.id.next_action, null)
)
```

Actionにはトランジションアニメーションも設定できます。これで、プログラム側でNavOptionsを使わなくてもXMLで設定できるので、プログラムがシンプルになります。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-action-transition-animation.png" />
</div>

Navigation GraphのXMLをみると、次のようなイメージになります。

```xml
<fragment android:id="@+id/launcher_home"...>
        
        <action android:id="@+id/next_action"
            app:destination="@+id/flow_step_one"
            app:enterAnim="@anim/slide_in_right"
            app:exitAnim="@anim/slide_out_left"
            app:popEnterAnim="@anim/slide_in_left"
            app:popExitAnim="@anim/slide_out_right" />
```

## Destination間のデータの渡し方

Desitination間でのデータの渡し方には2通りあります。
1つはBundleオブジェクトを使う方法と、safeargs Gradleプラグインを使う方法です。プラグインを使うほうが硬安全に渡せます。

## Bundleオブジェクトでデータを渡す

Navigation Editorで、データが渡される側のDestinationを選択して、AttributesのArgumentのプラス記号をクリックします。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-bundle-argument.png" />
</div>

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-bundle-argument-add.png" />
</div>

Bundleのkeyとなるnameやデフォルト値を入力します。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-bundle-argument-added.png" />
</div>

XMLでは次のようになります。

```xml
<fragment
   android:id="@+id/confirmationFragment"
   android:name="com.example.cashdog.cashdog.ConfirmationFragment"
   android:label="fragment_confirmation"
   tools:layout="@layout/fragment_confirmation">
   <argument android:name="amount" android:defaultValue=”0” />
```

データを渡す方のFragmentに次のように書きます。

```kotlin
val bundle = Bundle().apply { putInt("amount", amount) }
view.findNavController().navigate(R.id.confirmationAction, bundle)
```

データを受け取る側のFragmentでいままでのように`getArguments()`を使ってargumentを取得し、同じkeyでデータを取り出します。

```kotlin
arguments?.getString("amount")
```

## 型安全にデータを受け渡す

Navigation Architecture Componentは、`safe args`と呼ぶGradleプラグインも提供しています。

これは、次のようなコードを取り除くためのものです。

```kotlin
arguments?.getString("amount")
```


プロジェクトトップのbuild.gradleに
```groovy
classpath 'android.arch.navigation:navigation-safe-args-gradle-plugin:1.0.0-alpha01'
```

を記述して

app/build.gradleに `androidx.navigation.safeargs`を記述します。

```groovy
apply plugin: 'com.android.application'
apply plugin: 'androidx.navigation.safeargs'
```


```xml
<fragment
    android:id="@+id/flow_step_one"
    android:name="com.example.android.codelabs.navigation.FlowStepFragment"
    tools:layout="@layout/flow_step_one_fragment">
 <argument
     android:name="step"
     app:type="integer"
     android:defaultValue="1"/>
</fragment>
```

safeargsプラグインを使用すると、上記のようなargumntがある場合、`FlowStepFragmentArgs`クラスが自動生成されます。
`android:name="step"`と指定されているので、`FlowStepFragmentArgs`クラスは、get/set可能な`step`変数を持っています。

```kotlin
val step = arguments?.let {
    FlowStepFragmentArgs.fromBundle(it).step
}
```

データを渡す側も自動生成されたクラスを使うことができます。

actionをもつDestinaitionのクラス名に`Directions`が付いたクラスが生成されます

```xml
<fragment
    android:id="@+id/launcher_home"
    android:name="com.example.android.codelabs.navigation.MainFragment"
    android:label="@string/home"
    tools:layout="@layout/main_fragment">

    <action
        android:id="@+id/next_action"
        app:destination="@id/flow_step_one"
        app:enterAnim="@anim/slide_in_right"
        app:exitAnim="@anim/slide_out_left"
        app:popEnterAnim="@anim/slide_in_left"
        app:popExitAnim="@anim/slide_out_right" />
</fragment>
```

たとえば、上記のような場合、`MainFragmentDirections`が自動生成されます。

`app:destination="@id/flow_step_one"`となっていて、`flow_step_one`のDestinationをみると

```xml
<fragment
    android:id="@+id/flow_step_one"
    android:name="com.example.android.codelabs.navigation.FlowStepFragment"
    tools:layout="@layout/flow_step_one_fragment">
 <argument
     android:name="step"
     app:type="integer"
     android:defaultValue="1"/>
</fragment>
```
でした。`step`というargumentがあるので、`MainFragmentDirections`には、`step`変数を持っています。


```kotlin
view.findViewById<Button>(R.id.navigate_dest_bt)?.setOnClickListener {
    val navDirections = MainFragmentDirections.next_action().setStep(1)
    findNavController(it).navigate(navDirections, options)
}
```

## Navigation Graphのグループ化

一連のDestinaitionをサブグラフとしてグループ化できます。そのサブグラフを`Nested Graph`と呼び、それを含んでいるグラフを`root graph`といいます。Nested Graphは、別れたログイン画面のようなアプリの画面を再利用したり、整理するのに役に立ちます。

Nested graphを作成するには、DestinationをShift押しながら選択し、Context menuから作成します。

<div align="center">
<img src="/images/2018/05/android-jetpack/navigation-nested-graph.png" />
</div>


nested graphにもidを割り当てるので、そのidを使用して、navigateします。
actionを使ってnavigateしている場合は、特に変更の必要は無いかと思います。

## ディープリンクを作成する

Deeplinkを追加するには、Navigation Graphで Deeplinkから遷移したいDestinationを選択し、AttributeのDeep LinksでURLを追加します。

```xml
<fragment
    android:id="@+id/android"
    android:name="com.example.android.codelabs.navigation.DeepLinkFragment"
    android:label="@string/deeplink"
    tools:layout="@layout/deeplink_fragment">

    <argument
        android:name="myarg"
        android:defaultValue="Android!"/>
    <deepLink app:uri="www.example.com/{myarg}"/>
    
</fragment>
```


次にAndroidManifest.xmlのActivityに`<nav-graph android:value="@navigation/mobile_navigation"/>`を追加します。

```xml
<activity
   android:name=".MainActivity">
   <intent-filter>
       <action android:name="android.intent.action.MAIN"/>
       <category android:name="android.intent.category.DEFAULT"/>
       <category android:name="android.intent.category.LAUNCHER" />
   </intent-filter>
   <nav-graph android:value="@navigation/mobile_navigation"/>
</activity>
```


確認するには、ターミナルでadb shellのamコマンドで確認できます。アプリが立ち上がり、Deep Linkを設定したDestinationに遷移することが確認できます。

```shell
% adb shell am start -W -a android.intent.action.VIEW -d "https://www.example.com/abcd/" com.example.android.codelabs.navigation
```


# 参考
- codelab
    - https://codelabs.developers.google.com/codelabs/android-navigation/index.html?index=..%2F..%2Findex#5
- developer
    - https://developer.android.com/topic/libraries/architecture/navigation/navigation-implementing

- blog
    - https://proandroiddev.com/android-navigation-arch-component-a-curious-investigation-3e56e24126e1
# その他

Pagingライブラリソース
https://android.googlesource.com/platform/frameworks/support/+/master/paging/common/src/main/java/android/arch/paging/PagedList.java