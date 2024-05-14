+++
categories = ["GDS", "Android"]
date = "2016-04-27T12:23:03+09:00"
tags = ["GDS", "Android", "Android N"]
title = "Google Developers Summit Tokyo 2016 | Android に行ってきた"

+++

{{< figure src="http://kwmt27.net/images/2016/04/gds.jpg" >}}

# はじめに

4/25(月)は、<a href="http://googledevjp.blogspot.jp/2016/04/google-developers-summit-2016.html" target="_blank">Google Developers Summit Tokyo 2016</a>に行ってきました。

まとめようかと思ったけど、この方の記事が超まとまってて、書くことが何もないので、こちらのリンクだけはらせて頂きます。まとめありがとうございます！
<a href="http://yuki312.blogspot.jp/2016/04/google-developers-summit-tokyo-2016.html?m=1" target="_blank">http://yuki312.blogspot.jp/2016/04/google-developers-summit-tokyo-2016.html?m=1</a>

だけど、ぜんぶではないが自分もメモとってたので、わかりにくいけどメモをペタッと。


# メモ

7月から9月(第三四半期)に最終版
本日はPreview2のステージ


www.google.com/android/beta より登録
対象:Nexus6以降
http://www.sli.do/
1656

## 江川さん
	複数バージョンの共存
		applicationのファイル名を変更すればよいだけ。

	なるべくtoolバージョンをあげて、targetSDKはあとであげることが可能


	AS2.1
		JDK8のインストール必須
		Jack and Jill

	Jack and Jill
		Androidno新しいツールチェイン
			Jack:コンパイラ
			Jill:リンカ

		特徴
			classにする工程がない
			Android NはJackでコンパイルされている

		問題
			Preview時に実行

		AS2.1 に関するフィードバッグ
			http://b.android.com
			"Android Studio Bug", "Jack bug report"を選択して投稿
				Android Studio BugだとAndroid Studio担当者にアサインされる。featureだとAS担当者にアサインされない可能性がある

	DEXについて
		dexInProcess
			デフォルトでtrue
		ひとつのVM
		メモリ不足対策
			gradle.propertiesにorg.grdle	jv

	シュリンク機能
		不要メソッドを消去したい

	接続中に特化したtパッケージの生成
		例：複数端末接続していた場合、xxhdpiがいらない端末であれば、そのリソースを含まないようにデプロイする（Android Studio経由）

	Instant Run
		下記のような場合、パフォーマンスが落ちる問題が起きる
			multiDexEnabled trueかつminSdkVersion 20の場合
			Dexに含まれるメソッド数が本来の数より増える
			Jackと併用負荷	

	Android Emulator2

	App Indexing支援

## 荒木さん
	Android N
	Preview4まではAPIが変わる可能性がある
	Preview4は24に変わる予定
		過去の端末もプレビューできるようになる予定

	マルチウィンドウ
		Picture in Picture(TV)
			TV限定フローティングウィンドウのようにぽこっと出る
		二分割
		自由形式
			OEMのボードによる

		リサイズ問題
			新しいAPIはない。これまで通り。best practiceに従っている限り綺麗に表示されるハズ
			リサイズ
		ライフサイクル
			これまで通り
			リサイズはこれまでの画面回転と一緒。

			active ON  onResumeが呼ばれている
			active OFF onPauseが呼ばれてとまってる


			動画上で、twitterが下した場合
			onPauseで動画を止めてはだめ。onStopで止める。

		既存のアプリはマルチウィンドウを許可されている状態になっている。
			ただし、縦固定しているとマルチウィンドウにはならない。

		マルチウィンドの判定のAPI追加
		マルチウィンドウ切り替え	
			分割、自由形式は
				アプリから制御できない。右側の□ボタンを長押することだけしかできない


	通知
		直接返信
			通知から直接返信できる
		グループ化
		カスタムビュー
			setContentはdeprecated

	Java8
		Android向けのJava8はJackでないとコンパイルできない
		```
		android{
			jackOptions {
				enabled true
			}
		}
		```
		ラムダ	
			button.setOnclick->say("HI")
			ラムダつかうときはnewされてるんだ
	データセーバー
		バックグラウンドでネット通信できなくなる
		場合分けできるAPIがある
			ConnectivityManager#getRestrictBackgroundStatus()
	クイック設定
		アプリ側からTileを追加できるAPIが追加されてる
		通知領域の部分
	scopeDirectoryAcessサンプルあり

	その他機能	
		DirectBoot
			アラーム7時にセットした。OTAがおこって再起動してアラームがならなかった問題はもうおこらない

	既存機能の変更
		Doze
			軽いDoze
				ネットワークアクセスだけ
				ポケットの中にいれてれもDozeに入る
		Project Svelte(端末の消費電力を押さえる)
			Connectivity_action
				AndroidManifest.xmlに書いても呼ばれない
				動的にProvdierをかけばよばれる
		ユーザーが画面表示のズームを設定出来る
		言語設定
			言語の設定は一つだけだったが、順位付けとして複数選択できる

## 松田さん
	Android NDK
	Adndroid N Developer Preview
		Vulkan追加
		Nは安定リリース
		Vulkan
			Nexus6p/5x
			対応Nexus Player
				Nexus8
	Android NDK 
		r11c
			Clangがデフォルトコンパイラに
			SDKマネージャからダウンロードできるようになった
			開発はAOSP上で
			バグ報告はgithub.com/android-ndk/ndk
		r12 beta
			vulkanに対応
				vlukan.h/vulkan.so追加
	Google Play,開発ツール
		googleplay-game-services
		通信対戦とかが手軽に作れるgameフレームワーク
		google+を使わなくてもGamerIDを使えばgoogleplay game serviceをお使用可能

		Cardboard SDK

		Android Studio
			LLDBをつかた安定デバッグ
			adb高速化
			GraldeでのC++サポート（全部ではないが）
			GPUデバッグ
				Vulkanには対応していないが、ゆくゆくは対応していきたい

	Vulkan
		低レベルな高層なAPIがモバイルAPIでも使えるようになった

	FPL(Fun Proppulsion Labs)
		パイヌーン githubで公開
		ズーシ
	FlatBuffers


## QA
support libは今年の終わりぐらいに、2系などは切る可能がある


# まとめ

Android Nについてなんにもほとんど何にもしらなかったので、行ってよかったです。
なんかあったらb.android.comにバグ報告しよう。