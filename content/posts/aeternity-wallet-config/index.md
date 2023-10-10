---
title: "Aeternity钱包配置"
date: 2023-04-08T00:12:54+08:00
draft: false
tags: []
card: false
weight: 0
---

#### 钱包安装

https://github.com/aeternity/aeternity/releases

下载钱包文件：

```bash
mkdir aeternity && cd aeternity
wget https://github.com/aeternity/aeternity/releases/download/v2.5.0/aeternity-2.5.0-ubuntu-x86_64.tar.gz
tar xvf aeternity-2.5.0-ubuntu-x86_64.tar.gz

```

安装依赖(https://github.com/aeternity/documentation-hub/blob/master/docs/tutorials/run-node-on-ubuntu-1804.md)：

- [Libsodium](https://download.libsodium.org/doc/) v1.0.16
- [Openssl](https://www.openssl.org/) 1.0.0

```bash
sudo apt-get install build-essential
curl -O https://download.libsodium.org/libsodium/releases/libsodium-1.0.16.tar.gz
tar -xf libsodium-1.0.16.tar.gz && cd libsodium-1.0.16
./configure && make && sudo make install && sudo ldconfig
```

配置节点：

写入以下内容 epoch.yaml

```yaml
sync:
  port: 3015

keys:
  dir: keys
  peer_password: "secret"

http:
  external:
    port: 3013
  internal:
    port: 3113

websocket:
  channel:
    port: 3014

mining:
  beneficiary: "beneficiary_pubkey_to_be_replaced"
  autostart: false

chain:
  persist: true
  db_path: ./my_db

fork_management:
  network_id: ae_mainnet
```

运行节点：

```bash
./bin/aeternity console
```
