---
title: Runes 和字符编码（译文）
date: 2020-03-10T19:00:17+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/rune

![Runic letters encoded in stone, Ballstorp 1900](http://oss.yuguo.im/blog/202003/rune-stone-ballstorp-1900.jpg)

## 字符，ASCII 和 Unicode
`rune` 类型是 `int32` 的别名，用于强调其代表的是码位（code point），而不是整数。

**ASCII** 定义 128 个字符，由码位 0 – 127 标识。它涵盖英文字母，拉丁数字和其他一些字符。

**Unicode** 是 ASCII 的超集，它定义了 1,114,112 个代码点的代码空间。 Unicode 版本 10.0 涵盖 139 个现代和历史文本集（包括符文字母，但不包括 Klingon ）以及多个符号集。

## 字符串和 UTF-8 编码
> 字符串是字节序列，而不是符文。

但是，字符串通常包含以 UTF-8 编码的 Unicode 文本，该文本使用一到四个字节对所有 Unicode 码位进行编码。（ASCII 字符使用一个字节编码，而其他码位则使用更多字节。）

由于 Go 源代码本身被编码为 UTF-8，因此字符串文字将自动获得此编码。

例如，在字符串`"café"`中，字符`é`（码位 233）使用两个字节编码，而 ASCII 字符 c，a 和 f（码位 99、97 和 102）仅使用一个字节：
```
fmt.Println([]byte("café")) // [99 97 102 195 169]
fmt.Println([]rune("café")) // [99 97 102 233]
```




