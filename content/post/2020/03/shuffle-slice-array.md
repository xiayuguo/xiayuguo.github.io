---
title: Golang 中对切片或数组洗牌（译文）
date: 2020-04-10T16:08:23+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/shuffle-slice-array/

![](http://oss.yuguo.im/blog/202003/riffle-shuffle.jpg)

包 [math/rand](https://golang.org/pkg/math/rand/ "math/rand") 中的 [rand.Shuffle](https://golang.org/pkg/math/rand/#Shuffle "rand.Shuffle") 函数使用给定的交换函数对输入序列进行混洗。

```golang
a := []int{1, 2, 3, 4, 5, 6, 7, 8}
rand.Seed(time.Now().UnixNano())
rand.Shuffle(len(a), func(i, j int) { a[i], a[j] = a[j], a[i] })
```

输出：
```golang
[5 8 6 4 3 7 2 1]
```
> 警告：如果不调用 `rand.Seed`，则每次运行程序时，您都会获得相同的伪随机数序列。

#### 延伸阅读
![](http://oss.yuguo.im/blog/202003/dart.jpg)
[Golang 中生成随机数，字符和切片元素（译文）](https://yuguo.im/post/2020/03/generate-number-random-range "Golang 中生成随机数，字符和切片元素（译文）")

#### Go 1.10 之前
在包 `math/rand` 中使用 [rand.Seed](https://golang.org/pkg/math/rand/#Seed "rand.Seed") 和 [rand.Intn](https://golang.org/pkg/math/rand/#Intn "rand.Intn") 函数。
```golang
a := []int{1, 2, 3, 4, 5, 6, 7, 8}
rand.Seed(time.Now().UnixNano())
for i := len(a) - 1; i > 0; i-- { // Fisher–Yates shuffle
    j := rand.Intn(i + 1)
    a[i], a[j] = a[j], a[i]
}
```