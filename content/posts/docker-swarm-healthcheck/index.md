---
title: "Docker swarm服务HEALTHCHECK的坑"
date: 2023-04-07T23:41:06+08:00
draft: false
categories: [玩具]
tags: []
card: true
weight: 0
---

服务在启动时便执行 health check，如果这是服务没有准备好（端口未监听，未完成初始化工作），会使得 docker 判定服务为 unhealthy 状态，降低服务可用性。此时应设置较大的 Retries 次数。
