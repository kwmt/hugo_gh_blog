+++
date = "2017-01-05T14:47:25+09:00"
title = "SublimeText3の設定たち"
categories = ["エディタ"]
tags = ["Sublime Text", "シンタックスハイライト"]

+++
## はじめに

macをクリーンインストールして、Sublime Text3を入れ直したはいいが、シンタックスハイライトされてないのがあったので設定する方法をメモ

## 事前準備

パッケージコントロールが入っていなかったらインストールします。

こちらに書いているとおり、下記のimport~をコピーして、`View > Show Console`を開いて、そこに貼り付けてエンターキーを押します。
<a href="https://packagecontrol.io/installation" target="_blank">https://packagecontrol.io/installation</a>

### コマンドパレットを呼び出すには

Command＋Shift＋Pを押す（Tools→Command Pallet…でもOK）

下図のようなもの
<img src="/images/2017/01/command-pallet.png">

## 基本的なインストール方法

例えば、 <a href="https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting" target="_blank">Dockerfile syntax Highlighting</a>を設定したい場合は下記手順のようになります。

1. コマンドパレットを呼び出します。
2. 「Package Control: Install Package」を選択
3. 「Dockerfile Syntax Highlighting」を選択

## パッケージ

### Dockerfileのシンタックスハイライトを設定する

<a href="https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting" target="_blank">Dockerfile syntax Highlighting</a>

<img src="/images/2017/01/dockerfile-sytax.png">

こんな感じになって快適！


### シェルスクリプトのシンタックスハイライト設定

<a href="https://github.com/jfcherng/Sublime-ShellScriptImproved" target="_blank">ShellScriptImproved</a>

### Markdownのシンタックスハイライト設定

<a href="https://github.com/jonschlinkert/sublime-monokai-extended" target="_blank">Monokai Extended</a>
<a href="https://github.com/jonschlinkert/sublime-markdown-extended" target="_blank">Markdown Extended</a>

Monokai Extendedを入れたら、SublimeText→Preferences→Color Scheme→Monokai Extended→変更したいカラーテーマを選ぶ

<img src="/images/2017/01/markdown-color-setting.png">



