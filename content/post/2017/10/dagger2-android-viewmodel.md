+++
date = "2017-10-17T10:00:00+09:00"
title = "Dagger2 + Android + ViewModel(with databindin)"
draft = false
categories = ["Android"]
tags = ["Android", "Dagger2"]

+++

## はじめに

daggerバージョンが2.10になったときに、[`dagger.androd`クラスがリリースされた](https://github.com/google/dagger/releases/tag/dagger-2.10)ようで、いままでと少し書き方が変わっていたので、メモを書きました。


基本的に下記ページを参考にしています。

[https://google.github.io/dagger/android.html](https://google.github.io/dagger/android.html)


## dagger.android

MainActivityが依存しているインスタンスを注入できるようにしてみます。

1. AndroidInjectionModule(あるいは、Support componentを使う場合は AndroidSupportInjectionModule)をアプリケーションコンポーネントに指定してください。

    ```kotlin 
    @Singleton
    @Component(modules = arrayOf(
            AndroidSupportInjectionModule::class
        )
    )
    interface AppComponent : AndroidInjector<App> {

        @Component.Builder
        interface Builder {
            @BindsInstance
            fun application(application: App): Builder

            fun build():AppComponent

        }
    }
    ```

2.  `AndroidInjector.Builder<YourActivity>`を継承している`@Subcomponent.Builder`を持つ `AndroidInjector<YourActivity>` を実装する`@Subcomponent` を作成します。

    ```kotlin
    @Subcomponent
    interface MainActivitySubComponent:AndroidInjector<MainActivity> {
        @Subcomponent.Builder
        abstract class Builder : AndroidInjector.Builder<MainActivity>()
    }
    ```

3. subcomponentを定義したら、Subcomponent.Builderをバインドするモジュールを定義(ここでは`MainActivityModule`)し、

    ```kotlin
    @Module(subcomponents = arrayOf(MainActivityModule.MainActivitySubComponent::class))
    abstract class MainActivityModule {

        @Binds
        @IntoMap
        @ActivityKey(MainActivity::class)
        internal abstract fun bindAndroidInjectorFactory(
                builder: MainActivityModule.MainActivitySubComponent.Builder): AndroidInjector.Factory<out Activity>
    }
    ```

    そのモジュール(`MainActivityModule`)をアプリケーションコンポーネント(ここでは`AppComponent`)に追加します。


    ```
    @Singleton
    @Component(modules = arrayOf(
            AndroidSupportInjectionModule::class,
            MainActivityModule::class
        )
    )
    interface AppComponent : AndroidInjector<App> {
    // 省略
    }
    ```


4. `Appplication` クラスを継承したクラス(ここでは`App`)で `HasActivityInjector`インターフェースを実装し、 `DispatchingAndroidInjector<Activity>` を　`@Injectて`し、` activityInjector()` メソッドでそれを返します。

    と、書かれていますが、daggerがBaseクラスを用意してくれていて、

    `DaggerApplication`クラスを継承して、` activityInjector()`メソッドを実装すると良いみたいです。つまり

    ```
    open class App : DaggerApplication() {
        override fun applicationInjector(): AndroidInjector<out DaggerApplication> {
            return  DaggerAppComponent.builder().application(this).build()
        }
    }
    ```
    とします。

5. 最後にAcitivyのonCreateで`super.onCreate()`の前に`AndroidInjection.inject(this)`を呼んでください。

    とあるのですが、これもDaggerが用意してくれているベースクラス`DaggerAppCompatActivity` を継承すれば、`AndroidInjection.inject(this)`を書くなくても`DaggerAppCompatActivity`でやってくれています。

これで基本は完了です。

## ViewModel(databinding)のインスタンスを注入できるようにしてみます。

ViewModelクラスを作成します。

```kotlin
class MainViewModel : BaseObservable() {
    fun onClickButton(view: View) {
        Log.d("MainViewModel", "onclick")
    }
}
```

このViewModelは下記のactivity_main.xmlにバインドしたいため、次のように`layout`タグを入れています。

```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
    >
    <data>
        <variable
            name="viewModel"
            type="net.kwmt27.codesearch.presentation.viewmodel.MainViewModel"
            />
    </data>

    <LinearLayout>
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="button"
            android:onClick="@{viewModel::onClickButton}"
            />
    </LinearLayout>
</layout>
```

なお、`ActivityScope`アノテーションは下記のような感じで作成しています。


```kotlin
@Scope
@Retention(AnnotationRetention.RUNTIME)
@MustBeDocumented
annotation class ActivityScope
```

このViewModelをActivityでInjectできるようにするには、コンストラクタに`@Inject`アノテーションをつけ、Scopeアノテーションを付けます。

```kotlin
@ActivityScope
class MainViewModel @Inject constructor() : BaseObservable() {

    fun onClickButton(view: View) {
        Log.d("MainViewModel", "onclick")
    }

}
```

このような感じになると思います。そしてInjectしたいActivityのフィールド宣言で`@Inject`アノテーションを付けます。MainActivityは次のようになります。

```kotlin
class MainActivity : DaggerAppCompatActivity() {
    @Inject
    lateinit var viewModel: MainViewModel
}
```

あとは通常どおりにバインディングするだけです。

```kotlin
class MainActivity : DaggerAppCompatActivity() {
    @Inject
    lateinit var viewModel: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView<ActivityMainBinding>(this, R.layout.activity_main)
        binding.viewModel = viewModel
    }
}
```

## FragmentでInject可能にする

ほとんどActivityと同じですが、ちょっと書いておきます。

MainFragmentクラスを作成します。

```kotlin
@FragmentScope
class MainFragment : DaggerFragment() {

    companion object Factory {
        val TAG = MainFragment::class.simpleName!!
        fun newInstance(): MainFragment = MainFragment()
    }

    @Inject
    lateinit var viewModel: MainFragmentViewModel

    private lateinit var binding:FragmentMainBinding

    override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?,
                              savedInstanceState: Bundle?): View? {
        // Inflate the layout for this fragment
        binding = FragmentMainBinding.inflate(inflater, container, false)
        binding.viewModel = viewModel
        return binding.root
    }
}
```

このとき、レイアウトに`fragment_main.xml`を、ViewModelとして`MainViewModel.kt`を、Fragmentスコープ用に`FragmentScope.kt`を作成しています。それぞれ次のようになっています。

* fragment_main.xml

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            xmlns:tools="http://schemas.android.com/tools"
        >

        <data>

            <variable
                name="viewModel"
                type="net.kwmt27.codesearch.presentation.viewmodel.MainFragmentViewModel"
                />
        </data>

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"

            >

            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerInParent="true"
                android:onClick="@{viewModel::onClickButton}"
                android:text="Fragment Button"
                />
        </RelativeLayout>
    </layout>
    ```    

* MainFragmentViewModel.kt

    ```kotlin
    @FragmentScope
    class MainFragmentViewModel @Inject constructor() : BaseObservable() {
        fun onClickButton(view: View) {
            Log.d("MainFragmentViewModel", "onclick")
        }
    }
    ```

* FragmentScope.kt

    ```kotlin
    @Scope
    @Retention(AnnotationRetention.RUNTIME)
    @MustBeDocumented
    annotation class FragmentScope
    ```

`MainActivityModule.kt`と同様にして、`MainFragmentModule.kt`を作成します。

```kotlin
@Module(subcomponents = arrayOf(MainFragmentModule.MainFragmentSubComponent::class))
abstract class MainFragmentModule {

    @Binds
    @IntoMap
    @FragmentKey(MainFragment::class)
    internal abstract fun bindMainFragmentAndroidInjectorFactory(
            builder: MainFragmentModule.MainFragmentSubComponent.Builder): AndroidInjector.Factory<out Fragment>

    @FragmentScope
    @Subcomponent
    interface MainFragmentSubComponent: AndroidInjector<MainFragment> {
        @Subcomponent.Builder
        abstract class Builder : AndroidInjector.Builder<MainFragment>()
    }

}
```

これを`AppComponent.kt`に差し込むだけです。
```kotlin
@Singleton
@Component(modules = arrayOf(
        AndroidSupportInjectionModule::class,
        AppModule::class,
        MainActivityModule::class,
        MainFragmentModule::class // ←ここに入れた
    )
)
interface AppComponent : AndroidInjector<App> {

    @Component.Builder
    interface Builder {
        @BindsInstance
        fun application(application: App): Builder

        fun build():AppComponent

    }
}
```

FragmentをInjectableにする方法は以上です。

## 最後に

ActivityやFragmentが増えたときに、AppComponentのmodulesが太ってくる問題がありますが、これはまた後のほど考えたいと思います。どこかで`includes`みたいなのを見たので、もしかしたら

`ActivityModuels`や`FragmentModules`みたいなのを作ってそこにまとめて書けるのかもしれません。


