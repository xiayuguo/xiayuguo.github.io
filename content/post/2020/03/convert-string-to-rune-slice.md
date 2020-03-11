---
title: Golang 中 runes 和 字符串互转（译文）
date: 2020-03-11T08:26:57+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/convert-string-to-rune-slice/


## Convert string to runes
- 将字符串转换为 `rune` 切片时，会得到一个新的切片，其中包含字符串的 Unicode 码位( runes )
- 对于无效的 UTF-8 序列，每个无效字节的 `rune` 值将为 `0xFFFD`。

```
r := []rune("ABC€")
fmt.Println(r)        // [65 66 67 8364]
fmt.Printf("%U\n", r) // [U+0041 U+0042 U+0043 U+20AC]
```

> 您还可以使用 `range` 循环访问字符串的码位。

## Convert runes to string
- 当您将一小段 runes 转换为字符串时，会得到一个新字符串，该字符串是将 runes 转换为 UTF-8 编码字符串的串联。
- 有效 Unicode 码位范围之外的值将转换为 Unicode 替换字符`\uFFFD`, 显示为�。

```
s := string([]rune{'\u0041', '\u0042', '\u0043', '\u20AC', -1})
fmt.Println(s) // ABC€�
```

## 性能
这些转换将创建一个新的切片或字符串，因此时间复杂度与处理的字节数成正比。
