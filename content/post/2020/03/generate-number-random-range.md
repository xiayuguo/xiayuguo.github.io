---
title: Golang 中生成随机数，字符和切片元素（译文）
date: 2020-03-13T08:19:52+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/generate-number-random-range/

![](http://oss.yuguo.im/blog/202003/dart.jpg)

## Go 伪随机数基础知识
使用包 [math/rand](https://golang.org/pkg/math/rand/ "math/rand") 中的 [rand.Seed](https://golang.org/pkg/math/rand/#Seed "rand.Seed") 和 [rand.Int63](https://golang.org/pkg/math/rand/#Int63 "rand.Int63") 函数生成 int64 类型的非负伪随机数：
```golang
rand.Seed(time.Now().UnixNano())
n := rand.Int63() // for example 4601851300195147788
```

同样，[rand.Float64](https://golang.org/pkg/math/rand/#Float64 "rand.Float64") 生成伪随机浮点数 x，其中 0 ≤ x < 1：
```golang
x := rand.Float64() // for example 0.49893371771268225
```

> 警告：如果不先调用 `rand.Seed`，则每次运行该程序时，都会得到相同的数字序列。

请参阅[随机数生成器中的种子是什么?](https://yourbasic.org/algorithms/random-number-generator-seed/ "随机数生成器中的种子是什么?"), 解释伪随机数生成器的原理。

### 几个随机源
`math/rand` 包中的函数全部使用单个随机源。

如果需要，您可以使用自己的源创建一个新的 `Rand` 类型的随机生成器，然后使用其方法生成随机数：
```golang
generator := rand.New(rand.NewSource(time.Now().UnixNano()))
n := generator.Int63()
x := generator.Float64()
```

## 给定范围内的整数和字符

### a 和 b 之间的数字
使用 [rand.Intn(m)](https://golang.org/pkg/math/rand/#Intn "rand.Intn")，它返回一个伪随机数 n，其中 0 ≤ n < m。
```golang
n := a + rand.Intn(b-a+1) // a ≤ n ≤ b
```

### 'a' 和 'z' 之间的字符
```golang
c := 'a' + rune(rand.Intn('z'-'a'+1)) // 'a' ≤ c ≤ 'z'
```

## 切片中的随机元素
要从任意集合生成字符，请从字符切片中选择一个随机索引：
```golang
chars := []rune("AB⌘")
c := chars[rand.Intn(len(chars))] // for example '⌘'
```

#### 延伸阅读
![Runic letters encoded in stone, Ballstorp 1900](http://oss.yuguo.im/blog/202003/rune-stone-ballstorp-1900.jpg)
[Runes 和字符编码（译文）](https://yuguo.im/post/2020/03/rune/ "Runes 和字符编码（译文）")
