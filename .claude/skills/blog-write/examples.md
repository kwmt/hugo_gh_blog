# 本人の文章サンプル

本人（kwmt27 / yasi）がブログ・Zenn・X に実際に書いた文の抜粋。語尾、間の取り方、括弧の使い方をここから真似る。
出典はファイルパスか URL。

## はじめに

動機から入る。目的宣言も構成説明もない。

> MCPサーバーはローカルで実行してMCPクライント(Clineなど)から使うのが一般的？だと思いますが、リモートサーバーで動かせないのかな？と思って調べたら、Cloudflareがそれっぽいの作ってるみたいなので[Build a Remote MCP server](https://developers.cloudflare.com/agents/guides/remote-mcp-server/)を見ながら動かしてみました。
>
> （content/post/2025/04/remote-mcp.md）

> 1. Camera2APIを使って、カメラプレビュー中に（フラッシュではなく）ライトをONにできるのか？
> 2. 周りが暗くなったら自動でライトをONにできるか？
>
> というのが気になって調べたのでメモしておく。
>
> （content/post/2021/07/android-camera2-light-on-by-light-sensor.md）

> 基本的には、書きを参考に自分なりにまとめたものです。
> composeバージョンは 1.0.0-beta01 です。
>
> （content/post/2021/03/jetpack-compose-state.md。「書き」は「下記」の誤変換）

> それがしばらく分からなかった（調べようともしてませんでしたが）のですが、ようやく調べたのでメモしておこうと思います。
>
> （content/post/2020/12/shortcut-for-moving-daialog-focus.md）

> Jetpack Composeのリスト実装はほんとに簡単で直感的に書けちゃうなと思いますし、それがいいところでもあると思うのですが、パフォーマンス観点で気をつけたほうがいいなと思うことがあった
>
> （https://zenn.dev/yasi/articles/jetpack-compose-performance）

> 前提として、上げる前の環境は kotlin 1.9.24、AGP 8.5、compose compier 1.5.14、ksp導入済みあたりでしょうか
>
> （https://zenn.dev/yasi/articles/how-to-update-kotlin2-on-android-project）

## 手順の間の一言

コードブロックの前後は 1 行。体言止めでよい。

> とりあえずサンプルリポジトリを作成します。（この方法でダウンロードするのはうまくいきませんでした。うまくいく方法は少し下に書いてます）

> すべてdefaultで良いなら `-y` をつけると楽。
> 以下は実行したときに出力されたログ。

> 作ったディレクトリに移動

> ローカルサーバー起動

> MCP Inspectorが `http://127.0.0.1:6274` で動いてるので、アクセスしてみると以下のような画面が表示されました。

> 追加するとログイン画面が出ますのでログインしてください。
> 以下のように緑になっていたらOKです

> npm run deployしたいところですが準備が必要みたいです。

> wrangler.jsoncについてはこちら https://developers.cloudflare.com/workers/wrangler/configuration/
>
> （以上 content/post/2025/04/remote-mcp.md）

## 詰まったとき

三点リーダで間を作り、次の行で結果。原因は推測で書き、「〜っぽい」でぼかす。

> URLに最初に起動した
> `http://localhost:8787/sse` を設定して `Connect`をクリック。
>
> するとError...
>
> コード見ると、 `sse`がルーティングされてない...！？

> ChatGPTに聞くと
>
> > Cloudflare のテンプレートが最新でない理由は、たいてい：
> > - キャッシュの影響
> > - ...
>
> なんらかの影響で最新のコードが取得できないことがあるっぽいので、以下の方法でダウンロードしたほうがよさそうです。

> MCP Inspectorに戻り、URLを確認後、Connectを押すと...
>
> 今度はうまくいったようです！
>
> （以上 content/post/2025/04/remote-mcp.md）

> まず目に入ったのが、[`CameraMangaer#setTorchMode`](...)というメソッドで、これを（ドキュメントを読まずに^^;）試してみたら（カメラプレビュー中にコールする）、[`CAMERA_IN_USE`](...)という例外がスローされてしまう。
>
> これはよくよくドキュメントを見ると
>
> > Set the flash unit's torch mode of the camera of the given ID without opening the camera device.
>
> > カメラデバイスを開かずに、指定されたIDのカメラのフラッシュユニットのトーチモードを設定します。
>
> とあり、カメラプレビュー中ということはカメラをopenしている状態なので、openしてない場合に使えるメソッドであることがわかった。
>
> 別の方法を検討する。
>
> （content/post/2021/07/android-camera2-light-on-by-light-sensor.md）

> そもそもprimaryClipフィールドがなかったのか？
> ドキュメントみてもソース見てもない・・・
>
> （content/post/2020/12/android-update-targertsdk-from-28-to-30.md）

## 解説の書き方

概念は短い段落で 1 つずつ。「〜とよびます」「〜する必要があります」。

> Composeでは状態を`State`というもので扱います。
>
> 外から変更可能な `MutableState`と外からは変更できない`State`があります。

> Composable関数は必要に応じて再実行されます。これを`recomposition(再構成)` とよびます。

> Composable関数が再実行される可能性があり、
> rembemrを使わず mutableStateOfだけでStateを宣言した場合、
> 再実行されるときに最初期化され、状態が失われてしまうことになります。
> その状態を保持するために`remember`を付ける必要があります。
>
> （以上 content/post/2021/03/jetpack-compose-state.md。rembemr, 最初期化は typo）

> 読み取りを遅延させるというのは、Booleanを返す関数ラムダを渡すようにするということです
>
> （https://zenn.dev/yasi/articles/jetpack-compose-performance）

## おわりに

感想と次にやること。要約しない。

> これでremote mcp server側でいろいろなMCP実装すれば、クライアントでは設定をいじらなくてよくなるので楽になりそうかなと思います。
> ただ、たくさんMCPサーバーが出てますが、それらをremote mcp server側でつなげる方法が知りたいので、別途調べる予定です。
>
> （content/post/2025/04/remote-mcp.md）

> 自分で実際に読むのは他の記事よむだけよりものすごく理解しました。MCP Server実装の雰囲気がわかったので満足です
>
> （https://zenn.dev/yasi/articles/learn-mcp-server-typescript）

## 振り返り・目標（くだけた方）

> [昨年](/2020/12/31/looking-back-2020/)に引き続き8回目の振り返りを書くぞ！
>
> （content/post/2021/12/looking-back-2021.md）

> 12月のTOEICは、なんか勢いで申し込んじゃった。とくにこれといった目的は決めずでした。

> 結果は8,10月の点数よりなぜか少しだけ上がってました（誤差範囲かもですが）。

> 2023年はほとんど書いてないので、たくさんとは言わないが書いたかなと思います。

> 2025年は大きく変わりそうな予感。しらんけど。
> 今年もお世話になりました。来年もよろしくおねがいします！
>
> （以上 content/post/2024/12/looking-back-2024.md）

> 昨年までは既知で広まってるようなことは書くと雑音になるだけなので書かなくていいやって思ってたのですが（つまり、自分が知ってることはみんなも知ってると同義な気がする）、以下のメリットがあるので今年は書いていこうと思います。
>
> （content/post/2024/01/new-years-resolution.md）

> このあたりはよくやってることですが、動かないのを動くようにできたときは、いつになってもやっぱ嬉しいものがありますね。
>
> （content/post/2021/12/looking-back-2021.md）

## 一覧・メモ（最短）

> 基本的にはこちらを見ればいいが、載ってないのもあるのでわかったやつをめもっとく。
>
> （content/post/2025/03/migrate-chakra-v2-to-v3.md。本文はこの 1 行と diff だけ）

## X の投稿

常体、短い、絵文字なし。

> GWからAndroidの勉強かねて作り始めたQRコードリーダーリリースしました。車輪の再発明ですが、内部的には自分の中では初マルチモジュールだったり、gradleのkts化だったりDarkThemeに対応してみたり、勉強になりました。車輪の再発明も捨てたもんじゃないですね！

> Claude ダウン長いなぁ。無力なので寝るか。

> 今日は金曜だと思ってたが明日が金曜か！

> GitHub ユニコーン出てる

## X のプロフィール（自己紹介の書き方）

> メインはAndroidアプリ開発/直近はNext.js触ってる/Flutterは2018年から2019年にかけて個人でアプリリリースまでガッツリやった/gopherかわいい
