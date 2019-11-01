---
title: Defer, Panic and Recover
date: 2019-02-13T12:14:17+08:00
draft: false
comments: true
tags: 
- Golang
---

> 翻译自 golang 的官方博客 [Defer, Panic, and Recover](https://blog.golang.org/defer-panic-and-recover)

Go 具有控制流程的常用机制：if，for，switch，goto。它还有 go 语句在单独的 goroutine 中运行代码。在这里，我想讨论一些不太常见的问题：**defer**, **panic**, **recover**。

**defer** 语句将函数调用推送到列表中。在周围函数返回后执行已保存调用的列表。延迟通常用于简化执行各种清理操作的功能。

举个例子，让我们看一个打开两个文件并将一个文件的内容复制到另一个文件的函数：
```go
func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }

    written, err = io.Copy(dst, src)
    dst.Close()
    src.Close()
    return
}
```

这可以正常运行，但有一个 bug。 如果对 os.Create 的调用失败，该函数将返回而不关闭源文件。 这可以通过在第二个 return 语句之前调用 src.Close 来轻松解决，但如果函数更复杂，则问题可能不会那么容易被注意到并解决。 通过引入 **defer** 语句，我们可以确保文件始终关闭：
```go
func CopyFile(dstName, srcName string) (written int64, err error) {
    src, err := os.Open(srcName)
    if err != nil {
        return
    }
    defer src.Close()

    dst, err := os.Create(dstName)
    if err != nil {
        return
    }
    defer dst.Close()

    return io.Copy(dst, src)
}
```

**Defer** 语句允许我们考虑在打开它之后立即关闭每个文件，保证无论函数中的返回语句数量如何，文件都将被关闭。

**Defer** 语句的行为是直截了当且可预测的。有三个简单的规则：

1.在评估 defer 语句时，将评估 defer 函数的参数。 

在此示例中，在延迟Println调用时计算表达式“i”。 函数返回后，延迟调用将打印“0”。
```go
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
```

2.在周围函数返回后，以后进先出顺序执行 defer 函数调用。

此函数打印“3210”：
```go
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}
```

3.defer 函数可以读取并分配给返回函数的命名返回值。

在此示例中，defer 函数在周围函数返回后递增返回值i。因此，此函数返回2：
```go
func c() (i int) {
    defer func() { i++ }()
    return 1
}
```
这样便于修改函数的错误返回值;我们很快就会看到一个例子。

**panic** 是一种内置函数，可以阻止普通的控制流并开始触发 Panic 状态。 当函数 F 调用 panic 时，F 的执行停止，F 中的任何 defer 函数都正常执行，然后 F 返回其调用方。 对于调用方，F 表现得像是对 panic 的调用。 进程继续向上移动，直到当前 goroutine 中的所有函数都返回，此时程序崩溃。 可以通过直接调用 panic 来触发 Panic 状态。 它们也可能由运行时错误引起，例如数组越界访问。

**recover** 是一个内置函数，可以重新控制 Panic 状态的 goroutine。 恢复仅在 defer 函数内有用。 在正常执行期间，对 recover 的调用将返回 nil 并且没有其他作用。 如果当前 goroutine 处于 Panic 状态，则对恢复的调用将捕获给予 panic 的值并恢复正常执行。

这是一个示例程序，演示了 panic 和 defer 的机制：
```go
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}
```

函数 g 接受整型参数 i，如果 i 大于 3 则发生触发 Panic 状态，否则它使用参数 i + 1 调用自身。函数 f 推迟调用 recover 的函数并打印恢复的值（如果它是非nil）。 在阅读之前尝试描绘该程序的输出可能是什么。

该程序将输出：
```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
Recovered in f 4
Returned normally from f.
```

如果我们从 f 中删除 defer 函数，则 Panic 状态不会被恢复并到达 goroutine 调用堆栈的顶部，从而终止程序。此修改后的程序将输出：
```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
panic: 4
 
panic PC=0x2a9cd8
[stack trace omitted]
```

有关 panic 和 recover 的真实示例，请参阅 Go 标准库中的 json 包。 它使用一组递归函数对 JSON 编码的数据进行解码。 当遇到格式错误的 JSON 时，解析器调用 panic 将堆栈展开到顶级函数调用，该函数调用从 Panic 中恢复并返回适当的错误值（请参阅 decode.go 中 decodeState 类型的 'error' 和 'unmarshal' 方法）。

Go 库中的约定是即使包在内部使用 panic，其外部 API 仍然会显示明确的错误返回值。

**defer** 的其他用法（在关闭文件以外, 前面给出的示例）包括

释放互斥锁：
```go
mu.Lock()
defer mu.Unlock()
```

打印页脚；
```go
printHeader()
defer printFooter()
```
等更多应用。

总之，defer 语句（有或没有恐慌和恢复）为控制流提供了一种不寻常且强大的机制。 它可用于模拟由其他编程语言中的专用结构实现的许多功能。 试试看。 
