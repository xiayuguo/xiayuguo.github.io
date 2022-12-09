---
title: Golang 中 3 种简单的方法来创建错误（译文）
date: 2020-03-19T08:28:05+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/create-error/

![](http://oss.yuguo.im/blog/202003/bug-small.png)

## 基于字符串的错误
标准库提供了两个现成的选项。
```
// simple string-based error
err1 := errors.New("math: square root of negative number")

// with formatting
err2 := fmt.Errorf("math: square root of negative number %g", x)
```

## 带有数据的自定义错误
要定义自定义错误类型，您必须满足预先声明的 `error` 接口。

```
type error interface {
    Error() string
}
```

这里有两个例子。

```
type SyntaxError struct {
    Line int
    Col  int
}

func (e *SyntaxError) Error() string {
    return fmt.Sprintf("%d:%d: syntax error", e.Line, e.Col)
}
```

```
type InternalError struct {
    Path string
}

func (e *InternalError) Error() string {
    return fmt.Sprintf("parse %v: internal error", e.Path)
}
```

如果 `Foo` 是可以返回 `SyntaxError` 或 `InternalError` 的函数，则可以处理两种情况。

```
if err := Foo(); err != nil {
    switch e := err.(type) {
    case *SyntaxError:
        // Do something interesting with e.Line and e.Col.
    case *InternalError:
        // Abort and file an issue.
    default:
        log.Println(e)
    }
}
```

