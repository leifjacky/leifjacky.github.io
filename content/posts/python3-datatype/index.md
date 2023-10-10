---
title: "python3 基本数据类型"
date: 2023-04-08T00:10:00+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

```python
# python3 基本数据类型

counter = 100  # 整型变量
miles = 1000.0  # 浮点型变量
name = "runoob"  # 字符串

print(counter)
print(miles)
print(name)

# 多变量赋值
a = b = c = 1
x, y, z = 1, 2, "runoob"

# 数字类型int, float, bool, complex
a, b, c, d = 20, 5.5, True, 4 + 3j
print(type(a), type(b), type(c), type(d))
print(isinstance(a, int))


# isinstance 和 type 的区别
class A:
    pass


class B(A):
    pass


print(isinstance(A(), A))  # returns True
print(type(A()) == A)  # returns True
print(isinstance(B(), A))  # returns True
print(type(B()) == A)  # returns False
# type()不会认为子类是一种父类类型。
# isinstance()会认为子类是一种父类类型。

del a
del x, y

# 数值运算
print(5 + 4, 4.3 - 2, 3 * 7, 2 / 4, 2 // 4, 17 % 3, 2 ** 5)

# 字符串
st1 = 'Runoob'

print(st1)  # 输出字符串
print(st1[0:-1])  # 输出第一个到倒数第二个的所有字符
print(st1[0])  # 输出字符串第一个字符
print(st1[2:5])  # 输出从第三个开始到第五个的字符
print(st1[2:])  # 输出从第三个开始的后的所有字符
print(st1 * 2)  # 输出字符串两次
print(st1 + "TEST")  # 连接字符串
# 字符串元素不能改变

# Python 使用反斜杠(\)转义特殊字符，如果你不想让反斜杠发生转义，可以在字符串前面添加一个 r，表示原始字符串：
print('Ru\noob')
print(r'Ru\noob')

# Tuple元组
tuple1 = ('abcd', 786, 2.23, 'runoob', 70.2)
tinytuple = (123, 'runoob')

print(tuple1)  # 输出完整元组
print(tuple1[0])  # 输出元组的第一个元素
print(tuple1[1:3])  # 输出从第二个元素开始到第三个元素
print(tuple1[2:])  # 输出从第三个元素开始的所有元素
print(tinytuple * 2)  # 输出两次元组
print(tuple1 + tinytuple)  # 连接元组

# List列表
list1 = ['abcd', 786, 2.23, 'runoob', 70.2]
tinylist = [123, 'runoob']

print(list1)  # 输出完整列表
print(list1[0])  # 输出列表第一个元素
print(list1[1:3])  # 从第二个开始输出到第三个元素
print(list1[2:])  # 输出从第三个元素开始的所有元素
print(tinylist * 2)  # 输出两次列表
print(list1 + tinylist)  # 连接列表
# 字符串看作一种特殊的元组，修改元组元素的操作是非法的

# 列表元素可以改变
a = [1, 2, 3, 4, 5, 6]
a[0] = 9
a[2:5] = [13, 14, 15]
print(a)
a[2:5] = []
print(a)
# string、list和tuple都属于sequence（序列）

# Set集合。集合（set）是一个无序不重复元素的序列。基本功能是进行成员关系测试和删除重复元素。
a, b = 1, 2
parame = {a, b}
x = [1, 2, 3, 4, 5, 6, 1, 2]
s2 = set(x)
print(s2)

student = {'Tom', 'Jim', 'Mary', 'Tom', 'Jack', 'Rose'}

print(student)  # 输出集合，重复的元素被自动去掉

# 成员测试
if ('Rose' in student):
    print('Rose 在集合中')
else:
    print('Rose 不在集合中')

# set可以进行集合运算
a = set('abracadabra')
b = set('alacazam')

print(a)
print(a - b)  # a和b的差集
print(a | b)  # a和b的并集
print(a & b)  # a和b的交集
print(a ^ b)  # a和b中不同时存在的元素

# Dictionary字典
d1 = {}
d1['one'] = "1 - 菜鸟教程"
d1[2] = "2 - 菜鸟工具"

tinydict = {'name': 'runoob', 'code': 1, 'site': 'www.runoob.com'}

print(d1['one'])  # 输出键为 'one' 的值
print(d1[2])  # 输出键为 2 的值
print(tinydict)  # 输出完整的字典
print(tinydict.keys())  # 输出所有键
print(tinydict.values())  # 输出所有值

d2 = dict([('Runoob', 1), ('Google', 2), ('Taobao', 3)])
print(d2)

d3 = {x: x ** 2 for x in (2, 4, 6)}
print(d3)

Runoob = 'Runoob'
Google = 'Google'
Taobao = 'Taobao'
d4 = dict(Runoob=1, Google=2, Taobao=3)
print(d4)

# 类型转换
print(int("ff", 16))  # 转换整数
print(int("0xff", 16))  # 转换整数
print(16.5 // 1)

print(float(123))  # 转换浮点数
print(float("123"))  # 转换浮点数

print(complex(1.2, 123))

print(str(1), "type", type(str(1)))  # 转换为字符串
print(str(1.0), "type", type(str(1.0)))
print(str(complex(1.2, 123)), "type", type(str(complex(1.2, 123))))

print(repr(1))  # 转换为表达式字符串
print(repr(1.0))
print(repr(complex(1.2, 123)))

print(tuple([1, 2, 3, 4]), type(tuple([1, 2, 3, 4])))  # 转换为元组
print(tuple({1, 2, 3, 4}), type(tuple({1, 2, 3, 4})))
print(tuple((1, 2, 3, 4)), type(tuple((1, 2, 3, 4))))
print((1, 2, 3, 4), type((1, 2, 3, 4)))

print(list([1, 2, 3, 4]), type(list([1, 2, 3, 4])))  # 转换为列表
print(list({1, 2, 3, 4, 1, 2}), type(list({1, 2, 3, 4, 1, 2})))
print(list((1, 2, 3, 4, 1, 2)), type(list((1, 2, 3, 4, 1, 2))))
print([1, 2, 3, 4, 1, 2], type([1, 2, 3, 4, 1, 2]))

# set(s)转换为可变集合, frozenset(s)转换为不可变集合
print(set([1, 2, 3, 4]), type(set([1, 2, 3, 4])))
print(set({1, 2, 3, 4, 1, 2}), type(set({1, 2, 3, 4, 1, 2})))
print(set((1, 2, 3, 4, 1, 2)), type(set((1, 2, 3, 4, 1, 2))))
print({1, 2, 3, 4, 1, 2}, type({1, 2, 3, 4, 1, 2}))

print(dict(((1, 2), (2, 3), (3, 4))))  # 创建一个字典。d 必须是一个序列 (key,value)元组。

print(chr(97))  # 转换一个整数为一个字符，可转换为ASCII
print(chr(0x61))  # 转换一个整数为一个字符，可转换为ASCII

print(ord('A'))  # 转换一个字符为整数值

print(hex(12))  # 整数转16进制字符串
print(oct(1024))  # 整数转8进制字符串

```

参考资料：

1. Runoob 教程：[http://www.runoob.com/python3/python3-data-type.html](http://www.runoob.com/python3/python3-data-type.html "http://www.runoob.com/python3/python3-data-type.html")
