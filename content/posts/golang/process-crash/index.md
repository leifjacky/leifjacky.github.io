---
title: "Golang进程崩溃日志"
date: 2023-04-08T00:23:43+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

export GOTRACEBACK=crash

或者你把 ./daemon env GOTRACEBACK=crash ./robot_server --log_dir=${log_dir} --conf=robotserver.conf & 改写成：env GOTRACEBACK=crash ./daemon ./robot_server --log_dir=${log_dir} --conf=robotserver.conf & 也是可以的

开发服务器程序时如果未经过充分测试， 服务稳定运行一段时间后会突然崩溃退出。一般是因为程序中出现了某个未捕获的异常。
这类问题属于偶现的，且需要服务器运行一段时间之后才会出现，难以定位有问题的代码段。

这中情况下应该将服务进程的 stderr 重定向至某个文件，这样当进程因未捕获的异常导致崩溃时，go 运行时会将异常发生时各个协程的调用栈信息记录下来，便于定位问题。

```go
logFile, err := os.OpenFile("./log/fatal.log", os.O_CREATE|os.O_APPEND|os.O_RDWR, 0660)
if err != nil {
    log.Println("服务启动出错",  "打开异常日志文件失败" , err)
    return
}
```

// 将进程标准出错重定向至文件，进程崩溃时运行时将向该文件记录协程调用栈信息
syscall.Dup2(int(logFile.Fd()), int(os.Stderr.Fd()))
以上代码利用了 linux 系统中的 dup 系统调用，用以将某一文件描述符指向一个新的文件。在 windows 系统及 mac 系统下暂时不可用。
