+++
title= "Android Lifecycle関連コンポーネントについて"
date= 2019-09-19T21:10:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android","Kotlin","Lifecycle","ViewModel","LiveData"]
keywords = [""]
+++


# はじめに

Android Lifecyleのコードラボをやりつつ、ViewModelやLiveDataの仕組みを調べたのでメモしておこと思います。

https://codelabs.developers.google.com/codelabs/android-lifecycles

コードラボのコードベースでいろいろ試したソースはこちら

https://github.com/kwmt/android-lifecycles


# Step1,2 ViewModel
step1はアプリが起動したらタイマーがカウントアップし、画面回転するとタイマーのカウントがクリアされてしまう、という問題にViewModelというものを使ってどう対処するのかという話です。

## ViewModelとは
[ViewModel](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html)を使えば、ActivityやFragmentのライフサイクル全体でデータを保持できます。
ActivityやFragmentは、ユーザーの操作でCreatedとDestroyedを繰り返す生存期間の短いオブジェクトです。


ViewModelクラスを作成するには、`ViewModel()`を継承します。（中身は空っぽですが）

```kotlin
class ChronoViewModel: ViewModel() {
}
```

`ViewModel`は、ViewModelインスタンスが破棄されたときに呼ばれる`onCleared`というメソッドを持っています。

### ViewModelインスタンスの取得方法

このViewModelインスタンスをActivityやFragmentで取得するには、

```kotlin
private val viewModel by viewModels<ChronoViewModel>()
```

のようにKotlin property delegateを使って取得するか、

```kotlin
viewModel = ViewModelProvider(this).get(ChronoViewModel::class.java)
```

このように取得することになります。


以前は、`ViewModelProvider.of(activity)`のように取得していましたが、lifecycleのバージョン2.2.0-alpha03からDeprecatedになっていました。
https://developer.android.com/jetpack/androidx/releases/lifecycle#2.2.0-alpha03

また、kotlin propertey Delegationを使うにはfragment-ktxの1.1.0以上を使う必要があります。

```gradle
implementation "androidx.fragment:fragment-ktx:1.1.0"
```
https://developer.android.com/jetpack/androidx/releases/fragment#1.1.0


## ViewModelの生存期間

ViewModelの生存期間はActivityやFragmentよりも長いです。
下図はActivityを起動して、画面を回転させ、バックボタンでアプリを終了したときの状態遷移です。

```
起動
    onCreate
        viewModel created(正確にはLazyなのでアクセスしたタイミング)
    onStart
    onResume
回転
    onPause
    onStop
    onDestroy
    onCreate
    onStart
    onResume
バックボタンでアプリ終了
    onPause
    onStop
        viewModel onCleared
    onDestroy
```

画面回転時に、Activityは`onDestroy`されていますが、ViewModelは`onCleared`が呼ばれません。


## ViewModelの仕組み

ここで、

1. ViewModelがどのような仕組みで保持されているか
1. 画面回転時にも`onDestroy`が呼ばれているにも関わらず`onCleared`は呼ばれず、アプリ終了時にのみ`onCleared`が呼ばれている仕組み

をみてみます。

### 1. ViewModelがどのような仕組みで保持されているか

まず、Activityは

```
ChronoActivity -> AppCompatActivty -> FragmentActivity -> ComponentActivity -> Activity
```

このような継承関係になっています。

おそらく`ActivityThread`クラスにあるstaticな`sCurrentActivityThread`変数がActivityが破棄されても同じインスタンスが取得できる大元のインスタンスだと思います。(これは間違っている可能性大です・・・（汗）)
https://android.googlesource.com/platform/frameworks/base.git/+/refs/heads/android10-release/core/java/android/app/ActivityThread.java#351

- アプリ起動時

onCreate時に`ActivityThread`の`mActivities`に`ActivityClientRecord`(Activity含む)を保持します。
https://android.googlesource.com/platform/frameworks/base.git/+/refs/heads/android10-release/core/java/android/app/ActivityThread.java#3260

- 画面回転し始めからonDestroyまで

Configurationの変更が発生（画面が回転やシステムの言語変更）すると、
`onSaveInstanceState`で最後の構成状態`lastNonConfigurationInstances`を取得して保持し、`onDestroy`になります。
https://android.googlesource.com/platform/frameworks/base.git/+/refs/heads/android10-release/core/java/android/app/ActivityThread.java#4913

- 再生成(onCreate)

`onCreate`時に`ActivityThread`の`mActivies`の保持しておいた`ActivityClientRecord`インスタンスを取得して、そこに`lastNonConfigurationInstances`という最後の構成状態を取得し、`Activity`の`mLastNonConfigurationInstances`にセットします。
https://android.googlesource.com/platform/frameworks/base.git/+/refs/heads/android10-release/core/java/android/app/Activity.java#7738

`ComponentActivity#getViewModelStore()`が呼ばれと内部で`getLastNonConfigurationInstance()`が呼ばれていて、
https://android.googlesource.com/platform/frameworks/support/+/androidx-master-dev/activity/activity/src/main/java/androidx/activity/ComponentActivity.java#187

これは`mLastNonConfigurationInstances`がnullでなければ`mLastNonConfigurationInstances.activity`(これがViewModelの実態)を返すようになっています。
https://android.googlesource.com/platform/frameworks/base.git/+/refs/heads/android10-release/core/java/android/app/Activity.java#2876

自分のActivityでviewModelをインスタンスにアクセスするとき、`ComponentActivity#getViewModelStore()`が呼ばれ、ViewModelはキャッシュされているものを返す仕組みになっています。


### 2. 画面回転時にも`onDestroy`が呼ばれているにも関わらず`onCleared`は呼ばれず、アプリ終了時にのみ`onCleared`が呼ばれている仕組み

画面回転時に、Activityのメンバ変数`mChangingConfigurations`に`true`がセットされます。
https://android.googlesource.com/platform/frameworks/base.git/+/refs/heads/android10-release/core/java/android/app/ActivityThread.java#5165

LifeCycleのState変更が監視されていて、`onDestroy`にStateが変わると、onStateChengedが呼ばれて、`mChangingConfigurations`を返すメソッド `isChangingConfigurations()`で画面回転中か確認し、true(回転中)なら何もぜず、false(回転中でははないonDestory=Activity終了)なら、`onCleared`メソッドを呼ぶという仕組みになっています。
https://android.googlesource.com/platform/frameworks/support/+/androidx-master-dev/activity/activity/src/main/java/androidx/activity/ComponentActivity.java#118


## 画面回転でもタイマーリセットされないように変更する

ViewModelはActivityより生存期間が長いことがわかりましたので、ViewModelに保持したいタイマーの時間を保持します。

```kotlin
class ChronoViewModel : ViewModel() {
    var startTime: Long? = null
}
```

Activity側ではonCreate時に、startTimeがnullなら時間を新規にセットして、nullじゃない（開店時）なら、ViewModelのstartTimeをセットすればよいです。

Activity側のコードは[Step2](https://github.com/googlecodelabs/android-lifecycles/blob/master/app/src/main/java/com/example/android/lifecycles/step2/ChronoActivity2.java#L41-L50)のコードをKotlinで書いただけなので割愛。そういう意味ではViewModelもそうなのですが、ポイントはViewModelの方なので。


# Step3 LiveData

次は`Timer`クラスを使って、UIを1秒間隔で「~秒経過」と表示するということを考えます。

## 画面回転でメモリリークしない？

ちょっとその前に気になることが、、、

> To help avoid memory leaks, the ViewModel doesn't include references to the activity. For example, a configuration change, such as a screen rotation, might result in references in a ViewModel to an activity that should be garbage collected

> メモリリークを避けるために、ViewModelにAactivityの参照を持たないようにしてください。
たとえば、画面回転などのコンフィグ変更すると、GCすべきActivityの参照が破棄されないかもしれません。

と書いてるんですが、画面回転ではメモリリークしませんでした。


ViewModelはActivityをもたせて
```kotlin
class ChronoViewModel : ViewModel() {
    var activity: ChronoActivity? = null
}
```

ActivityでViewModelのactivityに自分自身をセットし

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    // 省略
    viewModel.activity = this
}    
```

画面回転させたところ、LeakCanaryは検知しませんでした。(API level 29で確認しました。)

しかし、アプリ終了でリークしました。

```
    ├─ com.example.android.lifecycles.practice.ChronoViewModel
    │    Leaking: UNKNOWN
    │    ↓ ChronoViewModel.activity
    │                      ~~~~~~~~
    ╰→ com.example.android.lifecycles.practice.ChronoActivity
    ​     Leaking: YES (Activity#mDestroyed is true and ObjectWatcher was watching this)
    ​     key = b9316fdf-3fe3-406a-b73e-d63d68a42b2a
    ​     watchDurationMillis = 5193
    ​     retainedDurationMillis = 152
```


まぁメモリリークはするので、ViewModelでActivityやViewの参照はするべきではないと理解。

ホントはViewModelにViewを渡して、次のような感じでViewModelでViewを更新できたら良かったんでしょうけど、上記の通りメモリリークするので、Activity側でUIを更新しないといけないので、それをどうするかっていうのがstep3の[`LiveData`](https://developer.android.com/reference/android/arch/lifecycle/LiveData)を使いましょうというお話。

## 本題に戻ります！

UIを1秒間価格で更新したいということでしたね。

```kotlin
class ChronoViewModel : ViewModel() {
    var timerTextView: TextView? = null

    init{ 
        // 毎秒ごとに更新できない
        timerTextView.text = "$秒数 秒経過"
    }
}
```

まず、LiveDataに値を設定できる`MutableLiveData`とそれをLiveDataとして返す関数を作成します。

```kotlin
private val elapsedTime = MutableLiveData<Long>()
fun getElapsedTime(): LiveData<Long> = elapsedTime
```

そして、1秒毎に更新したいので、Timerクラスを使って実装します。

```kotlin
class ChronoViewModel : ViewModel() {

    private val elapsedTime = MutableLiveData<Long>()
    fun getElapsedTime(): LiveData<Long> = elapsedTime

    private val initialTime = SystemClock.elapsedRealtime()
    private val timer = Timer()

    init {
        timer.scheduleAtFixedRate(object : TimerTask() {
            override fun run() {
                val newValue = (SystemClock.elapsedRealtime() - initialTime) / 1000;
            }
        }, 1000, 1000)
    }
}
```

ここまで来ると、なんとなく`newValue`をMutableLiveDataの`elapsedTime`にセットするんだなって思うと思うので、セットします。

```kotlin
val newValue = (SystemClock.elapsedRealtime() - initialTime) / 1000;
elapsedTime.value = newValue
```

すると、`setValue`はバックグラウンドスレッドで呼び出すことができないエラーが出ます。


```
java.lang.IllegalStateException: Cannot invoke setValue on a background thread
    at androidx.lifecycle.LiveData.assertMainThread(LiveData.java:462)
    at androidx.lifecycle.LiveData.setValue(LiveData.java:304)
    at androidx.lifecycle.MutableLiveData.setValue(MutableLiveData.java:50)
    at com.example.android.lifecycles.practice.ChronoViewModel$1.run(ChronoViewModel.kt:25)
    at java.util.TimerThread.mainLoop(Timer.java:562)
    at java.util.TimerThread.run(Timer.java:512)
```

じゃぁどうするかとというと、[`postValue`](https://developer.android.com/reference/android/arch/lifecycle/MutableLiveData.html#postValue(T))を使います。


なぜ`setValue`はメインスレッド以外禁止にしているかというと、そもそもViewModelにViewを持たせて、ViewModelでUIを更新したかったけど、リークするのでできないから、LiveDataを使いましょうという話だったので、LiveDataの役割はViewを更新することでした。

であるならば、Viewの更新はメインスレッドでしかできないので、`setValue`するときはメインスレッド以外で呼ぶのを禁止しましょう、ってなったんだと思います。(あ、UI側で`onChanged`メソッドを実装することになるので、`setValue`はそれを呼び出したスレッドで`onChanged`メソッドが呼ばれます。)
使う側で毎回 メインのHandlerのpostするのは面倒ですしね。

で、`postValue`はなぜメインスレッド以外で呼べるかというと、名前からもわかるように次のように実装されているからです。

```java
mMainHandler.post(runnable);
```

runnableは下記

```java
private final Runnable mPostValueRunnable = new Runnable() {
    @SuppressWarnings("unchecked")
    @Override
    public void run() {
        Object newValue;
        synchronized (mDataLock) {
            newValue = mPendingData;
            mPendingData = NOT_SET;
        }
        setValue((T) newValue);
    }
};
```


ぜんぶ`postValue`使ったら、使う側でスレッド意識しなくていいから、ぜんぶ`postValue`じゃダメなんですかね？  
という疑問が最近あるのですが。。。


さて、ViewModel側でMutableLiveDataにpostValue()するところまで来ました。
まだUIを更新していませんので、次にActivityをみていきます。

Activityで、ViewModelのelapsedTime(MutableLiveData)を使って、

```kotlin
viewModel.elapsedTime.value
```

でpostValueされたデータは取得できます。しかし、今回は毎秒ごとに更新したいので、そのためにはLiveDataを監視するという実装が必要になります。具体的には次のようになります。

```kotlin
viewModel.getElapsedTime().observe(this, object : Observer<Long> {
    override fun onChanged(t: Long?) {
        timerTextView.text = "${t}秒経過"
    }
})
```

`viewModel.getElapsedTime()`でLiveDataを取得して、それに対して`observe`メソッドで監視します。
ViewModelのMutableLiveDataが`postValue`されたら、`onChanged`メソッドが実行され、TextViewを更新するという流れなります。


余計なViewが入っていますが、実行することこうなります。

<img src="/images/2019/09/android-lifecycle-codelab/livedata.gif" width="300">


もう少しだけ補足が必要で、まず`viewModel.getElapsedTime()`は、`MutableLiveData`ではなく`LiveData`を返します。これは、Activity側では更新する必要がないため、不必要な更新ができてしまうことさけるためにこのようにしています。

次に、`observe`メソッドの第一引数に渡している`this`は、`LifecycleOwner`を渡しています。
このコードラボでは`LifecycleRegistryOwner`となっていますが、[version 1.1.0でDeprecatedになっていました](https://developer.android.com/reference/android/arch/lifecycle/LifecycleRegistryOwner.html)。


`LifecycleOwner`はViewModelとLiveDetaのライフサイクルをバインドするためのもので、ChronoActivityの親クラスの`ComponentActivity`が実装しているインターフェースです。

```java
class ChronoActivity extends AppCompatActivity extends FragmentActivity extends ComponentActivity extends androidx.core.app.ComponentActivity implements LifecycleOwner
```

注意）Activityがアクティブな状態でないと、UIは更新されないことに注意してください。
仕組み的にはアクティブになっていない場合は、`onChanged`が呼ばれ無いだけです。


LiveDataのprivateメソッドに次のようなものがあり、`if (!observer.mActive)`で判断しています。

```java
private void considerNotify(ObserverWrapper observer) {
    if (!observer.mActive) {
        return;
    }
    if (!observer.shouldBeActive()) {
        observer.activeStateChanged(false);
        return;
    }
    if (observer.mLastVersion >= mVersion) {
        return;
    }
    observer.mLastVersion = mVersion;
    observer.mObserver.onChanged((T) mData);
}
```

ちなみに、Activityがアクティブでないときも`onChanged`を呼びたい場合は、[`observeForever`](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html#observeForever(android.arch.lifecycle.Observer<T>))というメソッドが用意されてるので、そちらを使うと良さそうです。


また、observeの実装を説明のためにObserverのインターフェース実装のような形で書きましたが、次のようにlambdaを使って書くと少しスッキリします。


```kotlin
viewModel.getElapsedTime().observe(this, Observer {
    timerTextView.text = "${it}秒経過"
})
```

これでstep3は終了です。

# step4 Lifecycle

たとえば、現在地を取得するためには [`LocationManager`](https://developer.android.com/reference/kotlin/android/location/LocationManager)を使うと思います。
このようなAPIは、使うために初期化したりsubscribeしたりし、使わなくなったらストップしたり、unsubscribeします。

```java
@Override
protected void onResume() {
    mLocationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, 0, 0, mListener);
}

@Override
protected void onPause() {
    mLocationManager.removeUpdates(mListener);
}
```

AndroidのライブラリやAPIはこのような処理をすることが多く、ActivityやFragmentで処理を書くと煩雑になってしまい、読みにくくなってしまいます。このようなことを防ぐために[`Lifecycle`](https://developer.android.com/topic/libraries/architecture/lifecycle)を使うと便利ですよ、という話になります。

簡単に言うと、ActivityやFragment以外のクラスでライフサイクルの変更を検知することができます。
このコードラボでは、`BoundLocationListener`クラスがライフサイクルの変更を検知できるようになる予定なのですが、どのような仕組みかというと、まず`BoundLocationListener`にActivityの`lifecycleOwner`インスタンスを渡して、LifecCycleを次のように監視します。(`lifecycleOwner`は`ComponentActivity`が実装しているインターフェースでしたね)

```kotlin
lifecycleOwner.lifecycle.addObserver(this)
```

このときの`this`は`LifecycleObserver`インターフェースで、`BoundLocationListener`が実装する必要があります。（といっても特に実装するものはありません）

次に、ライフサイクルが変更したときに指定したメソッドをコールするには、次のように`@OnLifecycleEvent`アノテーションを付けます。

```kotlin
@OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
fun addLocationListener() {
    ・・・　
}
@OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
fun removeLocationListener() {
    ・・・　
}
```

これだけで、ActivityやFragmentのライフサイクルと連動できるなんて、超便利そうですね！
ちなみに、軽く内部実装みたら、リフレクション使ってますね。まぁそりゃそうかという感じですが。

ここで少し気になるのが、`init`で`addObserver(this)`としているから、デストラクタとかで`removeObserver(this)`としなくていいのかということです。

調べたところ、`removeObserver`はしなくて良いそうです。

https://github.com/googlecodelabs/android-lifecycles/issues/5

内部実装をみると、onDestroy時に`removeObserver`が呼ばれていました。



# step5 ViewModelの共有

次はViewModelがFramgment間や、FragmentとActivity間でシェアできるよというお話。

１つのActivityに２つのシークバーを持つFragmentが上下に並んでいて、ViewModelを使うと、片方のシークバーを移動すると、もう片方のシークバーが同期的に動くようにできるらしい。

ViewModelインスタンスを取得する際、FragmentのLifecycleOwnerを渡すのではなく、Fragmentが属しているActivityのLifecycleOwnerを渡してあげれば良い。


いままでは次のようにViewModelインスタンスを取得していた。


```kotlin
private val viewModel by viewModels<ChronoViewModel>()
```


これは、実装を見ればわかりますが、Fragment自身のLifecycleOwnerを渡していました。

以下の `{this}`のところ。


```kotlin
@MainThread
inline fun <reified VM : ViewModel> Fragment.viewModels(
    noinline ownerProducer: () -> ViewModelStoreOwner = { this },
    noinline factoryProducer: (() -> Factory)? = null
) = createViewModelLazy(VM::class, { ownerProducer().viewModelStore }, factoryProducer)
```

この部分をFragmentではなくActivityに変えたら良いので、次のように`{ requireActivity() }`を渡してあげます。

```kotlin
private val viewModel: SeekBarViewModel by viewModels({ requireActivity() })
```

あとは、シークバーの変更時に、LiveDataの値を更新してあげて

```kotlin
override fun onProgressChanged(seekBar: SeekBar, progress: Int, fromUser: Boolean) {
    mSeekBarViewModel.seekbarValue.value = progress
}
```

その更新するLiveDataを監視して、更新されたシークバーの値を更新してあげると、

```kotlin
mSeekBarViewModel.seekbarValue.observe(viewLifecycleOwner, Observer {
    mSeekBar?.progress = it
})
```

次のような動画のように、片方動かすと片方も動くようになります。

<img src="/images/2019/09/android-lifecycle-codelab/share_viewmodel.gif" width="300">




# まとめ

- ViewModelはActivityやFragmentより、生存期間が長いので、画面回転などのActivity再生成でも回転前と後で値を保持できる。
- LiveDataを使って、ViewModelでの更新をActivityやFragmentに通知できる。
- LifecycleはActivityやFragment以外のクラスでライフサイクル管理ができるので、ActivityやFragment生成時にubscribeし、破棄時にunsubscribeするようなコンポーネントで使うとスッキリできる。
- ViewModelはFragment間やFragmentとActivity間で共有できる。


