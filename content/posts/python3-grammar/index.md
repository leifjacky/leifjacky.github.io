---
title: "python3基础语法"
date: 2023-04-08T00:08:52+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

```python
# 基础语法
import keyword

print(keyword.kwlist)

# 注释
print("Hello, Python!")

'''
多行注释1
多行注释1
'''

"""
多行注释2
多行注释2
"""

# 缩进
flag = 0
if flag == 0:
    print("Answer")
    print("True")
else:
    print("Answer")
    print("False")

# 多行
a = b = c = 1
total = a + \
        b + \
        c

x = "a"
y = "b"
# 换行输出
print(x)
print(y)

print('---------')
# 不换行输出
print(x, end=" ")
print(y, end=" ")
print()

# import与from...import
import sys

print('================Python import mode==========================');
print('命令行参数为:')
for i in sys.argv:
    print(i)
print('\n python 路径为', sys.path)

from sys import argv, path  # 导入特定的成员

print('================python from import===================================')
print('path:', path)  # 因为已经导入path成员，所以此处引用时不需要加sys.path
```

参考资料：

1. Runoob 教程：[http://www.runoob.com/python3/python3-basic-syntax.html](http://www.runoob.com/python3/python3-basic-syntax.html "http://www.runoob.com/python3/python3-basic-syntax.html")
