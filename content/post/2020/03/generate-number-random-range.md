---
title: 生成随机数，字符和切片元素（译文）
date: 2020-03-13T09:19:52+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/generate-number-random-range/

## Go 伪随机数基础知识
使用软件包 [math/rand](https://golang.org/pkg/math/rand/ "math/rand") 中的 [rand.Seed](https://golang.org/pkg/math/rand/#Seed "rand.Seed") 和 [rand.Int63](https://golang.org/pkg/math/rand/#Int63 "rand.Int63") 函数生成 int64 类型的非负伪随机数

