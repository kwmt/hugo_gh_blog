+++
date = "2018-08-16T19:35:00+09:00"
title = "Flutterあるある"
draft = false
categories = ["Flutter"]
tags = ["Flutter","LT"]

+++

## はじめに

2018/08/12(日)にGDG神戸主催のFlutter勉強会が開催されました。勉強会全体に関しては <a href="https://kobegdg.blogspot.com/2018/08/flutter.html" target="_blank">こちらのブログ</a>を見ていただいて、ここでは僕が「Flutterあるある」というタイトルでLT(LT資料は<a href="https://speakerdeck.com/yasutaka/flutteraruaru" target="_blank">こちら</a>)したので、それについて書きたいと思います。

基本的に原稿をもとに加筆・修正しています。

## Flutterあるある

まず自己紹介でチャットアプリを作成中で下記の動画で紹介しました。


<iframe width="560" height="315" src="https://www.youtube.com/embed/gNALr-zmpFY" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

このアプリを作成中にいろいろあったので、これあるあるだなぁと思ったことを３つほど共有したいと思います。

このチャットアプリはまだ公開できる状態じゃないのですが、インストールしてためしていただくこともできますので、ご興味があれば声かけてください。

### 公式ドキュメントに書いてるプラグインが動かないことがある

本題です。まず、あるあるの１つ目は、公式ドキュメントに書いてるプラグインが動かないことがある。です。


公式ドキュメントの書いてある以上、動いてほしいよねという話なのですが、
たとえば、画像キャッシュしたいと思って、「flutter cache image」と検索すると、
cached_network_imageプラグインというのが公式ドキュメントに載っているのが見つかります。


<img src="/images/2018/08/flutter-workshop-gdgkobe/working_with_cached_images.png" />


これを使おうとして、flutter packages getすると、「cached_network_imageはuuidプラグインに依存していてバージョン0.5.3が必要です」というエラーが出てしまいました。これはuuidプラグインのバージョン1.0.0以上を使っている場合にエラーになります。


<img src="/images/2018/08/flutter-workshop-gdgkobe/pacakges_get_fail.png" />

issueを見てみると、29日前にissueが上がってたり、


<img src="/images/2018/08/flutter-workshop-gdgkobe/cached_network_image_issue.png" />

プルリクエストまで出ているのに、とくにメンテされる気配がなさそうでした。。。

<img src="/images/2018/08/flutter-workshop-gdgkobe/cached_network_image_pr.png" />


そんなときは、pubspec.yamlは直接githubリポジトリを参照することができるので、動かないgithubリポジトリをforkして修正すれば、本家のプラグインが動かなくても大丈夫です！

<img src="/images/2018/08/flutter-workshop-gdgkobe/pubspec-yaml.png" />

ここでは、pubspec.yamlはgitリポジトリを参照できますよ、ということを言いたかっただけです。

### Google製プラグインが動かないことがある

2つ目はGoogle製プラグインが動かないことがありました。
firebase_messagingプラグインを使って Androidにプッシュ通知を送ったところ、送った時点でクラッシュするという致命的な現象に遭遇しました。

<img src="/images/2018/08/flutter-workshop-gdgkobe/firebase_messaging_issue.png" />

これはissueがあって、プルリクエストがない状況でして、たまたま原因がわかったのでプルリクエストを送りました。

<img src="/images/2018/08/flutter-workshop-gdgkobe/firebase_messaging_pr.png" />

プルリクエストを送ったところ、2週間弱ぐらい反応なくて、他の人が突っついたら動き始めてくれました。

<img src="/images/2018/08/flutter-workshop-gdgkobe/firebase_messaging_pr2.png" />

このプルリクエスト自体は他のプラグインの絡みでマージされなかったのですが、修正してもらって、現在のバージョンではプッシュ通知が届いたときにクラッシュしなくなっています。

ここでは、プルリクエストは気軽にできるよ！ってことを伝えたかったです。


### AndroidとiOSで動きが異なる

最後は、AndroidとiOSで動きが異なるというあるあるです。これが一番あるあるかなぁなと思いますが、僕が遭遇したのはQRコードリーダーのプラグインです。

QRコードを読み取るプラグインはいくつかあって、たとえば、qr_readerというプラグインがあります。

<img src="/images/2018/08/flutter-workshop-gdgkobe/qr_reader_plugin.png" />

Androidで動かすとカメラ画面が全画面になっていますが、

<img src="/images/2018/08/flutter-workshop-gdgkobe/qr_reader_android.png" />

iOSでは全画面ではなく、中央部分のみカメラ画面があるという感じになっていました。

<img src="/images/2018/08/flutter-workshop-gdgkobe/qr_reader_ios.png" />


ほかにはbarcode_scanというプラグインがあります。

<img src="/images/2018/08/flutter-workshop-gdgkobe/barcode_scan_plugin.png" />

これもちょっと異なっていて、iOSは全画面表示でとくに枠とかないのですが、

<img src="/images/2018/08/flutter-workshop-gdgkobe/barcode_scan_ios.png" />

Androidは全画面表示で枠があるデザインになっていました。

<img src="/images/2018/08/flutter-workshop-gdgkobe/barcode_scan_android.png" />

今回のチャットアプリに関してはこれで妥協しているのですが、必要ならプルリクエストするか、自分でプラグインを作る必要がありそうかなぁと思います。

### まとめ

動かなかったり、AndroidとiOSで期待する動きと異なっている場合がありますが、githubリポジトリを参照できるのでなんとかなりますし、たとえ一部でも修正できればプルリクエストを送ってみてください。なんとかなります。
ということで、まとめとしては「なんとかなる」ですｗ

まだまだ微妙なところがあるflutterですが、ワンソースで両OS対応できるのは魅力的なので今後も追っかけていきたいと思います。


## 付録

と、まぁLTはここまでだったんですが、他にも2つほど考えていたけどLT時間内に入り切らなかったので泣く泣く除いたあるあるをここでご紹介。


### Dart Packagesサイトとgithubの情報が違うことがある

プラグインの情報は、[pub.dartlang.org](https://pub.dartlang.org/)(ここでは`Dart Packagesサイト`と呼ぶことにします)で探すか、ググったらだいたいここに行き着くと思います。


それで、Dart PackagesサイトのREADMEをまずは見ると思いますが、Dart Packagesサイト側のREADMEを読んでもうまく動かず、GithubのREADMEを読んだらうまくいったということがありました。

<img src="/images/2018/08/flutter-workshop-gdgkobe/pub_readme.png" />

具体的には、いまは修正されてるのですが、image_pickerというプラグインで、iOSではNSMicrophoneUsageDescriptionの設定が必要なのに、Dart PackagesサイトのREADMEには書いてなく、GithubのREADMEには書かれていたという事がありました。

これはTwitterで教えて頂いたのですが、Dart PackagesサイトはpublishしたときにしかREADME更新できないようで、publishするにはバージョンを上げる必要があるみたいで、README更新するだけのためにバージョンを上げるのも、、、感があるのかもということでした。


<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">pub.dartlangの方はpublishしたときにしかREADME更新できないんですよね...<br>README更新するためだけにバージョン上げるのも感があるのかもです<br>(同期はして欲しい)</p>&mdash; 月見ふそやんUnderground (@azihsoyn) <a href="https://twitter.com/azihsoyn/status/1012549577440833536?ref_src=twsrc%5Etfw">2018年6月29日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


ですので、Dart Packagesサイトを見るのもいいですが、できるだけGithubの方を見ておくと幸せになれるかもしれませんね。

### masterチャンネルで動かないことがある

もう1つは、masterチャンネルで動かないのことがある、ですが、flutterには <a href="https://github.com/flutter/flutter/wiki/Flutter-build-release-channels" targe="_blank">channel</a>というgitのブランチのようなものがあって(というか実態はgitのブランチだと思いますが)、`flutter channel`というコマンドで自分がいまどのチャンネルにいるか確認できます。下記の例だと`master`チャンネルにいます。

```
$ flutter channel
Flutter channels:
  beta
  dev
* master
```

この状態で開発していると、ビルドエラーにビルドエラーになることがありました。具体的には、ランチャーアイコンを生成するflutter_launcher_iconsプラグインを使って、アイコンを生成するコマンドを叩くと、下記のようにエラーが出ました。

```
% flutter pub pub run flutter_launcher_icons:main
Failed to precompile flutter_launcher_icons:main:
file:///usr/local/flutter/.pub-cache/hosted/pub.dartlang.org/dart_config-0.5.0/lib/loaders/config_loader_filesystem.dart:13:36: Error: The parameter 'path' of the method 'ConfigFilesystemLoader::loadConfig' has type dart.core::String, which does not match the corresponding type in the overridden method (dynamic).
Change to a supertype of dynamic (or, for a covariant parameter, a subtype).
  Future<String> loadConfig(String path) {
                                   ^
file:///usr/local/flutter/.pub-cache/hosted/pub.dartlang.org/dart_config-0.5.0/lib/parsers/config_parser_yaml.dart:10:15: Error: The return type of the method 'YamlConfigParser::parse' is dart.async::Future<dart.core::Map<dynamic, dynamic>>, which does not match the return type of the overridden method (dart.async::Future<dart.core::Map<dart.core::String, dart.core::Object>>).
Change to a subtype of dart.async::Future<dart.core::Map<dart.core::String, dart.core::Object>>.
  Future<Map> parse(String configText) {
              ^
pub finished with exit code 1
```

調べていたら、issueにはdev チャンネルで修正されているので切り替えてとのことなので、devチャンネルに切り替えました。

<img src="/images/2018/08/flutter-workshop-gdgkobe/flutter_launcher_icons_issue.png" />

チャンネルを切り替えるには、`flutter channel <チャンネル名>`とします。

```
$ flutter channel dev
```

切り替えてflutter packages getするとアップデートされ、今回の場合は約20分かかりましたが、devチャンネルで動くことを確認しました。

```
% flutter pub pub run flutter_launcher_icons:main
Android minSdkVersion = 16
Creating icons Android
Overwriting default Android launcher icon with new icon
Creating adaptive icons Android
Overwriting default iOS launcher icon with new icon
Copying colors.xml template to project
Found existing background color value
```

ここではチャンネルというものがあるよという紹介です。

## おわりに

以上、Flutterあるあるでした。

Flutter勉強会の懇親会で、画面遷移時のデータの渡し方ってどうしてますか？とか、Edit Textのような動的に変わる値を取得したいときとかってどうしてますか？とか聞かれましたが、また別途。
