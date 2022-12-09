---
title: Golang 中 int，int64 和字符串互转（译文）
date: 2020-03-11T19:40:30+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/convert-int-to-string/

![](http://oss.yuguo.im/blog/202003/numbers-1-2-3.jpg)

## int/int64 转字符串
使用 [strconv.Itoa](https://golang.org/pkg/strconv/#Itoa "strconv.Itoa") 将 int 转换为十进制字符串。
```
s := strconv.Itoa(97) // s == "97"
```

> 警告：在普通转换中，该值将解释为 Unicode 码位，并且生成的字符串将包含该码位表示的字符，并以 UTF-8 编码。
> 
> `s := string(97) // s == "a"`

使用 [strconv.FormatInt](https://golang.org/pkg/strconv/#FormatInt "strconv.FormatInt") 以给定的进制数（十进制、十六进制）格式化 int64。
```
var n int64 = 97
s := strconv.FormatInt(n, 10) // s == "97" (十进制)
```

```
var n int64 = 97
s := strconv.FormatInt(n, 16) // s == "61" (十六进制)
```

## 字符串转 int/int64
使用 [strconv.Atoi](https://golang.org/pkg/strconv/#Atoi "strconv.Atoi") 将十进制字符串解析为 int 。
```
s := "97"
if n, err := strconv.Atoi(s); err == nil {
    fmt.Println(n+1)
} else {
    fmt.Println(s, "is not an integer.")
}
// Output: 98
```

使用 [strconv.ParseInt](https://golang.org/pkg/strconv/#ParseInt "strconv.ParseInt") 解析一个十进制字符串（以10为基数），并检查其是否适合 int64。
```
s := "97"
n, err := strconv.ParseInt(s, 10, 64)
if err == nil {
    fmt.Printf("%d of type %T", n, n)
}
// Output: 97 of type int64
```

```
func ParseInt(s string, base int, bitSize int) (i int64, err error)
```
两个数字参数表示一个基数（0、2 到 36）和一个占位大小（0 到 64）。

如果第一个参数为 0，则字符串的前缀隐含基数：`0b`的基数为 2，`0` 或 `0o` 的基数是 8，`0x` 的基数为 16，否则为 10。

第二个参数指定结果必须适合的整数类型。占位大小 0、8、16、32 和 64 对应于 int，int8，int16，int32 和 int64。

## int 和 int64 互转
一个 int 的大小是基于操作系统特定实现的，它可能是 32 位，也可能是 64 位，因此从 int 转换为 int64 时，您不会丢失任何信息。

```
var n int = 97
m := int64(n) // safe
```

> 但是，当转换为较短的整数类型时，该值将被**截断**以适合结果类型的大小。

```
var m int64 = 2 << 32
n := int(m)    // truncated on machines with 32-bit ints
fmt.Println(n) // either 0 or 4,294,967,296
```
- 有关计算 int 大小的代码，请参见 [Maximum value of an int](https://yourbasic.org/golang/max-min-int-uint/ "")。
- 最佳实践 [Pick the right one: int vs. int64](https://yourbasic.org/golang/int-vs-int64/ "Pick the right one: int vs. int64")。

## 通用格式化（宽度，缩进，符号）
[fmt.Sprintf](https://golang.org/pkg/fmt/#Sprintf "fmt.Sprintf") 函数是将数据转换为字符串的好用工具：
```
s := fmt.Sprintf("%+8d", 97)
// s == "     +97" (width 8, right justify, always show sign)
```