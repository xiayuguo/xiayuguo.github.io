---
title: Interface to String
date: 2020-03-15T20:33:15+08:00
draft: true
comments: false
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/interface-to-string/

使用 [fmt.Sprintf](https://golang.org/pkg/fmt/#Sprintf "fmt.Sprintf") 将接口值转换为字符串。

```
var x interface{} = "abc"
str := fmt.Sprintf("%v", x)
```
