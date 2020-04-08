---
title: 生成随机数，字符和切片元素（译文）
date: 2020-03-13T09:19:52+08:00
draft: true
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/generate-number-random-range/

## Go 伪随机数基础知识
使用包 [math/rand](https://golang.org/pkg/math/rand/ "math/rand") 中的 [rand.Seed](https://golang.org/pkg/math/rand/#Seed "rand.Seed") 和 [rand.Int63](https://golang.org/pkg/math/rand/#Int63 "rand.Int63") 函数生成 int64 类型的非负伪随机数：
```
rand.Seed(time.Now().UnixNano())
n := rand.Int63() // for example 4601851300195147788
```

同样，[rand.Float64](https://golang.org/pkg/math/rand/#Float64 "rand.Float64") 生成伪随机浮点数 x，其中 0 ≤ x <1：
```
x := rand.Float64() // for example 0.49893371771268225
```

> 警告：如果不先调用 `rand.Seed`，则每次运行该程序时，都会得到相同的数字序列。

请参阅随机数生成器中的种子是什么？解释伪随机数生成器。

