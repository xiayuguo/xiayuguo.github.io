---
title: Golang 中类型断言和类型切换（译文）
date: 2020-03-10T07:55:40+08:00
draft: false
comments: true
tags: 
- Golang
---

类型断言提供对接口具体值的访问。

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/type-assertion-switch/

![](http://oss.yuguo.im/blog/202003/thumbs-up.jpg)

## 类型断言
类型断言并不能真正将 `interface` 转换为另一种数据类型，但是可以访问 `interface` 的具体值，而这正是您所希望的。



类型断言 `x.(T)` 断言 `x` 中存储的具体值是 `T` 类型，并且 `x` 不为 `nil`
- 如果 `T` 不是 `interface`，则断言 `x` 的动态类型与 `T` 相同
- 如果 `T` 是 `interface`，则断言 `x` 的动态类型实现了 `T` 

```
var x interface{} = "foo"

var s string = x.(string)
fmt.Println(s)     // "foo"

s, ok := x.(string)
fmt.Println(s, ok) // "foo true"

n, ok := x.(int)
fmt.Println(n, ok) // "0 false"

n = x.(int)        // ILLEGAL
```
输出：
```
panic: interface conversion: interface {} is string, not int
```

## 类型切换
类型切换顺序执行多个类型声明，并使用匹配的类型运行第一种情况。
```
var x interface{} = "foo"

switch v := x.(type) {
case nil:
    fmt.Println("x is nil")            // here v has type interface{}
case int: 
    fmt.Println("x is", v)             // here v has type int
case bool, string:
    fmt.Println("x is bool or string") // here v has type interface{}
default:
    fmt.Println("type unknown")        // here v has type interface{}
}
```
输出：
```
x is bool or string
```

