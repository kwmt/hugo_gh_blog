+++
categories = ["Go言語"]
date = "2016-12-11T20:49:46+09:00"
tags = ["gin", "golang"]
title = "ginを軽く読んでみる"

+++

# はじめに
[大阪]Goモク会を開催しました。
https://connpass.com/event/13696/
gomobileを使ってアニメーションさせたりレコメンドシステムを作ったりしてる人がいる中、僕はginのコードリーディングをしました。


# 読んでみる

Readmeに書いてるサンプル

```
package main

import "gopkg.in/gin-gonic/gin.v1"

func main() {
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "pong",
        })
    })
    r.Run() // listen and serve on 0.0.0.0:8080
}
```

## gin.Default()
```
gin.Default()
```
から見ていきます

```
// Default returns an Engine instance with the Logger and Recovery middleware already attached.
func Default() *Engine {
	engine := New()
	engine.Use(Logger(), Recovery())
	return engine
}
```

まずは、`New()` ですね。
コメントにありますが、`Logger` と `Recovery` ミドルウェア付き　`Engine`インスタンスを作成します。

```
// New returns a new blank Engine instance without any middleware attached.
// By default the configuration is:
// - RedirectTrailingSlash:  true
// - RedirectFixedPath:      false
// - HandleMethodNotAllowed: false
// - ForwardedByClientIP:    true
func New() *Engine {
	debugPrintWARNINGNew()
	engine := &Engine{
		RouterGroup: RouterGroup{
			Handlers: nil,
			basePath: "/",
			root:     true,
		},
		RedirectTrailingSlash:  true,
		RedirectFixedPath:      false,
		HandleMethodNotAllowed: false,
		ForwardedByClientIP:    true,
		trees:                  make(methodTrees, 0, 9),
	}
	engine.RouterGroup.engine = engine
	engine.pool.New = func() interface{} {
		return engine.allocateContext()
	}
	return engine
}
```

コメントにありますが、ミドルウェア無しの`Engine` インスタンスを作成します。

`engine.Use(Logger(), Recovery())`を見ていきます

```
// Use attachs a global middleware to the router. ie. the middleware attached though Use() will be
// included in the handlers chain for every single request. Even 404, 405, static files...
// For example, this is the right place for a logger or error management middleware.
func (engine *Engine) Use(middleware ...HandlerFunc) IRoutes {
	engine.RouterGroup.Use(middleware...)
	engine.rebuild404Handlers()
	engine.rebuild405Handlers()
	return engine
}
```
`Use`はグローバルなミドルウェアを提供します。

`Default()`では`Logger()` `Recover()`をアタッチしていました。

```
// Logger instances a Logger middleware that will write the logs to gin.DefaultWriter
// By default gin.DefaultWriter = os.Stdout
func Logger() HandlerFunc {
	return LoggerWithWriter(DefaultWriter)
}
```

`Logger()`はリクエストが来たら、下記のようなフォーマットで標準出力にログ出力します

```
fmt.Fprintf(out, "[GIN] %v |%s %3d %s| %13v | %s |%s  %s %-7s %s\n%s",
	end.Format("2006/01/02 - 15:04:05"),
	statusColor, statusCode, reset,
	latency,
	clientIP,
	methodColor, reset, method,
	path,
	comment,
)
```

`Recovery()`は、パニックが起きたらリカバーして500を返します。

```
// Recovery returns a middleware that recovers from any panics and writes a 500 if there was one.
func Recovery() HandlerFunc {
	return RecoveryWithWriter(DefaultErrorWriter)
}
```


```
func RecoveryWithWriter(out io.Writer) HandlerFunc {
	var logger *log.Logger
	if out != nil {
		logger = log.New(out, "\n\n\x1b[31m", log.LstdFlags)
	}
	return func(c *Context) {
		defer func() {
			if err := recover(); err != nil {
				if logger != nil {
					stack := stack(3)
					httprequest, _ := httputil.DumpRequest(c.Request, false)
					logger.Printf("[Recovery] panic recovered:\n%s\n%s\n%s%s", string(httprequest), err, stack, reset)
				}
				c.AbortWithStatus(500)
			}
		}()
		c.Next()
	}
}
```

`Use()`に戻って

```
func (engine *Engine) Use(middleware ...HandlerFunc) IRoutes {
	engine.RouterGroup.Use(middleware...)
	engine.rebuild404Handlers()
	engine.rebuild405Handlers()
	return engine
}
```


`engine.RouterGroup.Use(middleware...)`としているところを見ます。

`RouterGroup` は `Engine` 構造体に埋め込まれた構造体です。
埋め込まれてるので`engine.Use(middleware...)`こうできるはずですがしてないのは可読性からと思われます。
ただ、ほかのところでは、`engine.Use(middleware...)`としていたので、ここでは違うのはなんでだろ。


```
type RouterGroup struct {
	Handlers HandlersChain
	basePath string
	engine   *Engine
	root     bool
}
```

`RouterGroup`は*Contextを引数にとる関数HandlerFuncのスライス（HandlerChain）を持っていて

```
type HandlerFunc func(*Context)
type HandlersChain []HandlerFunc
```

その`HandlerChain`にappendしているだけです。

```
// Use adds middleware to the group, see example code in github.
func (group *RouterGroup) Use(middleware ...HandlerFunc) IRoutes {
	group.Handlers = append(group.Handlers, middleware...)
	return group.returnObj()
}
```

## r.GET("/ping", handler)

// GET is a shortcut for router.Handle("GET", path, handle)
func (group *RouterGroup) GET(relativePath string, handlers ...HandlerFunc) IRoutes {
	return group.handle("GET", relativePath, handlers)
}

tree構造にハンドラを追加している



## c.JSON(200, obj)
```
// JSON serializes the given struct as JSON into the response body.
// It also sets the Content-Type as "application/json".
func (c *Context) JSON(code int, obj interface{}) {
	c.Status(code)
	if err := render.WriteJSON(c.Writer, obj); err != nil {
		panic(err)
	}
}
```

与えた構造体をJSONに変換します。Content-Typeは"application/json"です。


## r.Run()

```
// Run attaches the router to a http.Server and starts listening and serving HTTP requests.
// It is a shortcut for http.ListenAndServe(addr, router)
// Note: this method will block the calling goroutine indefinitely unless an error happens.
func (engine *Engine) Run(addr ...string) (err error) {
	defer func() { debugPrintError(err) }()

	address := resolveAddress(addr)
	debugPrint("Listening and serving HTTP on %s\n", address)
	err = http.ListenAndServe(address, engine)
	return
}
```
ListenAndServeしていますね。

```
// Conforms to the http.Handler interface.
func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
	c := engine.pool.Get().(*Context)
	c.writermem.reset(w)
	c.Request = req
	c.reset()

	engine.handleHTTPRequest(c)

	engine.pool.Put(c)
}
```

sync.poolについては下記が参考になりました。
http://jxck.hatenablog.com/entry/sync.Pool
http://mattn.kaoriya.net/software/lang/go/20140625223125.htm
