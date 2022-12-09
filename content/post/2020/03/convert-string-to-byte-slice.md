---
title: Golang 中字节切片/数组和字符串互转（译文）
date: 2020-03-11T18:20:48+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/convert-string-to-byte-slice/

## 基本认知
在字符串和字节切片（数组）之间进行转换时，会得到一个全新的切片，其中包含与字符串相同的字节，反之亦然。
- 转换不会修改数据
- 唯一的区别是字符串是不可变的，而字节片可以修改

> 如果需要操纵字符串的 rune ，则可能需要将字符串转换为 rune 切片，更多细节请看这篇文章 [Golang 中 runes 和 字符串互转（译文）](https://yuguo.im/post/2020/03/convert-string-to-rune-slice/)

## 将字符串转为字节切片
将字符串转换为字节切片时，会得到一个新的切片，其中包含与字符串相同的字节。
```
b := []byte("ABC€")
fmt.Println(b) // [65 66 67 226 130 172]
```
请注意，字符 € 是使用 3 个字节的 UTF-8 编码的。有关 Unicode 码位的 UTF-8 编码的更多信息，请参阅关于 [rune](https://yuguo.im/post/2020/03/rune/ "rune") 的这篇文章。

## 将字节切片转为字符串
当您将字节的一部分转换为字符串时，您将获得一个新的字符串，其中包含与该片段相同的字节。
```
s := string([]byte{65, 66, 67, 226, 130, 172})
fmt.Println(s) // ABC€
```

## 性能
这些转换将创建一个新的切片或字符串，因此时间复杂度与处理的字节数成正比。





