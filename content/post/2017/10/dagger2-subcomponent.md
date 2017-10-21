+++
date = "2017-10-21T21:00:00+09:00"
title = "Dagger2でProvidesアノテーションつけてないのに、なぜフィールド注入されるのか"
draft = false
categories = ["Android"]
tags = ["Android", "Dagger2"]

+++


## Dagger2について分かってなかったこと

```kotlin
class MainActivity : DaggerAppCompatActivity() {
    @Inject
    lateinit var viewModel: MainViewModel
}
```

MainActivityに`viewModel`を保持させてくて、`@Inject`アノテーションを付けてあげれば、インスタンスを注入してくれてる。

インスタンス注入するには、以下のような準備は必要です。

下記のように`ActivityModule`と`AppComonent`を定義し、`ActivityModule`を`AppComponent`の`@Component(modules=ActivityModule::class)`に指定します。

```kotlin
@Singleton
@Component(modules = arrayOf(
        AndroidSupportInjectionModule::class,
        AppModule::class,
        ActivityModule::class
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

```kotlin
@Module
abstract class ActivityModule {

    @ActivityScope
    @ContributesAndroidInjector
    internal abstract fun contributeMainActivity(): MainActivity

}
```

`ActivityScope`も定義が必要だし、

```kotlin
@Scope
@Retention(AnnotationRetention.RUNTIME)
@MustBeDocumented
annotation class ActivityScope
```

`AndroidManifest.xml`に`App`を指定する必要がありますし、

```xml
<application
        android:name=".App"
```

そもそも`App`は、Applicationを継承したクラスで、

```kotlin
open class App : DaggerApplication() {
    override fun applicationInjector(): AndroidInjector<out DaggerApplication> {
        return  DaggerAppComponent.builder().application(this).build()
    }
}
```

のように定義されてます。最後に`MainViewModel`は

```kotlin
@ActivityScope
class MainViewModel @Inject
constructor() : BaseObservable() {

    fun onClickButton(view: View) {
        Log.d("MainViewModel", "onclick")
    }
}
```

のような感じで定義しています。


また、Activityとは独立した`GithubClient`クラスを作成しているのですが、それをInjectしたい場合、

```kotlin
@Module
class AppModule() {
    @Singleton
    @Provides
    fun provideGithubApi(client: OkHttpClient, moshi: Moshi): GithubApi {
        return Retrofit.Builder()
                .client(client)
                .baseUrl(BuildConfig.BASE_API_URL)
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .addConverterFactory(MoshiConverterFactory.create(moshi))
                .build()
                .create(GithubApi::class.java)
    }
}
```

のように`@Provides`アノテーションを付ければ、インスタンスを提供できると理解していました。

では、`MainViewModel`も`@Provides`アノテーションをつけないとインスタン注入出来ないんじゃないの？と思いながらも、実際注入できてるので、なんでかな？というのがずっと気になってました。

## 答えはMainActivityのSubComponentだから

と書こうとしたのですが、その根拠をよくよく調べてみると、いまは分からなくなってきましたので、たぶん、にしておきます。

おそらく下記ページのSubcomponentの項目を読めばなんとなくわかると思うのですが。。

https://google.github.io/dagger/api/latest/dagger/Component.html


とくに分からなくなったのは、

```kotlin
class MainActivity : DaggerAppCompatActivity() {
    @Inject
    lateinit var viewModel: MainViewModel
}
```

と、`@Inject`しただけで、`Subcomponent`とみなしてくれるかどうかが、怪しくなってきました。。今度調べます。。


いまは自分の中でここまでわかったということで、この記事は締めたいと思います。


