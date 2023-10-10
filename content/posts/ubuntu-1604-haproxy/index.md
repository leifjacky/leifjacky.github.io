---
title: "ubuntu 16.04 安装配置haproxy"
date: 2023-04-08T00:06:21+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

haproxy 是一款负载均衡软件，可以实现 TCP 和 HTTP 模式下的流量代理转发。

1.在官网下载源码

```bash
wget http://www.haproxy.org/download/1.8/src/haproxy-1.8.1.tar.gz
```

2.解压

```bash
tar xvf haproxy-1.8.1.tar.gz
```

3.查看 README 文件，haproxy 支持以下系统的编译，在这里需要选择合适的版本参数。在这里我使用 linux2628

```bash
To build haproxy, you have to choose your target OS amongst the following ones
and assign it to the TARGET variable :

  - linux22     for Linux 2.2
  - linux24     for Linux 2.4 and above (default)
  - linux24e    for Linux 2.4 with support for a working epoll (> 0.21)
  - linux26     for Linux 2.6 and above
  - linux2628   for Linux 2.6.28, 3.x, and above (enables splice and tproxy)
  - solaris     for Solaris 8 or 10 (others untested)
  - freebsd     for FreeBSD 5 to 10 (others untested)
  - netbsd      for NetBSD
  - osx         for Mac OS/X
  - openbsd     for OpenBSD 5.7 and above
  - aix51       for AIX 5.1
  - aix52       for AIX 5.2
  - cygwin      for Cygwin
  - haiku       for Haiku
  - generic     for any other OS or version.
  - custom      to manually adjust every setting
```

4.编译并安装，这里软件安装到**/usr/local/haproxy**目录下

```bash
apt install gcc make -y
make TARGET=linux2628 ARCH=x86_64 PREFIX=/usr/local/haproxy
make install PREFIX=/usr/local/haproxy
```

5.写入配置文件**/usr/local/haproxy/haproxy.cfg**

```bash
global
daemon
nbproc 8
pidfile /var/run/haproxy.id
maxconn 100000

defaults
mode tcp
retries 2
option redispatch
option abortonclose
maxconn 81920
timeout connect 5000ms
timeout client 300000ms
timeout server 300000ms
log 127.0.0.1 local0 err

listen admin_stats
bind 0.0.0.0:1080
mode http
option httplog
maxconn 10
stats refresh 30s
stats uri /stats
stats realm XingCloud\ Haproxy
stats auth user:pass
stats hide-version

listen proxy80
bind 0.0.0.0:80
server backend1 192.168.2.30:80 source 192.168.2.20
server backend2 192.168.2.31:80 source 192.168.2.21
balance leastconn
```

6.启动（热重启）haproxy

```bash
./haproxy/sbin/haproxy -f ./haproxy/haproxy.cfg -p /var/run/haproxy.pid -sf $(cat /var/run/haproxy.pid)
```

7.访问 **http://hostname:1080/stats** 获取连接数据

参考资料：

1. 官网：[http://www.haproxy.org](http://www.haproxy.org "http://www.haproxy.org")
2. 高负载均衡学习 haproxy 之安装与配置：[https://www.cnblogs.com/ilanni/p/4750081.html](https://www.cnblogs.com/ilanni/p/4750081.html "https://www.cnblogs.com/ilanni/p/4750081.html")
3. Ubuntu 16.04 下安装 HAProxy 1.5.11 做 tcp 负载均衡：[https://www.linuxidc.com/Linux/2016-06/132689.htm](https://www.linuxidc.com/Linux/2016-06/132689.htm "https://www.linuxidc.com/Linux/2016-06/132689.htm")
4. Haproxy, get original IP address for layer 4 (TCP)? ：[https://what.thedailywtf.com/topic/13808/haproxy-get-original-ip-address-for-layer-4-tcp/4](https://what.thedailywtf.com/topic/13808/haproxy-get-original-ip-address-for-layer-4-tcp/4 "https://what.thedailywtf.com/topic/13808/haproxy-get-original-ip-address-for-layer-4-tcp/4")
5. Haproxy 和 Nginx 负载均衡测试效果对比记录：[https://www.cnblogs.com/kevingrace/p/6249725.html](https://www.cnblogs.com/kevingrace/p/6249725.html "https://www.cnblogs.com/kevingrace/p/6249725.html")
6. haproxy 重启关闭停止：[http://www.bubuko.com/infodetail-1020779.html](http://www.bubuko.com/infodetail-1020779.html "http://www.bubuko.com/infodetail-1020779.html")
7. 关于 haproxy 负载均衡的算法整理：[https://my.oschina.net/BambooLi/blog/506397](https://my.oschina.net/BambooLi/blog/506397 "https://my.oschina.net/BambooLi/blog/506397")
8. 我们是如何优化 HAProxy 以让其支持 2,000,000 个并发 SSL 连接的？：[http://www.infoq.com/cn/articles/fine-tuned-haproxy-to-achieve-concurrent-ssl-connections](http://www.infoq.com/cn/articles/fine-tuned-haproxy-to-achieve-concurrent-ssl-connections "http://www.infoq.com/cn/articles/fine-tuned-haproxy-to-achieve-concurrent-ssl-connections")
9. Haproxy 在 socket/tcp 下传输数据的使用实例：[http://blog.csdn.net/audioo1/article/details/53189032](http://blog.csdn.net/audioo1/article/details/53189032 "http://blog.csdn.net/audioo1/article/details/53189032")
10. 一些通用性的 haproxy 调优 tips：[https://www.cnblogs.com/276815076/p/6992557.html](https://www.cnblogs.com/276815076/p/6992557.html "https://www.cnblogs.com/276815076/p/6992557.html")
11. 配置 haproxy 支持使用多个 lan 内网 ip 做负载均衡以突破 haproxy 机只支持 64k 连接(突破单 ip 65535 端口限制)：[https://www.iamle.com/archives/1865.html](https://www.iamle.com/archives/1865.html "https://www.iamle.com/archives/1865.html")
