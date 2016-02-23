---
title: '#golang 複数のテンプレートファイルを簡単にParseするには'
author: kwmt
layout: post
date: 2013-03-18
url: /index.php/2013/03/18/golang-複数のテンプレートファイルを簡単にparseするに/
pdrp_attributionLocation:
  - end
fb_author_post_id:
  - 502131416516837
categories:
  - golang
tags:
  - golang
  - Go言語
  - template

---
<img src="http://kwmt27.net/images/2013/03/golden_globs.jpg" width="1024" height="681" srcset="http://kwmt27.net/images/2013/03/golden_globs-451x300.jpg 451w, http://kwmt27.net/images/2013/03/golden_globs-300x199.jpg 300w, http://kwmt27.net/images/2013/03/golden_globs-624x414.jpg 624w, http://kwmt27.net/images/2013/03/golden_globs.jpg 1024w" sizes="(max-width: 1024px) 100vw, 1024px" />

[「golangでhtmlファイルの一部を別ファイルに分けるには」][1]で、テンプレートファイルを分割するエントリを書きました。分割したファイルをパースする関数は、template.ParseFiles(filenames &#8230;string)です。分割したファイルが２，３個ならこのParserFiles関数でよいと思いますが、もっと多くなったらファイルパスをゴリゴリ書かないといけません。 

ゴリゴリ書かなくてもいい方法があります。それは、template.ParseGlob関数を使用することです。ファイルをパターン化することができます。
  
まずは下記のコードを見て下さい。(golang.orgの[Example][2]の訳です) 


{{< gist 5186898 >}}

上記のコードは、$TMPDIR/template+suffixというディレクトリに、T0.tmpl, T1.tmpl, T2.tmpl という３つのファイルがあった時（このコードではテスト用に作成していますが）、パターン(pattern)を 

<pre class="go">$TMPDIR/template+suffix/*.tmpl
</pre>

として、template.ParseGlobに渡しています。これで、 

<pre class="go">template.ParseFiles("dir/T0.tmpl",""dir/T1.tmpl", "dir/T2.tmpl")
※dir = "$TMPDIR/template+suffix"とおいています。
</pre>

と書いたことと同じになります。 

## 追記(2013/3/19)

上記の例の場合は、T0.tmplが親となって、T1、T2を呼び出しているため、上記patternで最初に見つかるT0.tmplが生成されるテンプレートの名前となるので、特に問題ないのですが、たとえば、T1.tmplが親となって、T0とT2を呼び出すようにした場合は、なにも表示されません。（ためしに、上記コードの3行目と5行目のcontens要素を入れかえて実行してみて下さい) 

これを回避するには、`template.New("T1.tmpl")`でテンプレートを生成して、その生成されたテンプレートに対して、`ParseGlob(patter)`とします。具体的には次のようにします。 

{{< gist ea763b80b01d39db67f5 >}}

これは、たとえばテンプレートファイルとして、main.tmpl、header.tmpl、footer.tmplというファイルがあるとき、パターンを &#8220;*.tmpl&#8221;としてParseGlobすると、生成されたテンプレート名はfooter.tmplになり、main.tmplをテンプレートとしたいときに有効です。（このサンプルは[こちら][3]） 

## 参考

[ここ][4]に上記Exampleの全体のソースをおいてます。(cloneすればすぐに確かめることができるようにしています)

 [1]: http://kwmt27.net/index.php/2012/06/05/golang%e3%81%a7html%e3%83%95%e3%82%a1%e3%82%a4%e3%83%ab%e3%81%ae%e4%b8%80%e9%83%a8%e3%82%92%e5%88%a5%e3%83%95%e3%82%a1%e3%82%a4%e3%83%ab%e3%81%ab%e5%88%86%e3%81%91%e3%82%8b%e3%81%ab%e3%81%af/ "golangでhtmlファイルの一部を別ファイルに分けるには"
 [2]: http://golang.org/pkg/text/template/#example_Template_glob
 [3]: https://github.com/golang-samples/template/tree/master/parseglob
 [4]: https://github.com/kwmt/golangwiki/blob/master/src/pkg/text/template/exampletemplate_glob.go