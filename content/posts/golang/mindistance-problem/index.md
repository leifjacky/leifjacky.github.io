---
title: "最短编辑距离 Golang"
date: 2023-04-08T00:12:00+08:00
draft: false
categories: [_Misc]
tags: []
card: false
weight: 0
---

### 问题

给定两个字符串 a 和 b，只允许以下三种操作：

1. 插入一个字符；
2. 删除一个字符；
3. 替换一个字符。

求：把 a 转换成 b 的最小操作次数，也就是所谓的最小编辑距离。

> 举例： "xy" => "xz"，只需要把 y 替换成 z，因此，最小编辑距离为 1。
> "xyz" => "xy"，只需要删除 z ，因此，最小编辑距离为 1。

### 思路

本题与最长公共子序列有相似性。插入和删除其实是逆操作，操作与 ret[i-1][j]与 ret[i][j-1]有关，替换则是与 ret[i-1][j-1]有关。

### 代码

```go
package main

import "fmt"

const (
	maxDistance = 0x3fffffff
)

type Row []int

func MinDistance(str1, str2 string) int {
	r1, r2 := []rune(str1), []rune(str2)
	return minDistance(r1, r2)
}

func minDistance(r1, r2 []rune) int {
	l1, l2 := len(r1), len(r2)
	a := genArray(l1, l2)
	for i := 1; i <= l1; i++ {
		for j := 1; j <= l2; j++ {
			if r1[i-1] == r2[j-1] {		// 无需进行操作
				a[i][j] = a[i-1][j-1]
			} else {
				a[i][j] = min(a[i-1][j-1], min(a[i-1][j], a[i][j-1])) + 1  // 从三种操作中选择最优
			}
		}
	}
	return a[l1][l2]
}

func genArray(l1, l2 int) []Row {
	a := make([]Row, l1+1)
	for i := 0; i <= l1; i++ {
		a[i] = make(Row, l2+1)
		for j := 0; j <= l2; j++ {
			a[i][j] = maxDistance
			if i == 0 || j == 0 {
				a[i][j] = max(i, j)
			}
		}
	}
	return a
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}

func main() {
	a := "spiderman"
	b := "superman"

	fmt.Printf(`The min distance between "%s" and "%s" is: %d\n`, a, b, MinDistance(a, b))	// 结果为3
}

```
