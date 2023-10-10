---
title: "一种golang死锁情形"
date: 2023-04-08T00:16:03+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

最近做项目时候碰到了死锁的情况，还原成简单的代码如下：

```go
package main

import (
    "fmt"
    "sync"
)

var m sync.RWMutex

func main() {
    lock()
    defer unlock()

    doSthing()
}

func doSthing() {
    lock()
    defer unlock()

    fmt.Println("dosting start")
    // dosth
    fmt.Println("dosting end")
}

func lock() {
    fmt.Println("lock start")
    m.Lock()
    fmt.Println("lock end")
}

func unlock() {
    fmt.Println("unlock start")
    m.Unlock()
    fmt.Println("unlock end")
}
```

运行结果：

```bash
$ go run main.go
lock start
lock end
lock start
fatal error: all goroutines are asleep - deadlock!
```

想当然认为在正常情形下出现死锁程序会自动退出并提示，但实际使用中有时会很难发现这种错误。先把以上代码的 main 函数稍做修改，模拟使用场景。

```go
func main() {
    lock()
    defer unlock()

    go doSthing()

    for {
        // accept for tcp conn
    }
}
```

运行结果：

```bash
$ go run main.go
lock start
lock end
lock start
```

后面就一直卡住，发生死锁，这种情况下并不会报错，随着程序执行更多的 goroutine 申请锁资源，通过观察 htop 负载会一直增加，并且影响到其他业务的运行。
我在实际业务中需要对 map 进行读写所以用到了读写互斥锁，写的时候还使用到了带缓存的 channel。简单还原成如下：

```go
package main

import (
    "fmt"
    "math/rand"
    "sync"
)

const (
    mCnt     = 10
    chanSize = 10
)

var m map[int]int
var mu *sync.RWMutex

func init() {
    m = make(map[int]int)
    mu = &sync.RWMutex{}

    for i := 0; i < mCnt; i++ {
        m[i] = i
    }
}

func main() {
    go doSth()

    for {
    }
}

func doSth() {
    mu.RLock()
    defer mu.RUnlock()

    n := 0
    ch := make(chan int, chanSize)
    for k := range m {
        n++
        ch <- n
        go func(k int) {
            defer func() {
                <-ch
            }()
            if rand.Int()%100 == 0 {
                addM(k)
            }
        }(k)
    }

    fmt.Printf("add %d values\n", n)
}

func addM(key int) {
    mu.Lock()
    defer mu.Unlock()

    m[key]++
}
```

在 map 容量(mCnt)小的时候 channel 通常不会满，不会发生死锁。在检查代码与测试时都没有发现这个问题，但在实际环境，map 数据量大的情况下可能会出现死锁的情形。这种情况也是相对比较隐蔽的。
考虑到我实际业务中不需要保证 addM 实时运行，所以直接使用`go addM()`来避免死锁，当然还有其他方法可以实现，这里就不讨论了。
