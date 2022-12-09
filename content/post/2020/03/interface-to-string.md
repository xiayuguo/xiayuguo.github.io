---
title: Golang 中将接口转换为字符串（译文）
date: 2020-03-15T20:33:15+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/interface-to-string/

使用 [fmt.Sprintf](https://golang.org/pkg/fmt/#Sprintf "fmt.Sprintf") 将[interface value](https://yourbasic.org/golang/interfaces-explained/ "interface value")转换为字符串。

```
var x interface{} = "abc"
str := fmt.Sprintf("%v", x)
```

实际上，可以使用相同的技术来获取任何数据结构的字符串表示形式。
```
var x interface{} = []int{1, 2, 3}
str := fmt.Sprintf("%v", x)
fmt.Println(str) // "[1 2 3]"
```

## Fmt 备忘单
![](http://oss.yuguo.im/blog/202003/format-thumb.jpg)

[顶级 fmt 格式化技巧](https://yourbasic.org/golang/fmt-printf-reference-cheat-sheet/ "顶级 fmt 格式化技巧")