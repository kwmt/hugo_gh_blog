+++
categories = ["Tensorflow", "Cloud Vision API"]
date = "2016-04-30T12:10:44+09:00"
tags = ["Tensorflow", "Cloud Vision API"]
title = "Cloud Vision API & Tensorflow勉強会"

+++


佐藤さん
ニューラルネットワーク
Neural Network : a function than can learn


いままでは定石をいれといて、それを使っていたが、
alphaGoは人間が予想がつかない

分類
	身長と体重が分かっていた場合、男か女かを判別したい例
	身長と体重をいい感じの組み合わせ方をコンピューターが計算

ニューロン？

データはなんでもいい。
	例：画像・音声・センサーのデータ・広告のプロファイル
	しかし、ニューラルネットワークに当てはまる・ハマらないがあるかもだが。


学習のデータを与えるだけで、いい感じに分類してくれる

ボトルネック
	計算量：時間かかる
	データ：学習用データ

事例
	一般の事例
		ISETANさん：動線分析
		あまり事例がない
	Googleはたくさん使ってる


人間がタグ付けしてないのに、勝手にタグづけしてくれる

注意
	Google Cloud Vision APIの人識別は、個人の識別はやっていない
	Google フォトはやっている。


Cloud Spech API
	Limited Preview

QA
	VisionAPIにアップロードした画像は、学習に使うことはない
	なぜなら、クレームがくるから。
	調査のために使うことはある。

	右回れ、左回れは、意味を理解しているのか？
		していない。if文で右・左書いてるだけ。
		ただ、言葉の意味を理解するAPIも近々でる予定
