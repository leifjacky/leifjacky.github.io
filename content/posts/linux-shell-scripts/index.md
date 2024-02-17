---
title: "Some Linux Shell Scripts"
date: 2023-04-08T00:13:45+08:00
draft: false
categories: [linux]
tags: []
card: false
weight: 0
---

linux 添加 swap 分区

```bash
fallocate -l 8G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

ubuntu16.04 安装 postgreSQL10

```bash
echo "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main" >> /etc/apt/sources.list.d/dgpg.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt install postgresql-10
```

参数调整：

```bash
max_connections = 1024
shared_buffers = 4GB # 内存四分之一
work_mem = 16MB
maintenance_work_mem = 256MB
effective_cache_size = 8GB  # 内存一半

```

ubuntu16.04 安装 redis5.0.5

```bash
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
tar xvf redis-5.0.5.tar.gz
cd redis-5.0.5
make && make install
ln -fs /usr/local/bin/redis-server /bin/
ln -fs /usr/local/bin/redis-cli /bin/
```

docker compose install

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

jdk11 install

### 下载 JDK

> https://www.oracle.com/java/technologies/javase-jdk11-downloads.html

### 安装

```bash
tar -zxvf jdk-11.0.10_linux-x64_bin.tar.gz
cd /usr/lib
mkdir jd
mv ~/jdk/jdk-11.0.10 jdk/
```

### 设置环境变量

```bash
vi ~/.bashrc
```

```bash

#set java env
export JAVA_HOME=/usr/lib/jdk/jdk-11.0.10
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

### i3lock

```bash
#!/usr/bin/env bash

i3lock -n -i ~/Pictures/wall/857860.jpg -F \
    --inside-color=373445ff --ring-color=ffffffff --line-uses-inside \
    --keyhl-color=d23c3dff --bshl-color=d23c3dff --separator-color=00000000 \
    --insidever-color=fecf4dff --insidewrong-color=d23c3dff \
    --ringver-color=ffffffff --ringwrong-color=ffffffff --ind-pos="x+86:y+1003" \
    --radius=15 --verif-text="" --wrong-text=""
```

### xflux

```bash
#!/bin/bash

xflux -l 22.542883 -g 114.062996 -k 3000
```
