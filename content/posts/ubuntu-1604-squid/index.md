---
title: "在ubuntu16.04下通过squid进行http/https代理"
date: 2023-04-08T00:03:30+08:00
draft: false
categories: [linux]
tags: []
card: false
weight: 0
---

#### 1. 服务端设置

安装服务并编辑配置文件

```bash
apt install squid
vi /etc/squid/squid.conf
```

在 squid.conf 首行添加

```bash
http_access allow all
```

找到`http_port 3128`，将 3128 改为自己想要的端口。

```bash
# Squid normally listens to port 3128
http_port 3128
```

重启 squid 服务

```bash
service squid restart
```

观察日志，看是否正常。

```bash
tail /var/log/squid/access.log
```

#### 2. 客户端设置

在客户端使用填写`hostname:port`为代理服务器。

命令行下可以使用

```bash
export http_proxy=hostname:port
export https_proxy=hostname:port
```

Just enjoy ！！！

参考资料：

1. squid 的简单介绍:[https://www.cnblogs.com/cherishry/p/5706736.html](https://www.cnblogs.com/cherishry/p/5706736.html "https://www.cnblogs.com/cherishry/p/5706736.html")
