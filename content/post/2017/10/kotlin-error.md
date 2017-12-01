+++
date = "2017-10-15T21:18:00+09:00"
title = "Kotlin kapt error"
draft = false
categories = ["Kotlin"]
tags = ["Kotlin"]

+++

## 困ったこと

Kotlin + Dagger2を使って、下記のようなエラーが出た。

<img src="/images/2017/10/kotlin-error/kaptDevelopDebugKotlin.png">


>  See log for more details

とあるけど、どこ見たらいいのでしょうか。。。


## 対策

Android Studioからは見れないので、ターミナルでassembleDevelopDebugを実行してみましょう。

```
% ./gradlew assembleDevelopDebug   
```

すると下記のような感じで詳細なエラーログを見ることができます。

```
:app:kaptDevelopDebugKotlin
e: app/build/tmp/kapt3/stubs/developDebug/net/kwmt27/codesearch/presentation/internal/di/components/AppComponent.java:6: エラー: [dagger.android.AndroidInjector.inject(T)] java.util.Map<java.lang.Class<? extends android.support.v4.app.Fragment>,javax.inject.Provider<dagger.android.AndroidInjector.Factory<? extends android.support.v4.app.Fragment>>> cannot be provided without an @Provides-annotated method.
e: 

e: public abstract interface AppComponent extends dagger.android.AndroidInjector<net.kwmt27.codesearch.presentation.App> {
e:                 ^
e:       java.util.Map<java.lang.Class<? extends android.support.v4.app.Fragment>,javax.inject.Provider<dagger.android.AndroidInjector.Factory<? extends android.support.v4.app.Fragment>>> is injected at
e:           dagger.android.DispatchingAndroidInjector.<init>(injectorFactories)
e:       dagger.android.DispatchingAndroidInjector<android.support.v4.app.Fragment> is injected at
e:           dagger.android.support.DaggerAppCompatActivity.supportFragmentInjector
e:       net.kwmt27.codesearch.presentation.view.MainActivity is injected at
e:           dagger.android.AndroidInjector.inject(arg0)
e: java.lang.IllegalStateException: failed to analyze: org.jetbrains.kotlin.kapt3.diagnostic.KaptError: Error while annotation processing
```

ちなみに、このエラーの原因は

AppComponentクラスで指定している Moduleがsupportではない `AndroidInjectionModule` クラスの方を指定しているため。

```
@Component(modules = arrayOf(
        AndroidInjectionModule::class,
    )
)
interface AppComponent : AndroidInjector<App> {
```

対策は、上記の詳細からsupport.Fragment DispatchingAndroidInjectorとあることから、

`AndroidInjectionModule` を `AndroidSupportInjectionModule`にすればよい。


参考リンク: https://github.com/google/dagger/issues/783

## まとめ

Android Studioでエラーログ見れるようにならないかな。。コントリビュートすればいいんでしょうけど。
