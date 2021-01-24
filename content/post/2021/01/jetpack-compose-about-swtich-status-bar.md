
+++
title= "Jetpack Composeでダークテーマ設定変更によるステータスバーカラーの切り替え方"
date= 2021-01-24T22:50:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose","StatusBar", "Dark Theme"]
keywords = ["Android", "Jetpack", "Compose","StatusBar", "Dark Theme"]
+++

## はじめに

Android端末でダークテーマの切り替えをしたとき、Jetpack Composeでステータスバーカラーの切りかえをする方法を調べたので、メモ。

## 方法

Jetpack Composeのサンプル、Jetsnackの[コード](https://github.com/android/compose-samples/blob/main/Jetsnack/app/src/main/java/com/example/jetsnack/ui/utils/SystemUi.kt#L77)を参考にしています。



```
interface SystemUiController {
    fun setStatusBarColor(
        color: Color,
        darkIcons: Boolean = color.luminance() > 0.5f
    )
}

fun SystemUiController(window: Window): SystemUiController {
    return SystemUiControllerImpl(window)
}

/**
 * An [androidx.compose.runtime.Ambient] holding the current [SysUiController]. Defaults to a
 * no-op controller; consumers should [provide][androidx.compose.runtime.Providers] a real one.
 */
val SysUiController = staticAmbientOf<SystemUiController> {
    FakeSystemUiController
}

private class SystemUiControllerImpl(private val window: Window) : SystemUiController {
    override fun setStatusBarColor(
        color: Color,
        darkIcons: Boolean
    ) {
        window.statusBarColor = color.toArgb()
        @Suppress("DEPRECATION")
        if (darkIcons) {
            window.decorView.systemUiVisibility = window.decorView.systemUiVisibility or
                    View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR
        } else {
            window.decorView.systemUiVisibility = window.decorView.systemUiVisibility and
                    View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR.inv()
        }
    }
}

private object FakeSystemUiController : SystemUiController {
    override fun setStatusBarColor(color: Color, darkIcons: Boolean) = Unit
}
```

`SystemUiController`を定義し、`setStatusBarColor`でステータスバーカラーの実装をします。

アプリ全体の設定になるので、[Ambient](/2021/01/17/jetpack-compose-about-ambient/)として`SysUiController`を定義しておきます。

```
val SysUiController = staticAmbientOf<SystemUiController>
```

MainActivityのsetContentで provideし、

```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val systemUiController = remember { SystemUiController(window) }
            Providers(SysUiController provides systemUiController) {
                JetpackComposePlayGroundTheme {
                    // 省略
                }
            }
        }
    }
}
```
`JetpackComposePlayGroundTheme`内で`SysUiController.current`で SystemUiControllerを取り出し、
[`onCommit`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary?hl=ja#oncommit_2)でカラーが変わったら `sysUiController.setStatusBarColor`を呼び出します。

```
@Composable
fun JetpackComposePlayGroundTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable() () -> Unit
) {
    val colors = if (darkTheme) {
        DarkColorPalette
    } else {
        LightColorPalette
    }
    val sysUiController = SysUiController.current
    onCommit(sysUiController, colors.background) {
        sysUiController.setStatusBarColor(
            color = colors.background// .copy(alpha = 0.95f)
        )
    }
    // 省略
}
```

下の動画のようになります。

<img src="/images/2021/01/jetpack-compose-about-swtich-status-bar/jc_statusbar.gif" >


## 参考

- https://github.com/android/compose-samples/blob/main/Jetsnack/app/src/main/java/com/example/jetsnack/ui/utils/SystemUi.kt#L77