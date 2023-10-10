---
title: "ubuntu 16.04 搭建shadowsocks服务器"
date: 2023-04-08T00:04:53+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

Shadowsocks 优势在于能够同时为 Win、Mac、iOS、Android 等设备同时提供服务，使用 sock5 进行代理，相较于 VPN 稳定高效。

安装 ss 环境，需要先安装`pip`：

```bash
apt install -y python-gevent python-pip
pip install --upgrade pip
pip install setuptools
pip install shadowsocks
```

写入配置文件`/etc/shadowsocks.json`：

```bash
echo '{
 "server":"0.0.0.0",
 "local_address": "127.0.0.1",
 "local_port":1080,
 "port_password": { "18080": "password" },
 "timeout":300,
 "method":"rc4-md5",
 "fast_open": false
}' > /etc/shadowsocks.json
```

启动服务

```bash
ssserver -c /etc/shadowsocks.json -d start
```

配置开机自动启动：

```bash
echo '#!/bin/sh -e
ssserver -c /etc/shadowsocks.json -d start
exit 0' > /etc/rc.local
```

参考资料：

1. 官网：[https://shadowsocks.org/](https://shadowsocks.org/ "https://shadowsocks.org/")
2. github 主页：[https://github.com/shadowsocks](https://github.com/shadowsocks "https://github.com/shadowsocks")
