---
title: Golang 错误处理最佳实践（译文）
date: 2020-03-19T08:49:59+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/errors-explained/

![](http://oss.yuguo.im/blog/202003/errors.png)

Go 具有两种不同的错误处理机制：

- 大多数函数返回 errors；
- 只有真正无法恢复的条件（例如超出范围的索引）才会产生运行时异常，我们称之为 panic

Go 的多值返回值使您可以轻松地在正常返回值旁边返回详细的错误消息。按照惯例，此类消息具有类型 `error`，这是一个简单的内置 interface：
```
type error interface {
    Error() string
}
```

## 错误处理示例
`os.Open` 函数无法打开文件时返回一个非 nil 错误值。
```
func Open(name string) (file *File, err error)
```

以下代码使用 `os.Open` 打开文件。如果发生错误，它将调用 `log.Fatal` 打印错误消息并停止。
```
f, err := os.Open("filename.ext")
if err != nil {
    log.Fatal(err)
}
// do something with the open *File f
```

## 自定义错误
要创建简单的仅字符串 `error`，可以使用 [errors.New](https://golang.org/pkg/errors/#New "errors.New")：
```
err := errors.New("Houston, we have a problem")
```

`error` 接口仅需要一个 `Error` 方法，但是特定的 `error` 实现通常具有其他方法，从而允许调用方检查错误的详细信息。

#### 学到更多
![](http://oss.yuguo.im/blog/202003/bug-small.png)

有关更多示例，请参见 [3种创建错误的简单方法](https://yuguo.im/post/2020/03/create-error/ "3 simple ways to create an error")。

## Panic
Panics 类似于 C++ 和 Java 异常，但仅适用于运行时错误，例如跟随一个 nil 指针或试图对数组访问超出范围的索引。

#### 延伸阅读
![](http://oss.yuguo.im/blog/202003/panic-thumb.jpg)

有关如何从 panics 中恢复和测试 panics 的教程，请参见 [从 panic 中恢复](https://yuguo.im/post/2020/03/recover-from-panic/ "Recover from a panic")。