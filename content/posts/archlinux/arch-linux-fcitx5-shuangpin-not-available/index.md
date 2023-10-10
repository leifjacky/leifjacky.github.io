---
title: "Arch Linux下Fcitx5中文输入法失效，显示Not available"
date: "2023-10-10T19:21:26+08:00"
draft: false
tags: [linux, archlinux, fcitx]
---

运行时先是发现boost版本更新了，导致无法启动。直接更新fcitx5即可。

更新fcitx5仍然报错：

```bash
Failed to load library for addon pinyin on /usr/lib/fcitx5/libpinyin.so. Error: /usr/lib/fcitx5/libpinyin.so: undefined symbol: \_ZNK6libime13PinyinContext21candidatesToCursorSetB5cxx11Ev
```

参考[https://bbs.archlinuxcn.org/viewtopic.php?id=12859](https://bbs.archlinuxcn.org/viewtopic.php?id=12859)

更新libime就好了`pacman -S libime`
