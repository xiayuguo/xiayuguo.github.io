---
title: Convert Int to String
date: 2020-03-11T19:40:30+08:00
draft: true
comments: false
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/convert-int-to-string/

## int/int64 转字符串
使用 [strconv.Itoa](https://golang.org/pkg/strconv/#Itoa "strconv.Itoa") 将 int 转换为十进制字符串。
```
s := strconv.Itoa(97) // s == "97"
```

> 警告：在普通转换中，该值将解释为 Unicode 码位，并且生成的字符串将包含该码位表示的字符，并以 UTF-8 编码。
> 
> `s := string(97) // s == "a"`

使用 [strconv.FormatInt](https://golang.org/pkg/strconv/#FormatInt "strconv.FormatInt") 以给定的基数格式化 int64。
