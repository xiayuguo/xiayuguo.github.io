---
title: Panic,堆栈跟踪以及如何恢复【最佳实践】（译文）
date: 2020-03-19T08:47:34+08:00
draft: false
comments: true
tags: 
- Golang
---

> 作者：Stefan Nilsson
> 
> 原文网址：https://yourbasic.org/golang/recover-from-panic/

![](http://oss.yuguo.im/blog/202003/panic-thumb.jpg)

## Panic 是 Go 中的一个异常

Panics 类似于 C++ 和 Java 异常，但仅适用于运行时错误，例如跟随一个 nil 指针或试图对数组访问超出范围的索引。为了表示诸如文件结束之类的事件，Go 程序使用内置 `error` 类型。有关错误的更多信息，请参见 [错误处理最佳实践]() 和 [3种创建错误的简单方法]()。

Panic 停止 goroutine 的正常执行

- 程序出现 panic 时，它将立即开始展开调用堆栈。
- 一直持续到程序崩溃并打印堆栈跟踪,
- 或直到调用内置的恢复功能。

panic 是由运行时错误或对内置函数 `panic` 的显式调用引起的。

## 堆栈跟踪记录
堆栈跟踪记录 —— 所有活动堆栈帧的报告 —— 通常在 panic 发生时将其打印到控制台。堆栈跟踪对于调试非常有用：

- 您不仅可以看到错误发生的地方，
- 而且可以看到程序是如何到达这个地方的。

### 解释堆栈跟踪

这是一个堆栈跟踪的示例：

```
goroutine 11 [running]:
testing.tRunner.func1(0xc420092690)
    /usr/local/go/src/testing/testing.go:711 +0x2d2
panic(0x53f820, 0x594da0)
    /usr/local/go/src/runtime/panic.go:491 +0x283
github.com/yourbasic/bit.(*Set).Max(0xc42000a940, 0x0)
    ../src/github.com/bit/set_math_bits.go:137 +0x89
github.com/yourbasic/bit.TestMax(0xc420092690)
    ../src/github.com/bit/set_test.go:165 +0x337
testing.tRunner(0xc420092690, 0x57f5e8)
    /usr/local/go/src/testing/testing.go:746 +0xd0
created by testing.(*T).Run
    /usr/local/go/src/testing/testing.go:789 +0x2de
```
可以从下至上阅读：

- `testing.(*T).Run` 调用了 `testing.tRunner`,
- `testing.tRunner` 调用了 `bit.TestMax`,
- `bit.TestMax` 调用了 `bit.(*Set).Max`,
- `bit.(*Set).Max` 调用了 `panic`,
- `panic` 调用了 `testing.tRunner.func1`

缩进的行显示了调用该函数的源文件和行号。十六进制数字表示参数值，包括指针和内部数据结构的值。[Go 中的堆栈跟踪](https://www.goinggo.net/2015/01/stack-traces-in-go.html "Stack Traces in Go") 具有更多详细信息。

### 打印并记录堆栈跟踪

要打印当前 goroutine 的堆栈跟踪，请使用包 [runtime/debug](https://golang.org/pkg/runtime/debug/ "runtime/debug") 中的[debug.PrintStack](https://golang.org/pkg/runtime/debug/#PrintStack "debug.PrintStack")。

您还可以通过调用 [runtime.Stack](https://golang.org/pkg/runtime/#Stack "runtime.Stack") 以编程方式检查当前的堆栈跟踪

### 详细程度

变量 [GOTRACEBACK](https://golang.org/pkg/runtime/#hdr-Environment_Variables "GOTRACEBACK") 控制 Go 程序失败时生成的输出量。

- `GOTRACEBACK = none` 完全忽略 goroutine 堆栈跟踪。
- `GOTRACEBACK = single`（默认）为当前goroutine打印堆栈跟踪, 从而消除运行时系统内部的功能。如果没有当前goroutine或故障是运行时内部的，则故障会打印所有goroutine的堆栈跟踪。
- `GOTRACEBACK = all` 为所有用户创建的goroutine添加堆栈跟踪。
- `GOTRACEBACK = system` 与其他系统一样，但是为运行时函数添加了堆栈框架，并显示了运行时在内部创建的 goroutine。

## 恢复和捕获 Panic
![](http://oss.yuguo.im//blog/202003/extinguish-fire.jpg)

内置的 recover 函数可用于重新获得对异常程序的控制并恢复正常执行。

- 调用 recover 将停止展开并返回传递给 panic 的参数。
- 如果 goroutine 没有异常，则恢复将返回 nil。

因为展开时运行的唯一代码是在 [defer](https://yourbasic.org/golang/defer/ "defer") 函数内部，所以 recover 仅在此类函数内部有用。

### Panic 处理程序示例
```
func main() {
	n := foo()
	fmt.Println("main received", n)
}

func foo() int {
	defer func() {
		if err := recover(); err != nil {
			fmt.Println(err)
		}
	}()
	m := 1
	panic("foo: fail")
	m = 2
	return m
}
```

```
foo: fail
main received 0
```

由于 panic 是在 foo 返回值之前发生的，因此 n 仍然具有其初始零值。

### 返回值

要在发生 panic 时返回值，必须使用命名返回值。
```
func main() {
	n := foo()
	fmt.Println("main received", n)
}

func foo() (m int) {
	defer func() {
		if err := recover(); err != nil {
			fmt.Println(err)
			m = 2
		}
	}()
	m = 1
	panic("foo: fail")
	m = 3
	return m
}
```

```
foo: fail
main received 2
```

## 测试 Panic（实用功能）
![](http://oss.yuguo.im//blog/202003/panic-button.jpg)

在此示例中，我们使用反射来检查接口变量列表是否具有与给定函数的参数相对应的类型。如果是这样，我们使用这些参数调用该函数以检查是否有 panic。

```
// Panics tells if function f panics with parameters p.
func Panics(f interface{}, p ...interface{}) bool {
	fv := reflect.ValueOf(f)
	ft := reflect.TypeOf(f)
	if ft.NumIn() != len(p) {
		panic("wrong argument count")
	}
	pv := make([]reflect.Value, len(p))
	for i, v := range p {
		if reflect.TypeOf(v) != ft.In(i) {
			panic("wrong argument type")
		}
		pv[i] = reflect.ValueOf(v)
	}
	return call(fv, pv)
}

func call(fv reflect.Value, pv []reflect.Value) (b bool) {
	defer func() {
		if err := recover(); err != nil {
			b = true
		}
	}()
	fv.Call(pv)
	return
}
```