+++
date = "2018-06-23T15:30:00+09:00"
title = "Dart2のコンストラクタがわからなかったのでメモ"
draft = false
categories = ["flutter"]
tags = ["flutter", "dart2"]
+++

# はじめに

flutterを触りはじめて、rxあるのかな？と思って調べたら

https://github.com/ReactiveX/rxdart

ありました。どのように使うかサンプルを読んでたんですが、クラスのコンストラクタ部分が分からない・・・

サンプルでいうと下記の部分です。

```dart
class SearchBloc {
  final Sink<String> onTextChanged;
  final Stream<SearchState> state;

  SearchBloc._(this.onTextChanged, this.state);

  factory SearchBloc(GithubApi api) {
    final onTextChanged = new StreamController<String>();
    final state = new Observable<String>(onTextChanged.stream)
        // If the text has not changed, do not perform a new search
        .distinct()
        // Wait for the user to stop typing for 250ms before running a search
        .debounce(const Duration(milliseconds: 250))
        // Call the Github api with the given search term and convert it to a
        // State. If another search term is entered, flatMapLatest will ensure
        // the previous search is discarded so we don't deliver stale results
        // to the View.
        .switchMap(_buildSearch(api))
        // The initial state to deliver to the screen.
        .startWith(new SearchState.initial());

    return new SearchBloc._(onTextChanged, state);
  }

  static Stream<SearchState> Function(String) _buildSearch(GithubApi api) {
    return (String term) {
      return new Observable<SearchResult>.fromFuture(api.search(term))
          .map<SearchState>((SearchResult result) {
            return new SearchState(
              result: result,
              isLoading: false,
            );
          })
          .onErrorReturn(new SearchState.error())
          .startWith(new SearchState.loading());
    };
  }
}
```

https://github.com/ReactiveX/rxdart/blob/master/example/flutter/github_search/lib/github_search_widget.dart#L41-L78

RxJavaとか触ってたのでOvservableの部分はわかるのですが、コンストラクタまわりが分からなかったのでメモです。

# メモ

メンバ変数にx,yを宣言して、javaのようなかんな感じで書くのはわかります。

```dart
class Point {
  num x, y;

  Point(num x, num y) {
    // There's a better way to do this, stay tuned.
    this.x = x;
    this.y = y;
  }
}
```

これと同じことをDartでは下記のように短く書ける。

```dart
class Point {
  num x, y;
  Point(this.x, this.y);
}
```

コンストラクタを宣言しなければ、デフォルトコンストラクタが提供されます。

```dart
class Point {
  num x, y;
}

void main() {
  var p = Point();
  print(p.x);
  // output:
  // null
}
```

しかし、例えばこのようにコンストラクタを定義し、デフォルトコンストラクタは使えず、

```dart
class Point {
  num x, y;
  Point(this.x, this.y);

}
void main() {
  var p = Point();
  print(p.x);
}
```

次のようなエラーがでる。

```
error 2 required argument(s) expected, but 0 found.
```

その場合に、名前付きコンストラクタというのが使える。

次のように、`Point.origin()`を書いてあげると


```dart
class Point {
  num x, y;

  Point(this.x, this.y);

  // Named constructor
  Point.origin() {
    x = 0;
    y = 0;
  }
}
```

次のように書ける。ちなみに`origin()`は何でも良い。

```dart
void main() {
  var p = Point.origin();
  print(p.x);
}
```

また`factory`というキーワードが用意されている。

たとえば、javaの下記のようなものが

```java
class Logger {
    private String name;

    public static Logger factory(String name) {
        return new Logger(name);
    }

    Looger(String name) {
        this.name = name;
    }
}
```

Dartだとこんな感じに書けるという感じらしい。

```dart
class Logger {
  final String name;
  
  Logger._internal(this.name);
  
  factory Logger(String name) {
      return Logger._internal(name);
  }
}
```

これでようやく、RxDartのサンプルの下記(再掲)が読めるようになった。


```dart
class SearchBloc {
  final Sink<String> onTextChanged;
  final Stream<SearchState> state;

  SearchBloc._(this.onTextChanged, this.state);

  factory SearchBloc(GithubApi api) {
    final onTextChanged = new StreamController<String>();
    final state = new Observable<String>(onTextChanged.stream)
        // If the text has not changed, do not perform a new search
        .distinct()
        // Wait for the user to stop typing for 250ms before running a search
        .debounce(const Duration(milliseconds: 250))
        // Call the Github api with the given search term and convert it to a
        // State. If another search term is entered, flatMapLatest will ensure
        // the previous search is discarded so we don't deliver stale results
        // to the View.
        .switchMap(_buildSearch(api))
        // The initial state to deliver to the screen.
        .startWith(new SearchState.initial());

    return new SearchBloc._(onTextChanged, state);
  }

  static Stream<SearchState> Function(String) _buildSearch(GithubApi api) {
    return (String term) {
      return new Observable<SearchResult>.fromFuture(api.search(term))
          .map<SearchState>((SearchResult result) {
            return new SearchState(
              result: result,
              isLoading: false,
            );
          })
          .onErrorReturn(new SearchState.error())
          .startWith(new SearchState.loading());
    };
  }
}
```


`SearchBloc`というクラスに、名前付きコンストラクタ

```dart
SearchBloc._(this.onTextChanged, this.state);
```

がある。factoryキーワードで`SearchBloc(api)`のような使い方が想定されてるのがわかる。

factoryメソッドの中に、Ovservagleなどの設定があるが、最後に名前付きコンストラクタを使って

```dart
return new SearchBloc._(onTextChanged, state);
```
として、SearchBlocインスタンスを返している。

ちなみに、アンダースコアはprivateの意味。
