---
title: "gin使用cache的坑，CachePage与CachePageAtomic的区别"
date: 2023-04-08T00:18:53+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

> gin-contrib/cache 官网: https://github.com/gin-contrib/cache

### cache 初体验

最近做项目时候需要使用到 gin 的 cache，后端 http 服务提供 api 时使用缓存是很常见的情景。github 提供的例子里简单的介绍了 cache 组件的使用方法：

```go
func main() {
	r := gin.Default()

	store := persistence.NewInMemoryStore(time.Second)

	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
	})
	// Cached Page
	r.GET("/cache_ping", cache.CachePage(store, time.Minute, func(c *gin.Context) {
		c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
	}))

	// Listen and Server in 0.0.0.0:8080
	r.Run(":8080")
}
```

运行结果：

```bash
$ curl 127.0.0.1:8080/ping
pong 1563888434
$ curl 127.0.0.1:8080/ping
pong 1563888435
$ curl 127.0.0.1:8080/ping
pong 1563888436

$ curl 127.0.0.1:8080/cache_ping
pong 1563888465
$ curl 127.0.0.1:8080/cache_ping
pong 1563888465
$ curl 127.0.0.1:8080/cache_ping
pong 1563888465
```

可以看到执行 cache_ping 请求时，缓存已经生效。

### 实际应用场景分析

实际应用场景下还需要注意更多的地方，将程序稍作调整，加长处理请求的时间。

```go
r.GET("/cache_ping", cache.CachePage(store, time.Minute, func(c *gin.Context) {
  time.Sleep(5 * time.Second)
  c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
}))
```

发起 3 次请求，每次请求间隔 1s 左右。

客户端日志。

```bash
$ curl 127.0.0.1:8080/cache_ping
pong 1563898681
```

```bash
$ curl 127.0.0.1:8080/cache_ping
pong 1563898682
```

```bash
$ curl 127.0.0.1:8080/cache_ping
pong 1563898683
```

服务端日志。

```bash
[GIN-debug] Listening and serving HTTP on :8080
[GIN] 2019/07/24 - 00:18:01 | 200 |  5.010318785s |       127.0.0.1 | GET      /cache_ping
[GIN] 2019/07/24 - 00:18:02 | 200 |    5.0063969s |       127.0.0.1 | GET      /cache_ping
[GIN] 2019/07/24 - 00:18:03 | 200 |  5.007282647s |       127.0.0.1 | GET      /cache_ping
```

可以看到 3 次请求都没有使用到缓存。意味着在某些情况下，服务器可能需要使用更多的资源去并发执行 api 操作，这也许不是我们想要的。比如某些情况下 api 操作本来耗时较长，并发执行带来的不仅仅是资源的消耗增加，也会影响时间效率。

### CachePageAtomic

查阅文档时发现，除了 CachePage 这一种缓存方式，还有 CachePageAtomic 这种方式。顾名思义就是当前模式下的请求处理会按照原子性操作进行缓存，即请求会发生阻塞，但这样可能会带来另一个问题，并发性能大大下降。

在程序中加入以下代码：

```go
r.GET("/cache_ping_atomic", cache.CachePageAtomic(store, time.Minute, func(c *gin.Context) {
  time.Sleep(5 * time.Second)
  c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
}))
```

同样发起 3 次请求，每次请求间隔 1s 左右。

客户端日志。

```bash
$ curl 127.0.0.1:8080/cache_ping
pong 1563899657
```

```bash
$ curl 127.0.0.1:8080/cache_ping
pong 1563899657
```

```bash
$ curl 127.0.0.1:8080/cache_ping
pong 1563899657
```

服务端日志。

```bash
[GIN-debug] Listening and serving HTTP on :8080
[GIN] 2019/07/24 - 00:34:17 | 200 |  5.006461492s |       127.0.0.1 | GET      /cache_ping_atomic
[GIN] 2019/07/24 - 00:34:17 | 200 |  4.033627673s |       127.0.0.1 | GET      /cache_ping_atomic
[GIN] 2019/07/24 - 00:34:17 | 200 |  3.064078263s |       127.0.0.1 | GET      /cache_ping_atomic
```

观察结果和请求时间，除了第一次请求没有使用缓存，后面两次都使用到了。说明缓存已经发挥到了作用。

前面说到可能会带来并发性能的下降，是在特定的场景下会出现，与 cache 的实现有关。

### cache 源码分析

首先看 CachePage。

```go
// CachePage Decorator
func CachePage(store persistence.CacheStore, expire time.Duration, handle gin.HandlerFunc) gin.HandlerFunc {
	return func(c *gin.Context) {
		var cache responseCache
		url := c.Request.URL
		key := CreateKey(url.RequestURI())
		if err := store.Get(key, &cache); err != nil {
			if err != persistence.ErrCacheMiss {
				log.Println(err.Error())
			}
			// replace writer
			writer := newCachedWriter(store, expire, c.Writer, key)
			c.Writer = writer
			handle(c)

			// Drop caches of aborted contexts
			if c.IsAborted() {
				store.Delete(key)
			}
		} else {
			c.Writer.WriteHeader(cache.Status)
			for k, vals := range cache.Header {
				for _, v := range vals {
					c.Writer.Header().Set(k, v)
				}
			}
			c.Writer.Write(cache.Data)
		}
	}
}
```

CreateKey 会先把 url 做 hash 运算，然后判断键值是否存在，存在就读取缓存，否则更新。这部分没什么问题。

接下来是 CachePageAtomic。

```go
// CachePageAtomic Decorator
func CachePageAtomic(store persistence.CacheStore, expire time.Duration, handle gin.HandlerFunc) gin.HandlerFunc {
	var m sync.Mutex
	p := CachePage(store, expire, handle)
	return func(c *gin.Context) {
		m.Lock()
		defer m.Unlock()
		p(c)
	}
}
```

CachePageAtomic 是通过在 CachePage 外层加互斥锁实现的。初看似乎没什么问题，但结合 CachePage 的源码看，在某些场景下还是有问题的，比如以下代码。

```go
r.GET("/cache_hello/:name", cache.CachePage(store, time.Minute, func(c *gin.Context) {
  time.Sleep(5 * time.Second)
  c.String(200, "hello "+c.Param("name")+fmt.Sprint(time.Now().Unix()))
}))
r.GET("/cache_hello_atomic/:name", cache.CachePageAtomic(store, time.Minute, func(c *gin.Context) {
  time.Sleep(5 * time.Second)
  c.String(200, "hello "+c.Param("name")+fmt.Sprint(time.Now().Unix()))
}))
```

我们先看 cache_hello 运行结果。

客户端结果：

```bash
$ curl 127.0.0.1:8080/cache_hello/tom
hello tom1563986675
$ curl 127.0.0.1:8080/cache_hello/jack
hello jack1563986675
$ curl 127.0.0.1:8080/cache_hello/sam
hello sam1563986676
```

服务端结果：

```bash
[GIN-debug] Listening and serving HTTP on :8080
[GIN] 2019/07/25 - 00:44:35 | 200 |  5.007968563s |       127.0.0.1 | GET      /cache_hello/tom
[GIN] 2019/07/25 - 00:44:35 | 200 |  5.007213901s |       127.0.0.1 | GET      /cache_hello/jack
[GIN] 2019/07/25 - 00:44:36 | 200 |  5.009622944s |       127.0.0.1 | GET      /cache_hello/sam
```

这里我们不关心程序是否使用了缓存，只看运行时间，3 个请求都消耗了 5s，并且并发执行。

然后看 cache_hello_atomic 的运行结果。

客户端结果：

```bash
$ curl 127.0.0.1:8080/cache_hello_atomic/tom
hello tom1563986831
$ curl 127.0.0.1:8080/cache_hello_atomic/jack
hello jack1563986836
$ curl 127.0.0.1:8080/cache_hello_atomic/sam
hello sam1563986841
```

服务端结果：

```bash
[GIN] 2019/07/25 - 00:47:11 | 200 |  5.010191486s |       127.0.0.1 | GET      /cache_hello_atomic/tom
[GIN] 2019/07/25 - 00:47:16 | 200 |  9.143533318s |       127.0.0.1 | GET      /cache_hello_atomic/jack
[GIN] 2019/07/25 - 00:47:21 | 200 | 12.983593219s |       127.0.0.1 | GET      /cache_hello_atomic/sam
```

这里已经看出了问题了，由于/cache_hello_atomic/:name 所有的请求都通过同一个 Handler 处理，只使用同一个信号量，他们实际执行过程中是阻塞的。在实际使用场景中，如果存在高并发的接口调用情形而且路由带有参数，需要谨慎考虑使用 CachePageAtomic。

我们最终目的是为了将原子性操作控制到每一条具体的 url，只能通过自行编写中间件来实现了。
