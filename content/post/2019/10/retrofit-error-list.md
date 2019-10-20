+++
title= "Retrofitパースエラー一覧"
date= 2019-10-20T20:25:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Retrofit2"]
keywords = [""]
+++


# はじめに

Retrofitのエラー一覧です。

```kotlin
interface GitHubService {
   @GET("users/{user}/repos") 
   fun listRepos(@Path("user") user: String): Call<List<Repo>>
}

val retrofit = Retrofit.Builder()
    .baseUrl("https://api.github.com/")
    .build()

val gitHubService = retrofit.create(GitHubService::class.java)
```


このように定義したとき、次のように呼び出すときに、内部でリフレクション使ってパースして、ちゃんとHTTPメソッドのアノテーションつけてるかとか、
`ServiceMethod#parseAnnotations` メソッドでやっていました。

そのパース時にいろいろチェックしていたので、そのときのエラーを一覧でまとめて起きたいと思います。

## ServiceMethod#parseAnnotations

### RequestFactory#parseAnnotation

アノテーションチェックしていて、以下の場合IllegalArgumentExceptionをスローします。

####  HTTPメソッドのアノテーションを複数指定してる場合

##### 例

```kotlin
@GET("users/{user}/repos") @POST()
```

##### エラーメッセージ

Only one HTTP method is allowed. Found: GET and POST.

##### 該当コード

```java
if (this.httpMethod != null) {
throw methodError(method, "Only one HTTP method is allowed. Found: %s and %s.",
    this.httpMethod, httpMethod);
}
```

####  クエリパラメータに`{}`がある場合

##### 例
```kotlin
@GET("users/{user}/repos?limit={limit}")
```
##### エラーメッセージ

URL query string "limit={limit}" must not have replace block. For dynamic query parameters use @Query.

##### 該当コード
```java
// Get the relative URL path and existing query string, if present.
int question = value.indexOf('?');
if (question != -1 && question < value.length() - 1) {
    // Ensure the query string does not have any named parameters.
    String queryParams = value.substring(question + 1);
    Matcher queryParamMatcher = PARAM_URL_REGEX.matcher(queryParams);
    if (queryParamMatcher.find()) {
        throw methodError(method, "URL query string \"%s\" must not have replace block. "
            + "For dynamic query parameters use @Query.", queryParams);
    }
}
```

####  HTTPメソッドが全く指定されていない場合

##### 例

```kotlin
interface GitHubService {
    fun listRepos(@Path("user") user: String): Call<List<Repo>>
}
```

##### エラーメッセージ

HTTP method annotation is required (e.g., @GET, @POST, etc.).

##### 該当コード

```java
if (httpMethod == null) {
    throw methodError(method, "HTTP method annotation is required (e.g., @GET, @POST, etc.).");
}
```

#### PATCH,POST,PUT以外で@Multipartを指定した場合

##### 例

```kotlin
@Multipart
@GET("users/{user}/repos)
fun listRepos(@Path("user") user: String): Call<List<Repo>>
```

##### エラーメッセージ

Multipart can only be specified on HTTP methods with request body (e.g., @POST).

##### 該当コード

```java
if (!hasBody) {
  if (isMultipart) {
    throw methodError(method,
        "Multipart can only be specified on HTTP methods with request body (e.g., @POST).");
  }
  // 省略
}
```

#### PATCH,POST,PUT以外で@FormUrlEncodedを指定した場合

##### 例

```kotlin
@FormUrlEncoded
@GET("users/{user}/repos)
fun listRepos(@Path("user") user: String): Call<List<Repo>>
```

##### エラーメッセージ

FormUrlEncoded can only be specified on HTTP methods with request body (e.g., @POST).

##### 該当コード

```java
if (!hasBody) {
  // 省略
  if (isFormEncoded) {
    throw methodError(method, "FormUrlEncoded can only be specified on HTTP methods with "
        + "request body (e.g., @POST).");
  }
}
```

#### @Pathで指定する文字列がPathパラメータに含まれていない場合

##### 例

```kotlin
@GET("users/{user}/repos")
fun listRepos(@Path("user2") user2: String): Call<List<Repo>>
```

##### エラーメッセージ

URL "users/{user}/repos" does not contain "{user2}". (parameter #1)

##### 該当コード

```java
if (!relativeUrlParamNames.contains(name)) {
  throw parameterError(method, p, "URL \"%s\" does not contain \"{%s}\".", relativeUrl, name);
}
```

#### @GETなどでパスを指定しているのに、@Urlをパラメータに指定している場合

##### 例

```kotlin
@GET("users/{user}/repos")
fun listRepos(@Url user: String): Call<List<Repo>>
```

##### エラーメッセージ

@Url cannot be used with @GET URL (parameter #1)

##### 該当コード

```java
if (relativeUrl != null) {
  throw parameterError(method, p, "@Url cannot be used with @%s URL", httpMethod);
}
```

#### @Pathの前に@Queryおいている場合

`@QueryName`, `@QueryMap`も似たようなエラーになる。また`@Path`は`@Url`と一緒に使えない。

##### 例

```kotlin
@GET("users/{user}/repos")
fun listRepos(@Query("limit") limit:Int, @Path("user") user: String): Call<List<Repo>>
```

##### エラーメッセージ

A @Path parameter must not come after a @Query. (parameter #2)

##### 該当コード

```java
if (gotQuery) {
  throw parameterError(method, p, "A @Path parameter must not come after a @Query.");
}
```



#### @GETなどでパスを指定しているのに、@Urlをパラメータに指定している場合

##### 例

```kotlin
@GET("users/{user}/repos")
fun listRepos(@Url user: String): Call<List<Repo>>
```

##### エラーメッセージ

@Url cannot be used with @GET URL (parameter #1)

##### 該当コード

```java
if (relativeUrl != null) {
  throw parameterError(method, p, "@Url cannot be used with @%s URL", httpMethod);
}
```


#### メソッドの戻りがVoidの場合

##### 例

```kotlin
@GET("users/{user}/repos")
fun listRepos(@Path("user") user: String)
```

##### エラーメッセージ

Service methods cannot return void.

##### 該当コード

```java
if (returnType == void.class) {
  throw methodError(method, "Service methods cannot return void.");
}
```


### HttpServiceMethod#parseAnnotations


#### Genericsの型がResponse型(okhttp3.Response型またはRetrofitのResponse型)の場合

##### 例

```kotlin
@GET("users/{user}/repos") 
fun listRepos(@Path("user") user: String): Call<Respnose<Repo>>
```

##### エラーメッセージ

TODO

##### 該当コード

```java
if (responseType == okhttp3.Response.class) {
  throw methodError(method, "'"
      + getRawType(responseType).getName()
      + "' is not a valid response body type. Did you mean ResponseBody?");
}
if (responseType == Response.class) {
  throw methodError(method, "Response must include generic type (e.g., Response<String>)");
}
```

#### ResponseBodyを指定している型に変換するConverterが設定されていない場合

##### 例

```kotlin
@GET("users/{user}/repos") 
fun listRepos(@Path("user") user: String): Call<Respnose<Repo>>
```

##### エラーメッセージ

TODO

##### 該当コード

```java
if (responseType == Response.class) {
  throw methodError(method, "Response must include generic type (e.g., Response<String>)");
}
```