---
title: '構造体にフィールドを追加しないで、JSONに動的にフィールドを追加したい #golang'
author: kwmt
layout: post
date: 2014-09-23
url: /index.php/2014/09/23/how-to-add-a-field-to-the-json-output-without-including-it-in-the-struct/
pdrp_attributionLocation:
  - end
categories:
  - golang
tags:
  - encoding/json
  - golang
  - Go言語

---
## はじめに {.section}

JSONを返したい場合、定義済みの構造体に対して`json.Marshal`とかして、JSONにしていたのですが、下記の`Book`構造体の例だと、返せるフィールドは`Title,Author`だけになってしまいます。

<pre class="go">type Book struct {
	Title  string
	Author string
}

response, _ := json.Marshal(book)
fmt.Fprintf(w, string(response))
</pre>

それ以外のフィールドも動的に追加するにはどうしたらか調べたので書いておこうかと思います。

<!--more-->

## 方法 {.section}

基本的には、<a href="http://stackoverflow.com/questions/23045884/can-i-use-marshaljson-to-add-arbitrary-fields-to-a-json-encoding-in-golang" target="_blank">stackoverflow</a>に書いてありますので、参考くださいなんですが、下記のような感じです。

<pre class="go">type Book struct {
	Title  string
	Author string
}

type FakeBook Book

func (b Book) MarshalJSON() ([]byte, error) {
    return json.Marshal(struct {
        FakeBook
        Genre string
    }{
        FakeBook: FakeBook(b),
        Genre:    "Satire",
    })
}
</pre>

このように、`Book`構造体に対して、`Mashaler`インターフェースを実装することで、フィールドを追加することができます。

結果は以下のとおり。

<pre class="go">{
  "Title": "Catch-22",
  "Author": "Joseph Heller",
  "Genre": "Satire"
}
</pre>

## 仕組みとしては(簡単に) {.section}

この場合、`Book`は`Marshaler`インターフェースを実装したことになります。(<a href="http://golang.org/src/pkg/encoding/json/encode.go?h=MarshalJSON#L191" target="_blank">Marshalerインターフェース</a>)

これによって、<a href="http://golang.org/src/pkg/encoding/json/encode.go#L356" target="_blank">Marshalerインターフェースを実装しているなら</a>、`marshalerEncoder`がコールされ、<a href="http://golang.org/src/pkg/encoding/json/encode.go#L414" target="_blank"><code>MarshalJSON</code>がコールされます。</a>

## 配列の各要素に追加したい場合も同様で、 {.section}

つまり次のような場合、

<pre class="go">type Book struct {
	Title   string
	Authors []Author
}

type Author struct {
	Name  string
	Email string
}
</pre>

`Author`構造体に`Mashaler`インターフェースを実装してあげればいいだけです。

<pre class="go">func (b Author) MarshalJSON() ([]byte, error) {
	return json.Marshal(struct {
		FakeAuthor
		Genre string
	}{
		FakeAuthor: FakeAuthor(b),
		Genre:      "Satire",
	})
}
</pre>

Playground: <a href="http://play.golang.org/p/tZEaLTt7Fc" target="_blank">http://play.golang.org/p/tZEaLTt7Fc</a>

結果は次のようになります。

<pre class="go">{
  "Title": "Catch-22",
  "Authors": [
    {
      "Name": "Joseph",
      "Email": "abc@xxx.com",
      "Genre": "Satire"
    },
    {
      "Name": "Heller",
      "Email": "dec@yyy.com",
      "Genre": "Satire"
    }
  ]
}
</pre>

以上です。