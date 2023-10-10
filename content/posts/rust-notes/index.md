---
title: "Rust Notes"
date: 2023-04-08T00:22:20+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

### let 声明变量，mut 可变变量

```rust
let mut x: i8 = 1;
let y = 2;
```

### 函数声明

```rust
  fn calc(wf: f64) -> f64 {
      wf + 127.3
  }

  fn fibonaci(mut n: i8) -> i64 {
      if n == 1 {
          return 1;
      } else if n == 2 {
          return 2;
      }
      let mut last = 2;
      let mut ret = 3;
      while n > 3 {
          let temp = ret;
          ret += last;
          last = temp;
          n -= 1;
      }
      return ret;
  }
```

#### Rust 中的整型

| 长度    | 有符号 | 无符号 |
| ------- | ------ | ------ |
| 8-bit   | i8     | u8     |
| 16-bit  | i16    | u16    |
| 32-bit  | i32    | u32    |
| 64-bit  | i64    | u64    |
| 128-bit | i128   | u128   |
| arch    | isize  | usize  |

#### Rust 中的整型字面值

| 数字字面值                   | 例子        |
| ---------------------------- | ----------- |
| Decimal (十进制)             | 98_222      |
| Hex (十六进制)               | 0xff        |
| Octal (八进制)               | 0o77        |
| Binary (二进制)              | 0b1111_0000 |
| Byte (单字节字符)(仅限于 u8) | b'A'        |

#### 元组类型

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);

let (x, y, z) = tup;

println!("The value of y is: {}", y);

let x: (i32, f64, u8) = (500, 6.4, 1);

let five_hundred = x.0;

let six_point_four = x.1;

let one = x.2;
```

#### 数组类型

```rust
let a = [1, 2, 3, 4, 5];

let a: [i32; 5] = [1, 2, 3, 4, 5];

let first = a[0];
let second = a[1];
```

#### 循环

Rust 有三种循环：loop、while 和 for。我们每一个都试试。
