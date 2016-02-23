---
title: '#golang PHPerがGo言語を学ぶ理由'
author: kwmt
layout: post
date: 2013-09-13
url: /index.php/2013/09/13/why-phper-should-learn-golang/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - golang
  - Go言語
  - PHP

---
<font color="red">この記事は、mikespookさんの2012/8/10の記事<a href="http://mikespook.com/2012/08/why-you-php-guys-should-learn-golang/" target="_blank">Why you PHP guys should learn Golang</a>の非公式翻訳です。詳細はこの記事をご参照下さい。</p> 

<p>
  This post is the Japanese translated post of mikespook&#8217;s post:<a href="http://mikespook.com/2012/08/why-you-php-guys-should-learn-golang/" target="_blank">Why you PHP guys should learn Golang</a>.<br /> </font>
</p>

<p>
  1年前の記事ですが、興味深かったので、翻訳してみました。<br /> 翻訳にミスなどがありましたら、ご連絡頂けると嬉しいです。
</p>

<h1>
  PHPerがGo言語を学ぶ理由
</h1>

<p>
  <a href="http://kwmt27.net/index.php/2013/09/13/why-phper-should-learn-golang/gopher-talks/" rel="attachment wp-att-825"><img src="http://kwmt27.net/images/2013/09/gopher-talks.png" alt="gopher-talks" width="132" height="100" class="alignleft size-full wp-image-825" /></a>
</p>

<p>
  <a href="http://golang.org/" target="_blank">Go あるいは Go言語</a>はオープンソースで、コミュニティがあり、早くて、一貫性が有り、スケーラブルで、生産的言語で、Googleによって考案されました。<br /> おおくのアプリケーションがGoで構築されています。Rob Pikeは<a href="http://commandcenter.blogspot.com/2012/06/less-is-exponentially-more.html" target="_blank">&#8220;我々は、C++プログラマが代替としてGoに目をむけることを期待している&#8221;</a>と言っているけれども、私は、<strong>PHPerがGo言語を学ぶべきだ</strong>と考えています。<br /> それがなぜかをお話しましょう。
</p>

<p>
  <!--more-->
</p>

<h2 class="section">
  学ぶのが簡単
</h2>

<p>
  PHPは学ぶのが簡単です。Go言語はPHPと同じくらい簡単です。
</p>

<p>
  PHPの文法は、C言語系列(C/C++/Javaなど)の文法に似通っています。<br /> それらの言語を経験しているなら、PHPに最初に出会っても書くのはそれほど難しくありません。
</p>

<p>
  Go言語もC系列のプログラミング言語です。えっと少し異なるところもありますが。たとえば、<a href="http://tour.golang.org/#17" target="_blank">&#8220;for&#8221;</a>キーワードはPHPのものと似ていますが、括弧は必要ありません。また、条件のキーワードである<a href="http://tour.golang.org/#21" target="_blank">&#8220;if&#8221;</a>も括弧は要りません。詳細は<a href="http://golang.org/doc/effective_go.html" target="_blank">Effective Go</a>で読むことができます。
</p>

<p>
  Go言語は25のキーワードと47の演算子、区切り文字、他の特別なトークンだけしかありません。すべてのリテラルを覚える必要はありません。型システムは、非常に優れていて、使うのも簡単です。<br /> No huge OO but a practical struct type with methods on it.<br /> インターフェース設計はGo言語での私の好きな部分の１つです。あなたが<a href="http://tour.golang.org/" target="_blank">A Tour of Go</a>を終えたら、PHPの経験に基づいて、すぐにGo言語で書くことができるでしょう。
</p>

<h2 class="section">
  使うのが簡単
</h2>

<p>
  あなたのPHPスクリプトは SAPIコンポーネント：webサーバーモジュール、PHP-FPM、CLIによって評価されます。<br /> PHP開発に必要な全ては、SAPI環境です。環境の配置は、PHPビギナーが理解するには最も難しい部分です。
</p>

<p>
  すべてのGo言語のコードはコンパイル・リンクされますので、実行環境をデプロイする必要ありません。<br /> PHP環境のデプロイと比べるとGoのデプロイはそれほど複雑ではありません。でもPHP環境のデプロイが本当に複雑だと本当に思っていますか？そんなことはないんですよ。本当に！<br /> <del datetime="2013-09-13T03:28:08+00:00">PHP環境のデプロイと比較すると、それほど複雑ではありません。PHP環境のデプロイはほんと複雑と思いませんか？私はそう思いません。（訳者：誤記かな？）</del>
</p>

<p>
  ビルド環境をデプロイするのは、３つのステップだけです：
</p>

<ul>
  <li>
    Go言語のソースコードをダウンロードします
  </li>
  <li>
    環境変数を設定します。これに関しては素晴らしいブログがあります：<a href="http://mue.tideland.biz/2012/02/go-environment-setup.html?spref=tw" target="_blank" class="broken_link">Go environment setup</a>
  </li>
  <li>
    &#8220;src&#8221;ディレクトリにある&#8221;all.bash&#8221;を実行します。
  </li>
</ul>

<p>
  (訳者：これらをまとめて実行できるスクリプト <a href="https://github.com/kwmt/goinstall" target="_blank">goinstall</a>というツールを書きましたのでよかったら使用してみてください（PR）)
</p>

<p>
  あるいは、１ステップだけなら、<a href="http://code.google.com/p/go/downloads/list" target="_blank">バイナリパッケージ</a>を使ってセットアップします。
</p>

<p>
  それから、&#8221;go&#8221;というツールセットが使用できるようになります。goツールは、PHPのCLIツールを使うのと同じくらい簡単です。<a href="http://areyoufuckingcoding.me/2012/02/14/the-go-tool/" target="_blank" class="broken_link">&#8220;The Go Tool&#8221;</a>に使い方が説明されています。
</p>

<h2 class="section">
  PHPの神話
</h2>

<p>
  プログラミング言語を学んだり使ったりするのが簡単なら、私達はそれを学んだり使ったりするでしょうか？<br /> プログラミング言語の多くは、学ぶのが簡単で使うのも簡単です。私達はそれらの全てを学んだり使ったりするでしょうか？正直言うとそれは、<strong>NO！</strong>です。
</p>

<p>
  しかし、PHPerがなぜGo言語を学ぶべきか？それはクールだから！いや、冗談だけど、本当です。とにかく、まずPHP自身について話しましょう。
</p>

<p>
  PHPは、&#8221;もともと動的なWebページを生成するWeb開発用に設計された汎用のサーバサイドスクリプト言語(<a href="http://en.wikipedia.org/wiki/PHP" target="_blank">Wikipedia</a>)&#8221;です。<br /> PHPの最も重要な特徴は、HTMLに埋め込んでいるということです。PHPコードは“<?php … ?>”タグで下記、HTMLは外側に書きます。また強力な拡張システムも持っています。その拡張はZend APIを使ったCで書かれます。データ処理は、実際は、それらの拡張が処理しています。
  
  <br /> 私見では、PHPはベストなテンプレートプログラミング言語に違いありません。
</p>

<p>
  しかし、PHPを数年経験し、いくつかの複雑なWebアプリケーションに出会ったなら、PHPへの物足りなさを感じるに違いありません。それは、並行機能（スレッド、マルチプロセス）が備わっていないからです。低速なデータの処理するコードは、ページ全体の処理がブロックされるでしょう。PHPの前に強力Webサーバがいつも必要になってきます。バックエンドのプログラムはデータ（メッセージ・キューやキャッシュ、プロキシ・・・）を処理するために書かれます。このシステムはPHPにだけに限りませんし、多くのサービスやシステムコンポーネントもそうです。今日、PHPは少しのロジカルな処理があるテンプレート言語になっています。
</p>

<p>
  PHPerは、いつも<a href="https://www.google.com.hk/search?q=php+multithread&aq=f&sugexp=chrome,mod=16&sourceid=chrome&ie=UTF-8" target="_blank">&#8220;PHP マルチスレッド&#8221;</a> や <a href="https://github.com/laruence/yar" target="_blank">concurrent RPC framework for PHP</a>のようなソリューションを探しています。私はどれがいいか答えることが出来ませんでした。しかし、<br /> バックエンドジョブに対しては、他のプログラミング言語を選ばないといけないと思います。私の場合、C、Java、Pythonで試しました。Cはメモリの確保と解放との戦いで、Javaはjar地獄につまづき、PythonはPythonicになれないけど、型が失われます。パフォーマンスを得るなら、メモリ管理と向き合わなければなりません。GCを使うなら、分散やVMチューニングが必要です。便利さを重視するなら、慎重にならないと自滅する可能性があります。それぞれ長所がありますが、問題もあります。
</p>

<p>
  さて、Go言語に戻りましょう。
</p>

<p>
  Go言語にはGCがあります。メモリ管理を気にする必要がありません（メモリ管理にあまり気を配らなくてよくなります）。ソースコードはネイティブマシン語にコンパイルされます。<br /> それで、&#8221;cp&#8221;や&#8221;mv&#8221;コマンドはGo言語で書かれたアプリケーションを開発するために必要です。<br /> 言ったように、Go言語は静的型付けなコンパイル言語です。変数の型に惑わされることはありません。もちろん、もっとも重要なことは、Go言語へ移行するPHPerが、<a href="http://commandcenter.blogspot.com/2012/06/less-is-exponentially-more.html" target="_blank">多くの表現を捨てなければならないということはなく、パフォーマンスを得て、並列に実行することができるようになりということです</a>。<a href="http://www.drdobbs.com/open-source/why-not-go/240005062" target="_blank">&#8220;Why Not Go?&#8221;</a>はこれについて深く語られています。
</p>

<p>
  私自身の経験の部分をお話します。<a href="http://gearman.org/" target="_blank">Gearman</a> workerというのはバックエンドデータの処理をしてくれます。<br /> PHPは<a href="http://www.php.net/manual/en/book.gearman.php" target="_blank">Gearman API</a>を使ってGeaman Job Serverを通してそのworkerとつながっています。<br /> オリジナルのworkerは<a href="http://pypi.python.org/pypi/gearman/" target="_blank">python</a>で書かれていました。<br /> そのバージョンでは問題がたくさんありました（それらの問題は自分たち自身の問題でしたが、pythonの問題もありました。）<br /> しかし、それは動きます。<br /> それから、私達は、workerをGo言語で書きなおしました。驚きでした。Go言語とGo言語でPHPスクリプトを評価するためのZend API（cgoは別に驚いたこと）を使用している<a href="https://bitbucket.org/mikespook/goemphp" target="_blank">Go言語パッケージ</a>用に<a href="https://bitbucket.org/mikespook/gearman-go" target="_blank">Geamarn API</a>を書きました。<br /> それから、それらを一緒に合わせました。つまり、PHPスクリプトが実行可能なGearman workerとなります。<br /> 我々に環境で約3ヶ月動いており、いい感じです。<br /> 私はまた、PHPスクリプト内でRPCコールを結合するための<a href="https://bitbucket.org/mikespook/combinerpc" target="_blank">RPC-コンビネータ</a>を書きました。<br /> それは次のプロダクトで使う予定です。
</p>

<p>
  全てのことが順調です。ありがとう、Go言語！
</p>

<p>
  いずれの場合でも、ほとんどのPHPerは、バックエンド開発のためには他の言語を学ぶ必要が常にあります。<br /> もしあなたが他の言語を探していたり、すでに経験しているなら、Go言語に挑戦してはみてはいかがですか？<br /> あなたの人生をハッピーに楽にしてくるでしょう。多くの時間を家族や友達と過ごすことができ、好きな物を食べ、好き場所を訪れることが出来るようになります。
</p>

<p>
  これこそが、PHPerがGo言語を学ぶべき理由です。私はそう思います。
</p>

<p>
  <strong>これは英語を書く練習です。もし文法やスペルミスを見つけたら、連絡ください。</strong>
</p>