---
title: "tcpdump 使用实例"
date: 2023-04-08T00:10:58+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

tcpdump 参数

- -A 用户友好的方式进行输出
- -D, --list-interfaces 输出可用设备
- -i `interface`, --interface=`interface` 监听指定设备
- -e 输出 MAC 地址
- --immediate-mode 实时输出，一般不使用
- -n 禁止转换地址（etc ip,port）为名称
- -#, --number 在每个包开头添加编号
- -Q `direction`, --direction=`direction` 指定捕捉方向 in, out, inout
- -q 安静模式，输出少量协议信息
- -s `snaplen`, --snapshot-length=`snaplen` 只截取报文前`snaplen`个字节内容
- -tt 输出 timestamp 格式

以下指令监听`eth0`网卡，目标地址为`192.168.2.10`，源地址处于`192.168`网段，数据包长度范围在`160~200`之间的数据包。数据以安静模式输出，转换为易读的字符串，不对 ip 做反向 dns 操作，只筛选方向为流入的数据，时间以`timestamp`显示。

```bash
tcpdump -i eth0 'tcp and dst host 192.168.2.10 and src net 192.168 and len > 160 and len < 200' -q -A -n -Q in -tt
```

参考资料：

1. Tcpdump 非常实用的抓包 12 实例：[https://www.cnblogs.com/oskb/p/5667874.html](https://www.cnblogs.com/oskb/p/5667874.html "https://www.cnblogs.com/oskb/p/5667874.html")
2. tcpdump 丢包问题解决方法：[https://blog.csdn.net/zhangxinrun/article/details/47057945](https://blog.csdn.net/zhangxinrun/article/details/47057945 "https://blog.csdn.net/zhangxinrun/article/details/47057945")
3. NOTES ABOUT TCPDUMP FILTERS：[https://staff.washington.edu/dittrich/talks/core02/tools/tcpdump-filters.txt](https://staff.washington.edu/dittrich/talks/core02/tools/tcpdump-filters.txt "https://staff.washington.edu/dittrich/talks/core02/tools/tcpdump-filters.txt")
4. PCAP-FILTER：[https://www.tcpdump.org/manpages/pcap-filter.7.html](https://www.tcpdump.org/manpages/pcap-filter.7.html "https://www.tcpdump.org/manpages/pcap-filter.7.html")
